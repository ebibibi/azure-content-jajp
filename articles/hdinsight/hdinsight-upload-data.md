<properties
	pageTitle="HDInsight での Hadoop ジョブ用データのアップロード | Microsoft Azure"
	description="Azure CLI、Azure Storage エクスプローラー、Azure PowerShell、Hadoop コマンド ライン、または Sqoop を使用して、Hadoop ジョブ用データを HDInsight にアップロードする方法と HDInsight の Hadoop ジョブ用データにアクセスする方法について説明します。"
	services="hdinsight,storage"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="jgao"/>



#HDInsight での Hadoop ジョブ用データのアップロード

Azure の HDInsight では、Azure BLOB ストレージ上でフル機能の Hadoop 分散ファイル システム (HDFS) を利用できます。HDFS 拡張機能として、シームレスなエクスペリエンスを顧客に提供するように設計されています。Hadoop エコシステムのすべてのコンポーネントを使用し、管理対象のデータを直接操作できます。Azure BLOB ストレージと HDFS は、データの保管と計算処理のために最適化された別個のファイル システムです。Azure BLOB ストレージを使用するメリットの詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」をご覧ください。

**前提条件**

開始する前に、次の要件にご注意ください。

* Azure HDInsight クラスター。手順については、「[Azure HDInsight の概要][hdinsight-get-started]」または「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」を参照してください。

##BLOB ストレージを使用する理由

通常、Azure の HDInsight クラスターは MapReduce ジョブを実行するためにデプロイされ、それらのジョブが完了すると削除されます。計算処理が完了した後もデータを HDFS クラスターに保持しておくと、このデータの格納コストがかさんでしまいます。Azure BLOB ストレージは、可用性と拡張性に優れ、大容量、低コストの共有可能なデータ ストレージ手段であり、HDInsight で処理されるデータも格納できます。BLOB ストレージにデータを格納すれば、データを失うことなく、計算処理で使用した HDInsight クラスターを安全に解放できます。

###ディレクトリ

Azure BLOB ストレージ コンテナーには、キーと値のペアとしてデータが格納されます。ディレクトリ階層はありません。ただし、キー名でスラッシュ (/) を使用すれば、ファイルがディレクトリ階層に保存されているように見せかけることができます。HDInsight は、これらを実際のディレクトリのように見なします。

たとえば、BLOB のキー名を「*input/log1.txt*」とします。この場合、"input" ディレクトリは実際に存在しませんが、キー名でスラッシュ (/) が使用されているのでファイル パスのように見えます。

このため、Azure エクスプローラー ツールを使用すると、サイズが 0 バイトのファイルがあることに気付きます。これらのファイルには 2 つの目的があります。

- 空のフォルダーがある場合、フォルダーの存在をマークします。Azure BLOB ストレージでは、foo/bar と呼ばれる BLOB が存在している場合、**foo** と呼ばれるフォルダーも存在すると見なされます。しかし、**foo** と呼ばれる空のフォルダーが必要な場合、それを示すには、この特殊な 0 バイトのファイルを配置するしか方法はありません。

- Hadoop ファイル システムが必要とする特殊なメタデータ、特に、フォルダーのアクセス許可と所有者を保持します。

##コマンド ライン ユーティリティ

Microsoft では、Azure BLOB ストレージを操作する次のユーティリティを提供しています。

| ツール | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Azure コマンド ライン インターフェイス][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Hadoop コマンド](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Azure CLI、Azure PowerShell、AzCopy はすべて Azure の外部で使用できますが、Hadoop コマンドは HDInsight クラスターでのみ使用でき、ローカル ファイル システムのデータを Azure BLOB ストレージに読み込みます。

###<a id="xplatcli"></a>Azure CLI

Azure CLI は、Azure サービスを管理できるクロスプラットフォーム ツールです。次の手順を使用して、Azure Blob ストレージにデータをアップロードします。

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Mac、Linux、Windows 用の Azure CLI をインストールして構成する](../xplat-cli-install.md)

2. コマンド プロンプト、bash、その他のシェルを開き、次を使用して、Azure サブスクリプションを認証します。

		azure login

	メッセージが表示されたら、サブスクリプションのユーザー名とパスワードを入力します。

