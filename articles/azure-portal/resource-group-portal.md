<properties 
	pageTitle="Azure ポータルを使用した Azure リソースの管理 | Microsoft Azure" 
	description="Azure ポータルと Azure Resource Manager を使用してリソースを管理します。ダッシュボードを使用してリソースを監視する方法について説明します。" 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="tomfitz"/>

# ポータルを使用した Azure リソースの管理

> [AZURE.SELECTOR]
- [Azure PowerShell](../powershell-azure-resource-manager.md)
- [Azure CLI](../xplat-cli-azure-resource-manager.md)
- [ポータル](resource-group-portal.md)
- [REST API](../resource-manager-rest-api.md)

このトピックでは、[Azure ポータル](https://portal.azure.com)と [Azure Resource Manager](../resource-group-overview.md) を使用して Azure リソースを管理する方法について説明します。ポータルを使用したリソースのデプロイについては、「[Deploy resources with Resource Manager templates and Azure portal (Resource Manager テンプレートと Azure ポータルを使用したリソースのデプロイ)](../resource-group-template-deploy-portal.md)」を参照してください。

現時点では、すべてのサービスでポータルまたはリソース マネージャーがサポートされているわけではありません。これらのサービスの場合、[クラシック ポータル](https://manage.windowsazure.com)を使用する必要があります。各サービスの状態については、「[Azure ポータルの可用性チャート](https://azure.microsoft.com/features/azure-portal/availability/)」を参照してください。

## リソース グループの管理

1. 自分のサブスクリプションのリソース グループをすべて表示するには、**[リソース グループ]** を選択します。

    ![リソース グループの参照](./media/resource-group-portal/browse-groups.png)

1. 空のリソース グループを作成するために、**[追加]** を選択します。

    ![add resource group](./media/resource-group-portal/add-resource-group.png)

1. 新しいリソース グループの名前と場所を入力します。**[作成]** を選択します。

    ![リソースグループの作成](./media/resource-group-portal/create-empty-group.png)

1. **[最新の情報に更新]** を選択して、先ほど作成したリソース グループを表示します。

    ![refresh resource group](./media/resource-group-portal/refresh-resource-groups.png)

1. リソース グループについて表示される情報をカスタマイズするために、**[列]** を選択します。

    ![customize columns](./media/resource-group-portal/select-columns.png)

1. 追加する列を選択してから、**[更新]** を選択します。

    ![add columns](./media/resource-group-portal/add-columns.png)

1. 新しいリソース グループへのリソースのデプロイについては、「[Resource Manager テンプレートと Azure ポータルを使用したリソースのデプロイ](../resource-group-template-deploy-portal.md)」を参照してください。

1. リソース グループにすばやくアクセスするために、ブレードをダッシュボードにピン留めできます。

    ![リソース グループの固定](./media/resource-group-portal/pin-group.png)

1. ダッシュボードには、リソース グループとそのリソースが表示されます。リソース グループまたはそのリソースのいずれかを選択して、各項目に移動できます。

    ![リソース グループの固定](./media/resource-group-portal/show-resource-group-dashboard.png)

## リソースへのタグ付け

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。タグを処理する方法の詳細については、「[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)」をご覧ください。

[AZURE.INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## リソースの監視

リソースを選択すると、そのリソースの種類を監視するための既定のグラフと表が、リソースのブレードに表示されます。

1. リソースを選択し、**[監視]** セクションに注目してください。リソースの種類に関連するグラフが含まれます。次の図は、ストレージ アカウントの既定の監視データを示しています。

    ![監視の表示](./media/resource-group-portal/show-monitoring.png)

1. ブレードのセクションは、そのセクションの上にある省略記号 (...) を選択すると、ダッシュボードにピン留めできます。さらに、ブレードのセクションのサイズをカスタマイズしたり、完全に削除したりできます。次の図は、[CPU とメモリ] セクションをピン留め、カスタマイズ、または削除する方法を示しています。

    ![セクションの固定](./media/resource-group-portal/pin-cpu-section.png)

1. セクションをダッシュボードにピン留めすると、ダッシュボード上に概要が表示されます。さらに、それを選択すると、データの詳細情報が即時に表示されます。

    ![view dashboard](./media/resource-group-portal/view-startboard.png)

1. ポータルから監視するデータを完全にカスタマイズするには、既定のダッシュボードに移動して、**[新しいダッシュボード]** を選択します。

    ![dashboard](./media/resource-group-portal/dashboard.png)

1. 新しいダッシュボードに名前を付けて、ダッシュボードにタイルをドラッグします。タイルは、さまざまなオプションでフィルター処理されます。

    ![dashboard](./media/resource-group-portal/create-dashboard.png)

     ダッシュボードの操作の詳細については、「[Azure Portal でのダッシュボードの作成と共有](azure-portal-dashboards.md)」を参照してください。

## リソースの管理

リソースのブレードで、リソースを管理するためのオプションが表示されます。ポータルでは、その特定のリソースの種類の管理オプションが表示されます。リソース ブレードの上部全体および左側に管理コマンドが表示されます。

![リソースの管理](./media/resource-group-portal/manage-resources.png)

これらのオプションから、仮想マシンの開始と停止、または仮想マシンのプロパティの再構成などの操作を実行できます。

## リソースの移動

別のリソース グループまたは別のサブスクリプションにリソースを移動する必要がある場合は、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../resource-group-move-resources.md)」を参照してください。

## リソースのロック

サブスクリプション、リソース グループ、またはリソースにロックを適用し、組織の他のユーザーが誤って重要なリソースを削除したり変更したりするのを防止できます。詳細については、「[Azure リソース マネージャーによるリソースのロック](../resource-group-lock-resources.md)」を参照してください。

[AZURE.INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## サブスクリプションとコストを表示する

サブスクリプションの情報と、すべてのリソースの合計コストに関する情報を表示できます。**[サブスクリプション]** を選択し、表示するサブスクリプションを選択します。選択できるサブスクリプションが 1 つのみの場合もあります。

![サブスクリプション](./media/resource-group-portal/select-subscription.png)

サブスクリプション ブレードには、バーン レートが表示されます。

![バーン レート](./media/resource-group-portal/burn-rate.png)

また、リソースの種類別のコストの明細が表示されます。

![リソース コスト](./media/resource-group-portal/cost-by-resource.png)

## テンプレートをエクスポートする

リソース グループを設定したら、リソース グループのリソース マネージャー テンプレートを表示できます。テンプレートのエクスポートには、2 つの利点があります。

1. ソリューションの将来のデプロイを簡単に自動化できます。これは、テンプレートに完全なインフラストラクチャが含まれているためです。

2. ソリューションを表す JavaScript Object Notation (JSON) を見ることでテンプレートの構文に詳しくなります。

手順については、「[既存のリソースから Azure Resource Manager テンプレートをエクスポートする](../resource-manager-export-template.md)」を参照してください。

## リソース グループまたはリソースを削除する

リソース グループを削除すると、そこに含まれているすべてのリソースが削除されます。また、リソース グループ内にある個々のリソースを削除することもできます。他のリソース グループのリソースがリンクされている可能性があるため、リソース グループを削除する場合はご注意ください。リンク先のリソースは Resource Manager によって削除されませんが、予想されるリソースがない場合に正しく機能しない可能性があります。

![グループの削除](./media/resource-group-portal/delete-group.png)

## 次のステップ

- 監査ログの表示については、「[Resource Manager の監査操作](../resource-group-audit.md)」を参照してください。
- デプロイ エラーのトラブルシューティングについては、[Azure ポータルでのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)に関するページを参照してください。
- ポータルを使用したリソースのデプロイについては、「[Resource Manager テンプレートと Azure ポータルを使用したリソースのデプロイ](../resource-group-template-deploy-portal.md)」を参照してください。
- リソースへのアクセスの管理については、「[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../active-directory/role-based-access-control-configure.md)」を参照してください。

<!---HONumber=AcomDC_0914_2016-->