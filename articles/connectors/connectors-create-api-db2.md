<properties
    pageTitle="ロジック アプリへの DB2 コネクタの追加 | Microsoft Azure"
    description="DB2 コネクタと REST API パラメーターの概要"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="09/19/2016"
   ms.author="plarsen"/>


# DB2 コネクタの使用
Microsoft Connector for DB2 は、IBM DB2 データベースに格納されているリソースに Logic Apps を接続します。このコネクタには、TCP/IP ネットワーク経由でリモート DB2 サーバー コンピューターと通信するための Microsoft クライアントが含まれています。このリモート DB2 サーバー コンピューターには、クラウド データベース (IBM Bluemix dashDB や Azure 仮想化で実行されている IBM DB2 for Windows) のほか、オンプレミス データ ゲートウェイを使用したオンプレミスのデータベースなどがあります。サポートされている IBM DB2 のプラットフォームとバージョンについては、このトピックの末尾にある一覧を参照してください。

DB2 コネクタでは、次のデータベース操作がサポートされています。

- データベース テーブルの一覧表示
- SELECT を使用した 1 つの行の読み取り
- SELECT を使用したすべての行の読み取り
- INSERT を使用した 1 つの行の追加
- UPDATE を使用した 1 つの行の変更
- DELETE を使用した 1 つの行の削除

このトピックでは、ロジック アプリでコネクタを使用してデータベース操作を処理する方法を説明します。

>[AZURE.NOTE] 本記事は、一般公開された Logic Apps の一般公開 (GA) を対象としています。

Logic Apps の詳細については、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関するページを参照してください。

## ロジック アプリのアクション
コネクタでサポートされているロジック アプリのアクションは、次のとおりです。

- GetTables
- GetRow
- GetRows
- InsertRow
- UpdateRow
- DeleteRow


## テーブルを一覧表示するためのロジック アプリの定義
操作に関するロジック アプリの定義は、Microsoft Azure Portal を通じて実行する多くの手順で構成されています。DB2 データベース内のテーブルを一覧表示するように、ロジック アプリのアクションを定義できます。これにより、コネクタは、DB2 スキーマ ステートメント (CALL SYSIBM.SQLTABLES) を処理するよう指示されます。

### ロジック アプリ インスタンスの定義
1.	**Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2.	**名前** (例: "**Db2getTables**")、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。**[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### ロジック アプリのトリガーとアクションの定義
1.	**Logic Apps デザイナー**の**テンプレート**の一覧で、**[Blank LogicApp (空のロジック アプリ)]** をクリックします。
2.	**トリガー**の一覧で **[繰り返し]** をクリックします。
3.	**[繰り返し]** トリガーで **[編集]** をクリックし、**[頻度]** ドロップダウンをクリックして **[日]** を選択します。その後、**[間隔]** をクリックして「**7**」を入力します。
4.	**[+ New step (+ 新しい手順)]** ボックスをクリックし、**[Add an action (アクションの追加)]** をクリックします。
5.	**アクション**の一覧で、**[Search for more actions (その他のアクションの検索)]** ボックスに「**db2**」と入力し、**[DB2 - Get tables (Preview) (DB2 - テーブルの取得 (プレビュー))]** をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorActions.png)

6.	**[DB2 - Get tables (DB2 - テーブルの取得)]** 構成ウィンドウで、**[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)]** **チェック ボックス**をオンにして有効にします。クラウドからオンプレミスへの設定の変更を確認します。
	- "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、**[サーバー]** の値を入力します。たとえば、「**ibmserver01:50000**」と入力します。
	- **[データベース]** の値を入力します。たとえば、「**nwind**」と入力します。
	- **[認証]** の値を選択します。たとえば、**[Basic]** を選択します。
	- **[ユーザー名]** の値を入力します。たとえば、「**db2admin**」と入力します。
	- **[パスワード]** の値を入力します。たとえば、「**Password1**」と入力します。
	- **[ゲートウェイ]** の値を選択します。たとえば、**[datagateway01]** をクリックします。
7. **[作成]** をクリックし、次に **[保存]** をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