3. サブスクリプションのストレージ アカウントを表示するには、次のコマンドを使用します。

		azure storage account list

4. 作業対象の BLOB を含むストレージ アカウントを選択し、次のコマンドを使用して、このアカウントのキーを取得します。

		azure storage account keys list <storage-account-name>

	これは、**プライマリ** キーと **セカンダリ** キーを返します。次の手順で使用するために、**プライマリ** キーの値をコピーします。

5. 次のコマンドを使用してストレージ アカウント内の blob コンテナーの一覧を取得します。

		azure storage container list -a <storage-account-name> -k <primary-key>

6. 次のコマンドを使用して、blob に対するファイルのアップロードとダウンロードを行います。

	* ファイルをアップロードするには、次を実行します。

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* ファイルをダウンロードするには、次を実行します。

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] 常に同じストレージ アカウントで処理する場合は、各コマンドにアカウントとキーを指定する代わりに、次の環境変数を設定することができます。
>
> * **[AZURE\_STORAGE\_ACCOUNT]**: ストレージ アカウント名
>
> * **[AZURE\_STORAGE\_ACCESS\_KEY]**: ストレージ アカウント キー名

###<a id="powershell"></a>Azure PowerShell

Azure PowerShell は、Azure のワークロードのデプロイと管理を制御し自動化するために使用できるスクリプティング環境です。ワークステーションを構成して Azure PowerShell を実行する方法については、「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」をご覧ください。

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**ローカル ファイルを Azure BLOB ストレージにアップロードするには**

1. Azure PowerShell コンソールを開きます。手順については「[Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)」をご覧ください。
2. 次のスクリプトで最初の 5 つの変数の値を設定します。

		$resourceGroupName = "<AzureResourceGroupName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"

		# Get the storage account key
		$storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

		# Copy the file from local workstation to the Blob container
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. スクリプトを Azure PowerShell コンソールに貼り付け、実行してファイルをコピーします。

