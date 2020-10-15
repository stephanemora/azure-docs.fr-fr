---
title: Configurer un groupe de disponibilité (Portail Azure)
description: Utilisez le portail Azure pour créer le cluster de basculement Windows, l’écouteur de groupe de disponibilité et l’équilibreur de charge interne sur une machine virtuelle SQL Server dans Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4020f47184e141a69586fc958f641547d7bde94d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89482796"
---
# <a name="configure-an-availability-group-for-sql-server-on-azure-vm-azure-portal---preview"></a>Configurer un groupe de disponibilité pour SQL Server sur une machine virtuelle Azure (Portail Azure – Préversion)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article explique comment utiliser le [portail Azure](https://portal.azure.com) pour configurer un groupe de disponibilité pour SQL Server sur des machines virtuelles Azure. 

Utilisez le portail Azure pour créer un cluster ou intégrer un cluster existant, puis créez le groupe de disponibilité, l’écouteur et l’équilibreur de charge interne. 

   > [!NOTE]
   > Cette fonctionnalité est actuellement en préversion et en cours de déploiement. Par conséquent, si la région de votre choix n’est pas disponible, nous vous invitions à revérifier ultérieurement. 


## <a name="prerequisites"></a>Prérequis

Pour configurer un groupe de disponibilité Always On à l’aide du portail Azure, vous devez disposer des éléments suivants : 

- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Un groupe de ressources avec un contrôleur de domaine. 
- Une ou plusieurs [machines virtuelles jointes à un domaine dans Azure qui exécutent SQL Server 2016 (ou version ultérieure) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) dans le *même* groupe à haute disponibilité ou dans des zones de disponibilité *différentes*, qui sont [inscrites auprès du fournisseur de ressources de machine virtuelle SQL en mode de gestion complète](sql-vm-resource-provider-register.md) et qui utilisent le même compte de domaine pour le service SQL Server sur chaque machine virtuelle.
- Deux adresses IP disponibles (non utilisées par une entité) : une pour l’équilibreur de charge interne et l’autre pour l’écouteur de groupe de disponibilité dans le même sous-réseau que le groupe de disponibilité. Si vous utilisez un équilibreur de charge existant, une seule adresse IP disponible suffit pour l’écouteur de groupe de disponibilité. 

## <a name="permissions"></a>Autorisations

Vous avez besoin des autorisations de compte suivantes pour configurer le groupe de disponibilité à l’aide du portail Azure : 

- Un compte d’utilisateur de domaine existant qui dispose d’une autorisation de **création d’objet ordinateur** dans le domaine. Par exemple, un compte d’administrateur de domaine dispose généralement d’une autorisation suffisante (par exemple : account@domain.com). _Ce compte doit également faire partie du groupe administrateur local sur chaque machine virtuelle pour créer le cluster._
- Le compte d’utilisateur du domaine qui contrôle SQL Server. Il doit s’agir du même compte pour chaque machine virtuelle SQL Server que vous souhaitez ajouter au groupe de disponibilité. 

## <a name="configure-cluster"></a>Configurer le cluster

Configurez le cluster à l’aide du portail Azure. Vous pouvez créer un nouveau cluster ou, si vous disposez déjà d’un cluster, vous pouvez l’intégrer au fournisseur de ressources de machine virtuelle SQL pour faciliter la gestion du portail.


### <a name="create-a-new-cluster"></a>Créer un cluster