8.	**[Db2getTables]** ブレードで、**[概要]** の下の**すべての実行**一覧内にある、先頭の項目 (最新の実行) をクリックします。
9.	**[Logic app run (ロジック アプリの実行)]** ブレードで、**[Run Details (実行の詳細)]** をクリックします。**アクション**の一覧内にある、**[Get\_tables]** をクリックします。**[状態]** の値が **[成功]** になっていることを確認します。**[Inputs link (入力リンク)]** をクリックします。入力が表示されます。**[Outputs link (出力リンク)]** をクリックします。出力が表示されます。ここに、テーブルの一覧が含まれています。

	![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## DB2 接続の定義
コネクタでは、次の接続プロパティを使用した、オンプレミス データベースとクラウド データベースの両方への接続がサポートされています。

プロパティ | Description
--- | ---
server | 必須の server プロパティは、(コロンで区切られた後に) TCP/IP ポート番号が続く、IPv4 または IPv6 の形式の TCP/IP アドレスまたはエイリアスを表す文字列値を受け入れます。 
database | 必須の database プロパティは、DRDA リレーショナル データベース名 (RDBNAM) を表す文字列値を受け入れます。DB2 for z/OS は、16 バイトの文字列を受け入れます (database は IBM DB2 for z/OS の場所です)。DB2 for i5/OS は、18 バイトの文字列を受け入れます (database は IBM DB2 for i リレーショナル データベースです)。DB2 for LUW は、8 バイトの文字列を受け入れます。
authentication | 任意の authentication プロパティは、Basic と Windows (kerberos) のいずれかのリスト項目の値を受け入れます。 
username | 必須の username プロパティは、文字列値を受け入れます。DB2 for z/OS は、8 バイトの文字列を受け入れます。DB2 for i は、10 バイトの文字列を受け入れます。DB2 for Linux または DB2 for UNIX は、8 バイトの文字列を受け入れます。DB2 for Windows は、30 バイトの文字列を受け入れます。
パスワード | 必須の password プロパティは、文字列値を受け入れます。
gateway | 必須の gateway プロパティは、リスト項目の値を受け入れ、ストレージ グループ内のロジック アプリに定義されたオンプレミス データ ゲートウェイを表します。  

## オンプレミス ゲートウェイ接続の定義
コネクタは、オンプレミス ゲートウェイ経由でオンプレミスの DB2 データベースにアクセスできます。詳細については、ゲートウェイのトピックをご覧ください。

1. **[ゲートウェイ]** 構成ウィンドウで、**チェック ボックス**をオンにして**ゲートウェイ経由の接続**を有効にします。クラウドからオンプレミスへの設定の変更を確認します。
2. "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、**[サーバー]** の値を入力します。たとえば、「**ibmserver01:50000**」と入力します。
3. **[データベース]** の値を入力します。たとえば、「**nwind**」と入力します。
4. **[認証]** の値を選択します。たとえば、**[Basic]** を選択します。
5. **[ユーザー名]** の値を入力します。たとえば、「**db2admin**」と入力します。
6. **[パスワード]** の値を入力します。たとえば、「**Password1**」と入力します。
7. **[ゲートウェイ]** の値を選択します。たとえば、**[datagateway01]** をクリックします。
8. **[作成]** をクリックして続行します。

	![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## クラウド接続の定義
コネクタは、クラウド DB2 データベースにアクセスできます。

1. **[ゲートウェイ]** 構成ウィンドウで**チェック ボックス**をオンにせず、**ゲートウェイ経由の接続**を無効のままにしておきます。
2. **[接続名]** の値を入力します。たとえば、「**hisdemo2**」と入力します。
3. "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、**DB2 サーバー名**の値を入力します。たとえば、「**hisdemo2.cloudapp.net:50000**」と入力します。
3. **DB2 データベース名**の値を入力します。たとえば、「**nwind**」と入力します。
4. **[ユーザー名]** の値を入力します。たとえば、「**db2admin**」と入力します。
5. **[パスワード]** の値を入力します。たとえば、「**Password1**」と入力します。
6. **[作成]** をクリックして続行します。

	![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## SELECT を使用してすべての行を取得するためのロジック アプリの定義
DB2 テーブル内のすべての行を取得するように、ロジック アプリのアクションを定義できます。これにより、コネクタは、DB2 SELECT ステートメント (例: **SELECT * FROM AREA**) を処理するよう指示されます。

### ロジック アプリ インスタンスの定義
1.	**Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2.	**名前** (例: "**Db2getRows**")、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。**[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### ロジック アプリのトリガーとアクションの定義
1.	**Logic Apps デザイナー**の**テンプレート**の一覧で、**[Blank LogicApp (空のロジック アプリ)]** をクリックします。
2.	**トリガー**の一覧で **[繰り返し]** をクリックします。
3.	**[繰り返し]** トリガーで **[編集]** をクリックし、**[頻度]** ドロップダウンをクリックして **[日]** を選択します。その後、**[間隔]** をクリックして「**7**」を入力します。
4.	**[+ New step (+ 新しい手順)]** ボックスをクリックし、**[Add an action (アクションの追加)]** をクリックします。
5.	**アクション**の一覧で、**[Search for more actions (その他のアクションの検索)]** ボックスに「**db2**」と入力し、**[DB2 - Get rows (Preview) (DB2 - 行の取得 (プレビュー))]** をクリックします。
6. **[Get rows (Preview) (行の取得 (プレビュー))]** アクションで、**[接続の変更]** をクリックします。
7. **[接続]** 構成ウィンドウで、**[新規作成]** をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
  
8. **[ゲートウェイ]** 構成ウィンドウで**チェック ボックス**をオンにせず、**ゲートウェイ経由の接続**を無効のままにしておきます。
	- **[接続名]** の値を入力します。たとえば、「**HISDEMO2**」と入力します。
	- "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、**DB2 サーバー名**の値を入力します。たとえば、「**HISDEMO2.cloudapp.net:50000**」と入力します。
	- **DB2 データベース名**の値を入力します。たとえば、「**NWIND**」と入力します。
	- **[ユーザー名]** の値を入力します。たとえば、「**db2admin**」と入力します。
	- **[パスワード]** の値を入力します。たとえば、「**Password1**」と入力します。
9. **[作成]** をクリックして続行します。

	![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

10. **[テーブル名]** の一覧にある**下向き矢印**をクリックし、**[AREA]** をクリックします。
11. 必要に応じて **[詳細オプションの表示]** をクリックし、クエリ オプションを指定します。
12. [**Save**] をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)

13.	**[Db2getRows]** ブレードで、**[概要]** の下の**すべての実行**一覧内にある、先頭の項目 (最新の実行) をクリックします。
14.	**[Logic app run (ロジック アプリの実行)]** ブレードで、**[Run Details (実行の詳細)]** をクリックします。**アクション**の一覧内にある、**[Get\_rows]** をクリックします。**[状態]** の値が **[成功]** になっていることを確認します。**[Inputs link (入力リンク)]** をクリックします。入力が表示されます。**[Outputs link (出力リンク)]** をクリックします。出力が表示されます。ここに、行の一覧が含まれています。

	![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## INSERT を使用して 1 つの行を追加するためのロジック アプリの定義
DB2 テーブル内に 1 つの行を追加するように、ロジック アプリのアクションを定義できます。これにより、コネクタは、DB2 INSERT ステートメント (例: **INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)**) を処理するよう指示されます。

### ロジック アプリ インスタンスの定義
1.	**Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2.	**名前** (例: "**Db2insertRow**")、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。**[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### ロジック アプリのトリガーとアクションの定義
1.	**Logic Apps デザイナー**の**テンプレート**の一覧で、**[Blank LogicApp (空のロジック アプリ)]** をクリックします。
2.	**トリガー**の一覧で **[繰り返し]** をクリックします。
3.	**[繰り返し]** トリガーで **[編集]** をクリックし、**[頻度]** ドロップダウンをクリックして **[日]** を選択します。その後、**[間隔]** をクリックして「**7**」を入力します。
4.	**[+ New step (+ 新しい手順)]** ボックスをクリックし、**[Add an action (アクションの追加)]** をクリックします。
5.	**アクション**の一覧で、**[Search for more actions (その他のアクションの検索)]** ボックスに「**db2**」と入力し、**[DB2 - Insert row (Preview) (DB2 - 行の挿入 (プレビュー))]** をクリックします。
6. **[Get rows (Preview) (行の取得 (プレビュー))]** アクションで、**[接続の変更]** をクリックします。
7. **[接続]** 構成ウィンドウで、接続をクリックして選択します。たとえば、**[hisdemo2]** をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. **[テーブル名]** の一覧にある**下向き矢印**をクリックし、**[AREA]** をクリックします。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。たとえば、**[AREAID]** に「**99999**」、[AREADESC] に「**Area 99999**」、**[REGIONID]** に「**102**」を入力します。
10. [**Save**] をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
 
11.	**[Db2insertRow]** ブレードで、**[概要]** の下の**すべての実行**一覧内にある、先頭の項目 (最新の実行) をクリックします。
12.	**[Logic app run (ロジック アプリの実行)]** ブレードで、**[Run Details (実行の詳細)]** をクリックします。**アクション**の一覧内にある、**[Get\_rows]** をクリックします。**[状態]** の値が **[成功]** になっていることを確認します。**[Inputs link (入力リンク)]** をクリックします。入力が表示されます。**[Outputs link (出力リンク)]** をクリックします。出力が表示されます。ここに、新しい行が含まれています。

	![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## SELECT を使用して 1 つの行を取得するためのロジック アプリの定義
DB2 テーブル内の 1 つの行を取得するように、ロジック アプリのアクションを定義できます。これにより、コネクタは、DB2 SELECT WHERE ステートメント (例: **SELECT FROM AREA WHERE AREAID = '99999'**) を処理するよう指示されます。

### ロジック アプリ インスタンスの定義
1.	**Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2.	**名前** (例: "**Db2getRow**")、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。**[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### ロジック アプリのトリガーとアクションの定義
1.	**Logic Apps デザイナー**の**テンプレート**の一覧で、**[Blank LogicApp (空のロジック アプリ)]** をクリックします。
2.	**トリガー**の一覧で **[繰り返し]** をクリックします。
3.	**[繰り返し]** トリガーで **[編集]** をクリックし、**[頻度]** ドロップダウンをクリックして **[日]** を選択します。その後、**[間隔]** をクリックして「**7**」を入力します。
4.	**[+ New step (+ 新しい手順)]** ボックスをクリックし、**[Add an action (アクションの追加)]** をクリックします。
5.	**アクション**の一覧で、**[Search for more actions (その他のアクションの検索)]** ボックスに「**db2**」と入力し、**[DB2 - Get rows (Preview) (DB2 - 行の取得 (プレビュー))]** をクリックします。
6. **[Get rows (Preview) (行の取得 (プレビュー))]** アクションで、**[接続の変更]** をクリックします。
7. **[接続]** 構成ウィンドウで、既存の接続をクリックして選択します。たとえば、**[hisdemo2]** をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. **[テーブル名]** の一覧にある**下向き矢印**をクリックし、**[AREA]** をクリックします。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。たとえば、**[AREAID]** に「**99999**」と入力します。
10. 必要に応じて **[詳細オプションの表示]** をクリックし、クエリ オプションを指定します。
11. [**Save**] をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)

12.	**[Db2getRow]** ブレードで、**[概要]** の下の**すべての実行**一覧内にある、先頭の項目 (最新の実行) をクリックします。
13.	**[Logic app run (ロジック アプリの実行)]** ブレードで、**[Run Details (実行の詳細)]** をクリックします。**アクション**の一覧内にある、**[Get\_rows]** をクリックします。**[状態]** の値が **[成功]** になっていることを確認します。**[Inputs link (入力リンク)]** をクリックします。入力が表示されます。**[Outputs link (出力リンク)]** をクリックします。出力が表示されます。この出力に行が含まれています。

	![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## UPDATE を使用して 1 つの行を変更するためのロジック アプリの定義
DB2 テーブル内の 1 つの行を変更するように、ロジック アプリのアクションを定義できます。これにより、コネクタは、DB2 UPDATE ステートメント (例: **UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)**) を処理するよう指示されます。

### ロジック アプリ インスタンスの定義
1.	**Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2.	**名前** (例: "**Db2updateRow**")、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。**[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### ロジック アプリのトリガーとアクションの定義
1.	**Logic Apps デザイナー**の**テンプレート**の一覧で、**[Blank LogicApp (空のロジック アプリ)]** をクリックします。
2.	**トリガー**の一覧で **[繰り返し]** をクリックします。
3.	**[繰り返し]** トリガーで **[編集]** をクリックし、**[頻度]** ドロップダウンをクリックして **[日]** を選択します。その後、**[間隔]** をクリックして「**7**」を入力します。
4.	**[+ New step (+ 新しい手順)]** ボックスをクリックし、**[Add an action (アクションの追加)]** をクリックします。
5.	**アクション**の一覧で、**[Search for more actions (その他のアクションの検索)]** ボックスに「**db2**」と入力し、**[DB2 - Update row (Preview) (DB2 - 行の更新 (プレビュー))]** をクリックします。
6. **[Get rows (Preview) (行の取得 (プレビュー))]** アクションで、**[接続の変更]** をクリックします。
7. **[接続]** 構成ウィンドウで、既存の接続をクリックして選択します。たとえば、**[hisdemo2]** をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. **[テーブル名]** の一覧にある**下向き矢印**をクリックし、**[AREA]** をクリックします。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。たとえば、**[AREAID]** に「**99999**」、[AREADESC] に「**Updated 99999**」、**[REGIONID]** に「**102**」を入力します。
10. [**Save**] をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)

11.	**[Db2updateRow]** ブレードで、**[概要]** の下の**すべての実行**一覧内にある、先頭の項目 (最新の実行) をクリックします。
12.	**[Logic app run (ロジック アプリの実行)]** ブレードで、**[Run Details (実行の詳細)]** をクリックします。**アクション**の一覧内にある、**[Get\_rows]** をクリックします。**[状態]** の値が **[成功]** になっていることを確認します。**[Inputs link (入力リンク)]** をクリックします。入力が表示されます。**[Outputs link (出力リンク)]** をクリックします。出力が表示されます。ここに、新しい行が含まれています。

	![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## DELETE を使用して 1 つの行を削除するためのロジック アプリの定義
DB2 テーブル内の 1 つの行を削除するように、ロジック アプリのアクションを定義できます。これにより、コネクタは、DB2 DELETE ステートメント (例: **DELETE FROM AREA WHERE AREAID = '99999'**) を処理するよう指示されます。

### ロジック アプリ インスタンスの定義
1.	**Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2.	**名前** (例: "**Db2deleteRow**")、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。**[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### ロジック アプリのトリガーとアクションの定義
1.	**Logic Apps デザイナー**の**テンプレート**の一覧で、**[Blank LogicApp (空のロジック アプリ)]** をクリックします。
2.	**トリガー**の一覧で **[繰り返し]** をクリックします。
3.	**[繰り返し]** トリガーで **[編集]** をクリックし、**[頻度]** ドロップダウンをクリックして **[日]** を選択します。その後、**[間隔]** をクリックして「**7**」を入力します。
4.	**[+ New step (+ 新しい手順)]** ボックスをクリックし、**[Add an action (アクションの追加)]** をクリックします。
5.	**アクション**の一覧で、**[Search for more actions (その他のアクションの検索)]** ボックスで **db2** をクリックし、**[DB2 - Delete row (Preview) (DB2 - 行の削除 (プレビュー))]** をクリックします。
6. **[Get rows (Preview) (行の取得 (プレビュー))]** アクションで、**[接続の変更]** をクリックします。
7. **[接続]** 構成ウィンドウで、既存の接続をクリックして選択します。たとえば、**[hisdemo2]** をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. **[テーブル名]** の一覧にある**下向き矢印**をクリックし、**[AREA]** をクリックします。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。たとえば、**[AREAID]** に「**99999**」と入力します。
10. [**Save**] をクリックします。

	![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)

11.	**[Db2deleteRow]** ブレードで、**[概要]** の下の**すべての実行**一覧内にある、先頭の項目 (最新の実行) をクリックします。
12.	**[Logic app run (ロジック アプリの実行)]** ブレードで、**[Run Details (実行の詳細)]** をクリックします。**アクション**の一覧内にある、**[Get\_rows]** をクリックします。**[状態]** の値が **[成功]** になっていることを確認します。**[Inputs link (入力リンク)]** をクリックします。入力が表示されます。**[Outputs link (出力リンク)]** をクリックします。出力が表示されます。ここに、削除した行が含まれています。

	![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## 技術的な詳細

## アクション
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。DB2 データベース コネクタには、次のアクションがあります。

|アクション|Description|
|--- | ---|
|[GetRow](connectors-create-api-db2.md#get-row)|DB2 テーブルから 1 つの行を取得します|
|[GetRows](connectors-create-api-db2.md#get-rows)|DB2 テーブルから複数の行を取得します|
|[InsertRow](connectors-create-api-db2.md#insert-row)|DB2 テーブルに新しい行を挿入します|
|[DeleteRow](connectors-create-api-db2.md#delete-row)|DB2 テーブルから行を削除します|
|[GetTables](connectors-create-api-db2.md#get-tables)|DB2 データベースからテーブルを取得します|
|[UpdateRow](connectors-create-api-db2.md#update-row)|DB2 テーブルの既存の行を更新します|

### アクションの詳細

このセクションでは、必須または任意の入力プロパティ、コネクタに関連付けられた対応する出力など、各アクションに関する具体的な詳細について説明します。

#### 行を取得する 
DB2 テーブルから 1 つの行を取得します。

| プロパティ名| Displayname Settings |Description|
| ---|---|---|
|table * | テーブル名 |DB2 テーブルの名前|
|id * | 行 ID |取得する行の一意識別子|

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
項目

| プロパティ名 | データ型 |
|---|---|
|ItemInternalId|string|


#### 行を取得する 
DB2 テーブルから複数の行を取得します。

|プロパティ名| Displayname Settings|Description|
| ---|---|---|
|table*|テーブル名|DB2 テーブルの名前|
|$skip|Skip Count (スキップ数)|スキップするエントリの数 (既定値 = 0)|
|$top|Maximum Get Count (最大取得数)|取得するエントリの最大数 (既定値 = 256)|
|$filter|Filter Query (フィルター クエリ)|エントリ数を制限する ODATA filter クエリ|
|$orderby|Order By (並べ替え)|エントリの順序を指定する ODATA orderBy クエリ|

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
ItemsList

| プロパティ名 | データ型 |
|---|---|
|値|array|


#### 行を挿入する 
DB2 テーブルに新しい行を挿入します。

|プロパティ名| Displayname Settings|Description|
| ---|---|---|
|table*|テーブル名|DB2 テーブルの名前|
|item*|行|DB2 の指定されたテーブルに挿入する行|

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
項目

| プロパティ名 | データ型 |
|---|---|
|ItemInternalId|string|


#### 行を削除する 
DB2 テーブルから行を削除します。

|プロパティ名| Displayname Settings|Description|
| ---|---|---|
|table*|テーブル名|DB2 テーブルの名前|
|id*|行 ID|削除する行の一意識別子|

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細
なし。

#### テーブルを取得する 
DB2 データベースからテーブルを取得します。

この呼び出しには、パラメーターはありません。

##### 出力の詳細 
TablesList

| プロパティ名 | データ型 |
|---|---|
|値|array|

#### 行を更新する 
DB2 テーブルの既存の行を更新します。

|プロパティ名| Displayname Settings|Description|
| ---|---|---|
|table*|テーブル名|DB2 テーブルの名前|
|id*|行 ID|更新する行の一意識別子|
|item*|行|更新された値のある行|

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### 出力の詳細  
項目

| プロパティ名 | データ型 |
|---|---|
|ItemInternalId|string|


### HTTP 応答

他のアクションを呼び出すとき、特定の応答を受け取る場合があります。次の表に、これらの応答とその説明を示します。

|名前|説明|
|---|---|
|200|OK|
|202|承認済み|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## サポートされている DB2 のプラットフォームとバージョン
コネクタでは、これらの IBM DB2 のプラットフォームとバージョンがサポートされているほか、分散型リレーショナル データベース アーキテクチャ (DRDA) SQL アクセス マネージャー (SQLAM) バージョン 10 および 11 に対応している IBM DB2 互換製品 (IBM Bluemix dashDB など) がサポートされています。

- IBM DB2 for z/OS 11.1
- IBM DB2 for z/OS 10.1
- IBM DB2 for i 7.3
- IBM DB2 for i 7.2
- IBM DB2 for i 7.1
- IBM DB2 for LUW 11
- IBM DB2 for LUW 10.5


## 次のステップ

[ロジック アプリを作成する](../app-service-logic/app-service-logic-create-a-logic-app.md)[API の一覧](apis-list.md)で、Logic Apps で使用できる他のコネクタを確認してください。

<!---HONumber=AcomDC_0921_2016-->