HDInsight で使用する PowerShell スクリプトの例については、[HDInsight ツール](https://github.com/blackmist/hdinsight-tools)のページを参照してください。

###<a id="azcopy"></a>AzCopy

AzCopy は、Azure ストレージ アカウントでデータの送受信タスクが簡単になるコマンドライン ツールです。スタンドアロン ツールとして使用することも、既存のアプリケーションに組み込むこともできます。[AzCopy をダウンロード][azure-azcopy-download]してください。

AzCopy の構文は次のとおりです。

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

詳細については、「[AzCopy - Uploading/Downloading files for Azure Blobs (AzCopy - Azure BLOB のファイルのアップロード/ダウンロードについて)][azure-azcopy]」をご覧ください。


###<a id="commandline"></a>Hadoop コマンド ライン

Hadoop コマンド ラインは、クラスターのヘッド ノードに既にデータが存在している場合に、BLOB ストレージにデータを格納する際にのみ役立ちます。

Hadoop コマンドを使用するためには、まず、次の方法のいずれかを使用してヘッドノードに接続する必要があります。

* **Windows ベースの HDInsight**: [リモート デスクトップを使用して接続する](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **Linux ベースの HDInsight**: SSH を使用して接続する ([SSH コマンド](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) または [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

接続したら、次の構文を使用してファイルをストレージにアップロードすることができます。

	hadoop -copyFromLocal <localFilePath> <storageFilePath>

たとえば、`hadoop fs -copyFromLocal data.txt /example/data/data.txt` のように指定します。

HDInsight の既定のファイル システムは Azure BLOB ストレージにあるため、/example/data.txt は実際は Azure BLOB ストレージ上にあります。このファイルは次のように表すこともできます。

	wasbs:///example/data/data.txt

または

	wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Hadoop のその他のファイル操作コマンドの一覧については、[http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html) を参照してください。

##グラフィカル クライアント

Azure Storage を操作するためのグラフィカル インターフェイスを提供するアプリケーションもいくつかあります。これらのアプリケーションの一部を次の一覧に示します。

| クライアント | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Microsoft Visual Studio Tools for HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) | ✔ | ✔ | ✔ |
| [Azure Storage Explorer](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure 用エクスプローラー](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Cyberduck](https://cyberduck.io/) | | ✔ | ✔ |

###Visual Studio Tools for HDInsight

詳細については、「[リンクしているリソースへの移動](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources)」を参照してください。

###<a id="storageexplorer"></a>Azure Storage エクスプローラー

*Azure Storage エクスプローラー*は、BLOB 内のデータを調べたり、変更したりするときに役立つツールです。これは、[http://storageexplorer.com/](http://storageexplorer.com/) からダウンロードできる無料のオープン ソース ツールです。このリンクからソース コードも入手できます。

Azure Storage エクスプローラーを使用するには、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。この情報を取得する方法については、「[ストレージ アカウントの作成、管理、削除][azure-create-storage-account]」の「方法:ストレージ アクセス キーを表示、コピー、再生成する」をご覧ください。

1. Azure Storage エクスプローラーを実行します。ストレージ エクスプローラーを初めて実行した場合は、___ストレージ アカウント名___とストレージ アカウント キーの入力を求められます。ストレージ エクスプローラーを以前に実行したことがある場合は、[追加] ボタンをクリックして、新しいストレージ アカウント名とストレージ アカウント キーを追加します。

    HDinsight クラスターで使用されるストレージ アカウントの名前とキーを入力し、__[保存/開く]__ をクリックします。

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

5. インターフェイスの左側にあるコンテナーの一覧で、HDInsight クラスターに関連付けられているコンテナーの名前をクリックします。既定では、これは HDInsight クラスターの名前ですが、クラスターの作成時に特定の名前を入力した場合は異なることがあります。

6. ツール バーのアップロード アイコンをクリックします。

    ![アップロード アイコンが強調表示されたツール バー](./media/hdinsight-upload-data/toolbar.png)

7. アップロードするファイルを指定して、**[開く]** をクリックします。メッセージが表示されたら、__[アップロード]__ をクリックして、ファイルをストレージ コンテナーのルートにアップロードします。ファイルを特定のパスにアップロードする場合は、__[アップロード先]__ フィールドにパスを入力し、__[アップロード]__ をクリックします。

    ![[ファイルのアップロード] ダイアログ](./media/hdinsight-upload-data/fileupload.png)
    
    ファイルのアップロードが終了すると、HDInsight クラスターでジョブからそのファイルを使用できます。

##Azure Blob ストレージをローカル ドライブとしてマウントする

「[Mount Azure Blob Storage as Local Drive (Azure BLOB ストレージをローカル ドライブとしてマウントする)](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)」をご覧ください。

##サービス

###Azure Data Factory

Azure Data Factory は完全に管理されたサービスで、データの保存、データの処理、データの移動の各サービスを効率的かつスケーラブルで信頼性の高いデータ生成パイプラインとして構成します。

Azure Data Factory は、Azure BLOB ストレージへのデータの移動や、Hive や Pig などの HDInsight 機能を直接使用するデータ パイプラインの作成に使用できます。

詳細については、[Azure Data Factory のドキュメント](https://azure.microsoft.com/documentation/services/data-factory/)のページを参照してください。

###<a id="sqoop"></a>Apache Sqoop

Sqoop は、Hadoop とリレーショナル データベース間でデータを転送するためのツールです。このツールを使用して、SQL、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) から Hadoop 分散ファイル システム (HDFS) へデータをインポートしたり、MapReduce または Hive を使用して Hadoop のデータを変換し、そのデータを RDBMS へ取り込んだりできます。

詳細については、[HDInsight での Sqoop の使用][hdinsight-use-sqoop]に関するページを参照してください。

##開発 SDK

Azure BLOB ストレージには、次のプログラミング言語で Azure SDK を使用してアクセスできます。

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Azure SDK のインストールの詳細については、[Azure のダウンロード](https://azure.microsoft.com/downloads/)のページを参照してください。


## 次のステップ
ここでは、HDInsight にデータを取り込む方法を説明しました。次の記事でデータの分析方法を学習してください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Pig の使用][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

<!---HONumber=AcomDC_0914_2016-->