Si vous disposez déjà d’un cluster, ignorez cette section et passez plutôt à [Intégrer un cluster existant](#onboard-existing-cluster). 

Si vous ne disposez pas encore d’un cluster, créez-en un à l’aide du portail Azure en procédant comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à votre ressource [machines virtuelles SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Sélectionnez **Haute disponibilité** sous **Paramètres**. 
1. Sélectionnez **+ Nouveau cluster de basculement Windows Server** pour ouvrir la page **Configurer le cluster de basculement Windows**.  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

1. Nommez votre cluster et fournissez un compte de stockage à utiliser comme témoin Cloud. Utilisez un compte de stockage existant ou sélectionnez **Créer nouveau** pour créer un nouveau compte de stockage. Un nom de compte de stockage doit être compris entre 3 et 24 caractères composés uniquement de chiffres et de lettres en minuscules.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

1. Développez **Informations d’identification du cluster de basculement Windows Server** pour fournir les [informations d’identification](https://docs.microsoft.com/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) du compte de service SQL Server, ainsi que les comptes de démarrage et d’opérateur de cluster s’ils sont différents du compte utilisé pour le service SQL Server. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail"
    ```
    


### <a name="onboard-existing-cluster"></a>Intégrer un cluster existant

Si vous disposez déjà d’un cluster configuré dans votre environnement de machine virtuelle SQL Server, vous pouvez l’intégrer à partir du portail Azure.

Pour ce faire, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à votre ressource [machines virtuelles SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Sélectionnez **Haute disponibilité** sous **Paramètres**. 
1. Sélectionnez **Intégrer le cluster de basculement Windows Server existant** pour ouvrir la page **Intégrer le cluster de basculement Windows Server**. 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

1. Passez en revue les paramètres de votre cluster. 
1. Sélectionnez **Appliquer** pour intégrer votre cluster, puis sélectionnez **Oui** à l’invite pour continuer.




## <a name="create-availability-group"></a>Créer un groupe de disponibilité

Une fois que votre cluster a été créé ou intégré, créez le groupe de disponibilité à l’aide du portail Azure. Pour ce faire, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à votre ressource [machines virtuelles SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Sélectionnez **Haute disponibilité** sous **Paramètres**. 
1. Sélectionnez **+ Nouveau groupe de disponibilité Always On** pour ouvrir la page **Créer un groupe de disponibilité**.

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

1. Entrez un nom pour le groupe de disponibilité. 
1. Sélectionnez **Configurer l’écouteur** pour ouvrir la page **Configurer l’écouteur de groupe de disponibilité**. 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

1. Renseignez les valeurs, puis utilisez un équilibreur de charge existant ou sélectionnez **Créer nouveau** pour créer un équilibreur de charge.  Sélectionnez **Appliquer** pour enregistrer vos paramètres et créer l’écouteur et l’équilibreur de charge. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

1. Choisissez **+ Sélectionner un réplica** pour ouvrir la page **Configurer les réplicas du groupe de disponibilité**.
1. Sélectionnez les machines virtuelles que vous souhaitez ajouter au groupe de disponibilité, puis choisissez les paramètres de groupe de disponibilité qui répondent le mieux aux besoins de votre entreprise. Sélectionnez **Appliquer** pour enregistrer vos paramètres. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

1. Vérifiez les paramètres de votre groupe de disponibilité, puis sélectionnez **Appliquer** pour créer votre groupe de disponibilité. 

Vous pouvez vérifier l’état de votre déploiement dans le **journal d’activité**, lequel est accessible à partir de l’icône en forme de cloche dans la barre de navigation supérieure. 

  > [!NOTE]
  > Votre **intégrité de synchronisation** sur la page **Haute disponibilité** du portail Azure apparaîtra comme **Non saine** jusqu’à ce que vous ajoutiez des bases de données à votre groupe de disponibilité. 


## <a name="add-database-to-availability-group"></a>Ajouter une base de données au groupe de disponibilité

Ajoutez vos bases de données à votre groupe de disponibilité une fois le déploiement terminé. Les étapes ci-dessous utilisent SQL Server Management Studio (SSMS), mais vous pouvez également utiliser [Transact-SQL ou PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database). 

Pour ajouter des bases de données à votre groupe de disponibilité à l’aide de SQL Server Management Studio, procédez comme suit :

1. Connectez-vous à l’une de vos machines virtuelles SQL Server à l’aide de la méthode de votre choix, telle que Connexion Bureau à distance (RDP). 
1. Ouvrez SQL Server Management Studio (SSMS).
1. Connectez-vous à votre instance SQL Server. 
1. Développez **Haute disponibilité Always On** dans **Explorateur d’objets**.
1. Développez **Groupes de disponibilité**, cliquez avec le bouton droit sur votre groupe de disponibilité et choisissez **Ajouter une base de données…**

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

1. Suivez les invites pour sélectionner la ou les bases de données que vous souhaitez ajouter à votre groupe de disponibilité. 
1. Sélectionnez **OK** pour enregistrer vos paramètres et ajouter votre base de données au groupe de disponibilité. 
1. Une fois la base de données ajoutée, actualisez **Explorateur d’objets** pour confirmer que l’état de votre base de données est `synchronized`. 

Une fois les bases de données ajoutées, vous pouvez vérifier l’état de votre groupe de disponibilité dans le portail Azure : 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

## <a name="add-more-vms"></a>Ajouter d’autres machines virtuelles

Pour ajouter d’autres machines virtuelles SQL Server au cluster, procédez comme suit : 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à votre ressource [machines virtuelles SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Sélectionnez **Haute disponibilité** sous **Paramètres**. 
1. Sélectionnez **Configurer le cluster de basculement Windows Server** pour ouvrir la page **Configurer le cluster de basculement Windows**. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

1. Développez **Informations d’identification du cluster de basculement Windows Server** et entrez les comptes utilisés pour le service SQL Server, l’opérateur de cluster et le démarrage de cluster. 
1. Sélectionnez les machines virtuelles SQL Server que vous souhaitez ajouter au cluster. 
1. Sélectionnez **Appliquer**. 

Vous pouvez vérifier l’état de votre déploiement dans le **journal d’activité**, lequel est accessible à partir de l’icône en forme de cloche dans la barre de navigation supérieure. 


## <a name="modify-availability-group"></a>Modifier le groupe de disponibilité 


Vous pouvez **ajouter des réplicas** au groupe de disponibilité, **configurer l’écouteur** ou **supprimer l’écouteur** à partir de la page **Haute disponibilité** dans le portail Azure en sélectionnant le bouton de sélection (…) à côté de votre groupe de disponibilité : 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail":::

## <a name="remove-cluster"></a>Supprimer le cluster

Supprimez toutes les machines virtuelles SQL Server du cluster pour le détruire, puis supprimez les métadonnées de cluster du fournisseur de ressources de machine virtuelle SQL. Pour ce faire, vous pouvez utiliser la version la plus récente d’[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou de PowerShell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Tout d’abord, supprimez toutes les machines virtuelles SQL Server du cluster. Cette opération supprime physiquement les nœuds du cluster et détruit le cluster :  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

S’il s’agit des seules machines virtuelles du cluster, le cluster est détruit. S’il existe dans le cluster d’autres machines que les machines virtuelles SQL Server qui ont été supprimées, ces autres machines virtuelles ne seront pas supprimées et le cluster ne sera pas détruit. 

Ensuite, supprimez les métadonnées relatives au cluster du fournisseur de ressources de machine virtuelle SQL : 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tout d’abord, supprimez toutes les machines virtuelles SQL Server du cluster. Cette opération supprime physiquement les nœuds du cluster et détruit le cluster : 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

S’il s’agit des seules machines virtuelles du cluster, le cluster est détruit. S’il existe dans le cluster d’autres machines que les machines virtuelles SQL Server qui ont été supprimées, ces autres machines virtuelles ne seront pas supprimées et le cluster ne sera pas détruit. 


Ensuite, supprimez les métadonnées relatives au cluster du fournisseur de ressources de machine virtuelle SQL : 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez des problèmes, vous pouvez vérifier l’historique de déploiement et passer en revue les erreurs courantes ainsi que leurs résolutions. 

### <a name="check-deployment-history"></a>Vérifier l’historique de déploiement

Les modifications apportées au cluster et au groupe de disponibilité via le portail sont effectuées par le biais de déploiements. L’historique de déploiement peut fournir plus de détails en cas de problème lors de la création ou de l’intégration du cluster, ou lors de la création du groupe de disponibilité.

Pour afficher les journaux du déploiement et vérifier l’historique de déploiement, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à votre groupe de ressources.
1. Sélectionnez **Déploiements** sous **Paramètres**.
1. Sélectionnez le déploiement qui vous intéresse pour en savoir plus sur celui-ci. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Créer un cluster en sélectionnant l’option + Nouveau cluster dans le portail" :::

### <a name="common-errors"></a>Erreurs courantes

Passez en revue les erreurs courantes suivantes et leurs résolutions. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>Le compte utilisé pour démarrer le service SQL n’est pas un compte de domaine

Cela indique que le fournisseur de ressources n’a pas pu accéder au service SQL Server avec les informations d’identification fournies. Voici quelques solutions courantes :
- Vérifiez que votre contrôleur de domaine est en cours d’exécution.
- Vérifiez que les informations d’identification fournies dans le portail correspondent à celles du service SQL Server. 


## <a name="next-steps"></a>Étapes suivantes


Pour plus d’informations sur les groupes de disponibilité, consultez :

- [Vue d’ensemble des groupes de disponibilité](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Administration d’un groupe de disponibilité](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Supervision de groupes de disponibilité &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Instructions Transact-SQL de groupe de disponibilité ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Commandes PowerShell des groupes de disponibilité](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

Pour plus d’informations sur les machines virtuelles SQL Server, consultez : 

* [Vue d’ensemble des machines virtuelles SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Notes de publication pour les machines virtuelles SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Questions fréquentes (FAQ) sur les machines virtuelles SQL Server](frequently-asked-questions-faq.md)
