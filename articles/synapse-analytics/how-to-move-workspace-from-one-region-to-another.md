---
title: Déplacer un espace de travail Azure Synapse Analytics d’une région à une autre
description: Cet article explique comment déplacer un espace de travail Azure Synapse Analytics d’une région vers une autre.
author: phanir
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 08/16/2021
ms.author: phanir
ms.reviewer: jrasnick
ms.openlocfilehash: 015128d986ab0e32a1377da8b91c319895264aa9
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310295"
---
# <a name="move-an-azure-synapse-analytics-workspace-from-one-region-to-another"></a>Déplacer un espace de travail Azure Synapse Analytics d’une région à une autre

Cet article est un guide pas à pas qui vous montre comment déplacer un espace de travail Azure Synapse Analytics d’une région Azure vers une autre.

> [!NOTE]
> Les étapes décrites dans cet article ne déplacent pas réellement l’espace de travail. Les étapes vous montrent comment créer un espace de travail dans une nouvelle région à l’aide d’Azure Synapse Analytics - sauvegardes et artefacts dédiés du pool SQL de la région source.

## <a name="prerequisites"></a>Prérequis

- Intégrez l’espace de travail Azure Synapse de la région source avec Azure DevOps ou GitHub. Pour plus d’informations, consultez [Contrôle de code source dans Synapse Studio](cicd/source-control.md).
- Les modules [Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-6.3.0&preserve-view=true) et [Azure CLI](/cli/azure/install-azure-cli) doivent être installés sur le serveur sur lequel les scripts sont exécutés.
- Assurez-vous que tous les services dépendants, par exemple, Azure Machine Learning, le stockage Azure et les hubs de liaison privée Azure, sont recréés dans la région cible ou déplacés vers la région cible si le service prend en charge un déplacement de région. 
- Déplacer un Stockage Azure vers une autre région. Pour plus d’informations, consultez [Déplacer un compte Stockage Azure vers une autre région](../storage/common/storage-account-move.md).
- Vérifiez que le nom du pool SQL dédié et le nom du pool Apache Spark sont identiques dans la région source et l’espace de travail de la région cible.

## <a name="scenarios-for-a-region-move"></a>Scénarios d’un déplacement dans une région

- **Nouvelles exigences en matière de conformité** : les organisations requièrent que les données et les services soient placés dans la même région dans le cadre de nouvelles exigences de conformité.
- **Disponibilité d’une nouvelle région Azure** : scénarios dans lesquels une nouvelle région Azure est disponible et où les besoins du projet ou commerciaux requièrent un déplacement de l’espace de travail et d’autres ressources Azure vers la nouvelle région Azure disponible.
- **Région sélectionnée incorrecte** : une région incorrecte a été sélectionnée lors de la création des ressources Azure.

## <a name="steps-to-move-an-azure-synapse-workspace-to-another-region"></a>Étapes pour déplacer un espace de travail Azure Synapse vers une autre région

Le déplacement d’un espace de travail Azure Synapse d’une région vers une autre est un processus à plusieurs étapes. Les étapes principales sont les suivantes :

1. Créez un nouvel espace de travail Azure Synapse dans la région cible, ainsi qu’un pool Spark avec les mêmes configurations que celles utilisées dans l’espace de travail de la région source.
1. Restaurez le pool SQL dédié dans la région cible à l’aide de points de restauration ou de géo-sauvegardes.
1. Recréez toutes les connexions requises sur le nouveau Serveur SQL logique.
1. Créez des objets et des bases de données de pool SQL et Spark sans serveur.
1. Ajoutez un Principal de Service Azure DevOps au rôle d’Éditeur d’artefact Synapse de contrôle de l’accès en fonction du rôle (RBAC) Azure synapse si vous utilisez un pipeline de version Azure DevOps pour déployer les artefacts.
1. Déployez l’artefact de code (Scripts SQL , Blocs-notes), les services liés, les pipelines, les jeux de données, les déclencheurs de définitions de tâche Spark et les informations d’identification des pipelines de version Azure DevOps vers l’espace de travail Azure Synapse de la région cible.
1. Ajoutez des utilisateurs ou des groupes du Répertoire actif Azure (Azure AD) aux rôles RBAC Azure Synapse. Attribution d’un accès Contributeur de Stockage Blob à l’identité managée affectée par le système (SA-MI) sur le Stockage Azure et le Coffre de clés Azure Key si vous vous authentifiez à l’aide de l’identité managée.
1. Attribuer les rôles de Lecteur de Stockage blob ou de Contributeur de Stockage blob à des utilisateurs requis Azure AD sur le stockage attaché par défaut ou sur le compte Stockage dont les données doivent être interrogées à l’aide d’un pool SQL sans serveur.
1. Recréer un runtime d’intégration auto-hébergé (SHIR).
1. Téléchargez manuellement toutes les bibliothèques et fichiers jar nécessaires dans l’espace de travail Azure Synapse cible.
1. Créez tous les points de terminaison privés managés si l’espace de travail est déployé dans un réseau virtuel managé.
1. Testez le nouvel espace de travail sur la région cible et mettez à jour toutes les entrées DNS qui pointent vers l’espace de travail de la région source.
1. Si une connexion de point de terminaison privée est créée sur l’espace de travail source, créez-en une dans l’espace de travail de la région cible.
1. Vous pouvez supprimer l’espace de travail dans la région source après l’avoir testé minutieusement et router toutes les connexions à l’espace de travail de la région cible.

## <a name="step-1-create-an-azure-synapse-workspace-in-a-target-region"></a>Étape 1 : créer un espace de travail Azure Synapse dans une région cible

Dans cette section, vous allez créer l’espace de travail Azure Synapse à l’aide d’Azure PowerShell, de l’interface CLI Azure et du portail Azure. Vous allez créer un groupe de ressources avec un compte Azure Data Lake Storage Gen2 qui sera utilisé comme stockage par défaut pour l’espace de travail dans le cadre du script PowerShell et du script CLI. Si vous souhaitez automatiser le processus de déploiement, appelez ces scripts PowerShell ou CLI à partir du pipeline de mise en production DevOps.

### <a name="azure-portal"></a>Portail Azure
Pour créer un espace de travail à partir du portail Azure, suivez les étapes de [Démarrage rapide : créer un espace de travail Synapse](quickstart-create-workspace.md).

### <a name="azure-powershell"></a>Azure PowerShell 
Le script suivant crée le groupe de ressources et l’espace de travail Azure Synapse à l’aide des applets de commande New-AzResourceGroup et New-AzSynapseWorkspace.

#### <a name="create-a-resource-group"></a>Créer un groupe de ressources

```powershell
$storageAccountName= "<YourDefaultStorageAccountName>"
$resourceGroupName="<YourResourceGroupName>"
$regionName="<YourTargetRegionName>"
$containerName="<YourFileSystemName>" # This is the file system name
$workspaceName="<YourTargetRegionWorkspaceName>"

$sourcRegionWSName="<Your source region workspace name>"
$sourceRegionRGName="<YourSourceRegionResourceGroupName>"
$sqlUserName="<SQLUserName>"
$sqlPassword="<SQLStrongPassword>"

$sqlPoolName ="<YourTargetSQLPoolName>" #Both Source and target workspace SQL pool name will be same
$sparkPoolName ="<YourTargetWorkspaceSparkPoolName>"
$sparkVersion="2.4"

New-AzResourceGroup -Name $resourceGroupName -Location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>Créer un compte Data Lake Storage Gen2

```powershell
#If the Storage account is already created, then you can skip this step.
New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location  $regionName `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $true 
```


#### <a name="create-an-azure-synapse-workspace"></a>Créer un espace de travail Azure Synapse

```powershell
$password = ConvertTo-SecureString $sqlPassword -AsPlainText -Force
$creds = New-Object System.Management.Automation.PSCredential ($sqlUserName, $password)

New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds 
```

Si vous souhaitez créer l’espace de travail avec un Réseau virtuel managé, ajoutez le paramètre supplémentaire « ManagedVirtualNetwork » au script. Pour en savoir plus sur les options disponibles, consultez [Configuration de réseau virtuel managé](/powershell/module/az.synapse/new-azsynapsemanagedvirtualnetworkconfig?view=azps-6.3.0&preserve-view=true).


```powershell
#Creating a managed virtual network configuration
$config = New-AzSynapseManagedVirtualNetworkConfig -PreventDataExfiltration -AllowedAadTenantIdsForLinking ContosoTenantId 

#Creating an Azure Synapse workspace
New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds `
                              -ManagedVirtualNetwork $config
```


### <a name="azure-cli"></a>Azure CLI

Ce script de l’interface CLI Azure crée un groupe de ressources, un compte Data Lake Storage Gen2 et un système de fichiers. Ensuite, il crée l’espace de travail Azure synapse.

#### <a name="create-a-resource-group"></a>Créer un groupe de ressources

```azurecli
az group create --name $resourceGroupName --location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>Créer un compte Data Lake Storage Gen2

```azurecli
# Checking if name is not used only then creates it.

$StorageAccountNameAvailable=(az storage account check-name --name $storageAccountName --subscription $subscriptionId | ConvertFrom-Json).nameAvailable

if($StorageAccountNameAvailable)
{
Write-Host "Storage account Name is available to be used...creating storage account"

#Creating an Data Lake Storage Gen2 account
$storgeAccountProvisionStatus=az storage account create `
  --name $storageAccountName `
  --resource-group $resourceGroupName `
  --location $regionName `
  --sku Standard_GRS `
  --kind StorageV2 `
  --enable-hierarchical-namespace $true

($storgeAccountProvisionStatus| ConvertFrom-Json).provisioningState
}
else
{
    Write-Host "Storage account Name is NOT available to be used...use another name --    exiting the script..."
    EXIT
}

#Creating a container in a Data Lake Storage Gen2 account

$key=(az storage account keys list -g $resourceGroupName -n $storageAccountName|ConvertFrom-Json)[0].value

$fileShareStatus=(az storage share create --account-name $storageAccountName --name $containerName --account-key $key)

if(($fileShareStatus|ConvertFrom-Json).created -eq "True")
{
      Write-Host f"Successfully created the fileshare - '$containerName'"
}
```


#### <a name="create-an-azure-synapse-workspace"></a>Créer un espace de travail Azure Synapse

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $containerName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName
```
Pour activer un réseau virtuel managé, incluez le paramètre `--enable-managed-virtual-network` dans le script précédent. Pour plus d’options, consultez [réseau virtuel de l’espace de travail managé](/cli/azure/synapse/workspace?view=azure-cli-latest&preserve-view=true).

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $FileShareName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName `
  --enable-managed-virtual-network true `
  --allowed-tenant-ids "Contoso"
```

## <a name="step-2-create-an-azure-synapse-workspace-firewall-rule"></a>Étape 2 : créer une règle de pare-feu d’espace de travail Azure Synapse 
Une fois l’espace de travail créé, ajoutez les règles de pare-feu pour l’espace de travail. Limitez les adresses IP à une certaine plage. Vous pouvez ajouter un pare-feu à partir du portail Azure ou à l’aide de PowerShell ou de l’interface CLI.

### <a name="azure-portal"></a>Portail Azure
Sélectionnez les options de pare-feu et ajoutez la plage d’adresses IP comme indiqué dans la capture d’écran suivante.

:::image type="icon" source="media/how-to-move-workspace-from-one-region-to-another/firewall.png" border="false":::


### <a name="azure-powershell"></a>Azure PowerShell 
Exécutez les commandes PowerShell suivantes pour ajouter des règles de pare-feu en spécifiant les adresses IP de début et de fin. Mettez à jour la plage d’adresses IP conformément à vos besoins.


```powershell
$WorkspaceWeb = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Web
$WorkspaceDev = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Dev

# Adding firewall rules
$FirewallParams = @{
  WorkspaceName = $workspaceName
  Name = 'Allow Client IP'
  ResourceGroupName = $resourceGroup
  StartIpAddress = "0.0.0.0"
  EndIpAddress = "255.255.255.255"
}
New-AzSynapseFirewallRule @FirewallParams
```

Exécutez le script suivant pour mettre à jour les paramètres de contrôle SQL de l’identité managée de l’espace de travail :

```powershell 
Set-AzSynapseManagedIdentitySqlControlSetting -WorkspaceName $workspaceName -Enabled $true 
```

### <a name="azure-cli"></a>Azure CLI


```azurecli
az synapse workspace firewall-rule create --name allowAll --workspace-name $workspaceName  `
--resource-group $resourceGroupName --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
 
Exécutez le script suivant pour mettre à jour les paramètres de contrôle SQL de l’identité managée de l’espace de travail :

```azurecli
az synapse workspace managed-identity grant-sql-access `
--workspace-name $workspaceName --resource-group $resourceGroupName
```


## <a name="step-3-create-an-apache-spark-pool"></a>Etape 3 : Créer un pool Apache Spark

Créez le pool Spark avec la même configuration que celle utilisée dans l’espace de travail de la région source.

### <a name="azure-portal"></a>Portail Azure

Pour créer un pool Spark à partir du portail Azure, consultez [Démarrage rapide : créer un nouveau pool Apache Spark sans serveur à l’aide du portail Azure](quickstart-create-apache-spark-pool-portal.md).


Vous pouvez également créer le pool Spark à partir de Synapse Studio en suivant les étapes décrites dans [Démarrage rapide : créer un pool de Apache Spark sans serveur à l’aide de Synapse Studio](quickstart-create-apache-spark-pool-studio.md).

### <a name="azure-powershell"></a>Azure PowerShell

Le script suivant crée un pool Spark avec deux workers et un nœud de pilote. Mettez à jour les valeurs pour qu’elles correspondent au pool Spark de l’espace de travail de votre région source.


```powershell
#Creating a Spark pool with 3 nodes (2 worker + 1 driver) and a small cluster size with 4 cores and 32 GB RAM. 
New-AzSynapseSparkPool `
    -WorkspaceName  $workspaceName `
    -Name $sparkPoolName `
    -NodeCount 3 `
    -SparkVersion $sparkVersion `
    -NodeSize Small
```
 
### <a name="azure-cli"></a>Azure CLI

```azurecli
az synapse spark pool create --name $sparkPoolName --workspace-name $workspaceName --resource-group $resourceGroupName `
--spark-version $sparkVersion --node-count 3 --node-size small
```

## <a name="step-4-restore-a-dedicated-sql-pool"></a>Etape 4 : Restaurer un pool SQL dédié 

### <a name="restore-from-geo-redundant-backups"></a>Restauration à partir de sauvegardes géo-redondantes

Pour restaurer les pools SQL dédiés à partir de la géo-sauvegarde à l’aide du portail Azure et de PowerShell, consultez [Géo-restauration d’un pool SQL dédié dans Azure Synapse Analytics](sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup.md).


### <a name="restore-by-using-restore-points-from-the-source-region-workspace-dedicated-sql-pool"></a>Restaurer à l’aide de points de restauration à partir du pool SQL dédié de l’espace de travail de la région source

Restaurez le pool SQL dédié dans l’espace de travail de la région cible à l’aide du point de restauration du pool SQL dédié de l’espace de travail de la région source. Vous pouvez utiliser le portail Azure, Synapse Studio ou PowerShell pour effectuer une restauration à partir de points de restauration. Si la région source n’est pas accessible, vous ne pouvez pas effectuer de restauration à l’aide de cette option.

#### <a name="synapse-studio"></a>Synapse Studio

A partir de Synapse Studio, vous pouvez restaurer le pool SQL dédié à partir de n’importe quel espace de travail de l’abonnement à l’aide de *points de restauration*. Lorsque vous créez le pool SQL dédié, sous **Paramètres supplémentaires**, sélectionnez **Point de restauration** et sélectionnez l’espace de travail comme indiqué dans la capture d’écran suivante. Si vous avez créé un point de restauration défini par l’utilisateur, utilisez-le pour restaurer le pool SQL. Dans le cas contraire, vous pouvez sélectionner le dernier point de restauration automatique.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/restore-sql-pool.png" alt-text="Restauration du pool SQL":::


#### <a name="azure-powershell"></a>Azure PowerShell

Exécutez le script PowerShell suivant pour restaurer l’espace de travail. Ce script utilise le dernier point de restauration du pool SQL dédié de l’espace de travail source pour restaurer le pool SQL sur l’espace de travail cible. Avant d’exécuter le script, mettez à jour le niveau de performance de DW100c vers la valeur requise. 

> [!IMPORTANT]
> Le nom du pool SQL dédié doit être le même sur les deux espaces de travail.


```powershell
#Getting the restore points
$restorePoint=Get-AzSynapseSqlPoolRestorePoint -WorkspaceName $sourceRegionWSName -Name $sqlPoolName|Sort-Object  -Property RestorePointCreationDate -Descending `
                                                                                         | SELECT RestorePointCreationDate -ExpandProperty  RestorePointCreationDate -First 1
```
 
 

```powershell
<#
Transform Synapse SQL pool resource ID to SQL database ID because currently the command only accepts the SQL database ID. 
For example: /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Sql/servers/<WorkspaceName>/databases/<DatabaseName>
#>
$pool = Get-AzSynapseSqlPool -ResourceGroupName $sourceRegionRGName -WorkspaceName $sourcRegionWSName -Name $sqlPoolName
$databaseId = $pool.Id `
    -replace "Microsoft.Synapse", "Microsoft.Sql" `
    -replace "workspaces", "servers" `
    -replace "sqlPools", "databases" 
 

$restoredPool = Restore-AzSynapseSqlPool -FromRestorePoint `
                                         -RestorePoint $restorePoint `
                                         -TargetSqlPoolName $sqlPoolName `
                                         -ResourceGroupName $resourceGroupName `
                                         -WorkspaceName $workspaceName `
                                         -ResourceId $databaseId `
                                         -PerformanceLevel DW100c -AsJob


#Tracks the status of the restore 

Get-Job | Where-Object Command -In ("Restore-AzSynapseSqlPool") | `
Select-Object Id,Command,JobStateInfo,PSBeginTime,PSEndTime,PSJobTypeName,Error |Format-Table
```
Une fois le pool SQL dédié restauré, créez toutes les connexions SQL dans Azure Synapse. Pour créer toutes les connexions, suivez les étapes dans [Créer une connexion](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="step-5-create-a-serverless-sql-pool-spark-pool-and-objects"></a>Etape 5 : créer un pool SQL sans serveur, un pool Spark et des objets

Vous ne pouvez ni sauvegarder ni restaurer les bases de données du pool SQL sans serveur et des pools Spark. En guise de solution de contournement possible, vous pouvez :

1. Créez des blocs-notes et des scripts de SQL, qui comportent le code permettant de recréer tous les pools Spark requis, les bases de données des pools SQL sans serveur, les tables, les rôles et les utilisateurs avec toutes les attributions de rôles. Vérifiez ces artefacts pour Azure DevOps ou GitHub.
1. Si le nom du compte Stockage est modifié, assurez-vous que les artefacts de code pointent vers le nom de compte de stockage correct.
1. Créer des pipelines, qui appellent ces artefacts de code dans une séquence spécifique. Lorsque ces pipelines sont exécutés sur l’espace de travail de la région cible, les bases de données SQL Spark, les bases de données du pool SQL sans serveur, les sources de données externes, les vues, les rôles, les utilisateurs et les autorisations seront créés dans l’espace de travail de la région cible.
1. Lorsque vous intégrez l’espace de travail de la région source à Azure DevOps, ces artefacts de code feront partie du référentiel. Ultérieurement, vous pouvez déployer ces artefacts de code dans l’espace de travail de la région cible à l’aide du pipeline de mise en production DevOps, comme indiqué à l’étape 6.
1. Dans l’espace de travail de la région cible, déclenchez ces pipelines manuellement.

## <a name="step-6-deploy-artifacts-and-pipelines-by-using-cicd"></a>Étape 6 : Déployer des artefacts et des pipelines à l’aide de CI/CD 

 Pour savoir comment intégrer un espace de travail Azure Synapse avec Azure DevOps ou GitHub et comment déployer les artefacts dans l’espace de travail d’une région cible, suivez les étapes dans [Intégration continue et de livraison continue (CI/CD) pour un espace de travail Azure Synapse](cicd/continuous-integration-deployment.md). 

Une fois que l’espace de travail est intégré à Azure DevOps, vous trouverez une branche portant le nom workspace_publish. Cette branche contient le modèle d’espace de travail qui inclut des définitions pour les artefacts tels que les Blocs-notes, les Scripts SQL, les Jeux de données, les Services liés, les Pipelines, les Déclencheurs et la définition de tâche Spark.

Cette capture d’écran du référentiel Azure DevOps affiche les fichiers de modèle de l’espace de travail pour les artefacts et autres composants.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/devops-repo-workspace-publish.png" alt-text="Capture d’écran montrant la publication de l’espace de travail.":::

Vous pouvez utiliser le modèle de l’espace de travail pour déployer des artefacts et des pipelines dans un espace de travail à l’aide du pipeline de mise en production Azure DevOps, comme indiqué dans la capture d’écran suivante.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/release-pipeline.png" alt-text="Capture d’écran qui montre la version synapse-mise en production-pipeline.":::

Si l’espace de travail n’est pas intégré à GitHub ou Azure DevOps, vous devez recréer ou écrire manuellement des scripts PowerShell ou Azure CLI personnalisés pour déployer tous les artefacts, pipelines, services liés, informations d’identification, déclencheurs et définitions Spark dans l’espace de travail de la région cible.


> [!NOTE]
> Ce processus nécessite que vous ayez à mettre à jour les pipelines et les artefacts de code pour inclure toutes les modifications apportées à Spark et aux pools SQL sans serveur, aux objets et aux rôles dans les espaces de travail de la région source.

## <a name="step-7-create-a-shared-integration-runtime"></a>Étape 7 : Créer un runtime d’intégration partagé

Pour créer un SHIR, suivez les étapes dans [Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="step-8-assign-an-azure-role-to-managed-identity"></a>Étape 8 : Affecter un rôle Azure à une identité managée

 Affectez l'`Storage Blob Contributor`accès à l’identité gérée du nouvel espace de travail sur le compte Data Lake Storage Gen2 attaché par défaut. Attribuez également l’accès à d’autres comptes de stockage où SA-MI est utilisée pour l’authentification. Affectez `Storage Blob Contributor` ou `Storage Blob Reader` accédez aux utilisateurs et groupes Azure ad pour tous les comptes de stockage requis.

### <a name="azure-portal"></a>Portail Azure
Suivez les étapes décrites dans [Accorder des autorisations à l’identité managée de l’espace de travail ](security/how-to-grant-workspace-managed-identity-permissions.md) pour affecter un rôle Contributeur de données d’objets Blob de stockage à l’identité managée de l’espace de travail.

### <a name="azure-powershell"></a>Azure PowerShell
Affectez un rôle de Contributeur de données blob de stockage à l’identité managée de l’espace de travail.

```powershell

$workSpaceIdentityObjectID= (Get-AzSynapseWorkspace -ResourceGroupName $resourceGroupName -Name $workspaceName).Identity.PrincipalId 
$scope = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Storage/storageAccounts/$($storageAccountName)"

# Adding Storage Blob Data Contributor to WS Managed Identity on the storage account. This errors out with the message New-AzRoleAssignment : Exception of type 'Microsoft.Rest.Azure.CloudException' was thrown.
# But it creates the required permissions on the storage account.
$roleAssignedforManagedIdentity=New-AzRoleAssignment -ObjectId $workSpaceIdentityObjectID `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope $scope -ErrorAction SilentlyContinue
```


### <a name="azure-cli"></a>Azure CLI

```azurecli
# Getting Role name
$roleName =az role definition list --query "[?contains(roleName, 'Storage Blob Data Contributor')].{roleName:roleName}" --output tsv

#Getting resource id for storage account
$scope= (az storage account show --name $storageAccountName|ConvertFrom-Json).id

#Getting principal ID for WS Managed Identity
$workSpaceIdentityObjectID=(az synapse workspace show --name $workspaceName --resource-group $resourceGroupName|ConvertFrom-Json).Identity.PrincipalId 
                    
# Adding Storage Blob Data Contributor Azure role to SA-MI
az role assignment create --assignee $workSpaceIdentityObjectID `
--role $roleName `
--scope $scope
```

## <a name="step-9-assign-azure-synapse-rbac-roles"></a>Étape 9 : Attribuer des rôles RBAC Azure Synapse

Ajoutez tous les utilisateurs qui ont besoin d’accéder à l’espace de travail cible avec des rôles et des autorisations distincts. Le script PowerShell et CLI suivant ajoute un utilisateur Azure AD au rôle Administrateur Synapse dans l’espace de travail de la région cible. Pour obtenir tous les noms de rôle RBAC Azure Synapse, consultez [rôles RBAC Azure Synapse](security/synapse-workspace-synapse-rbac-roles.md).

### <a name="synapse-studio"></a>Synapse Studio

Pour ajouter ou supprimer des affectations Azure Synapse RBAC à partir de Synapse Studio, suivez les étapes dans [Gestion des attributions de rôles Azure Synapse RBAC dans Synapse Studio](security/how-to-manage-synapse-rbac-role-assignments.md).


### <a name="azure-powershell"></a>Azure PowerShell

Le script PowerShell suivant ajoute l’attribution de rôle Administrateur Synapse à un utilisateur ou un groupe Azure AD. Vous pouvez utiliser-RoleDefinitionId au lieu de-RoleDefinitionName avec la commande suivante pour ajouter les utilisateurs à l’espace de travail :

```powershell
# Add the Synapse RBAC assignment. Use the objectId of the Azure AD user or group you want to assign.
New-AzSynapseRoleAssignment `
   -WorkspaceName $workspaceName  `
   -RoleDefinitionName "Synapse Administrator" `
   -ObjectId 1c02d2a6-ed3d-46ec-b578-6f36da5819c6

# Check if user is added to the access control by running this command.
Get-AzSynapseRoleAssignment -WorkspaceName $workspaceName  
```

Pour accéder à ObjectIds et RoleIds dans l’espace de travail de la région source, exécutez la commande Get-AzSynapseRoleAssignment. Affectez les mêmes rôles RBAC Azure Synapse aux utilisateurs ou groupes Azure AD dans l’espace de travail de la région cible.

Au lieu d’utiliser-ObjectId comme paramètre, vous pouvez également utiliser-SignInName, où vous fournissez l’adresse de messagerie ou le nom d’utilisateur principal de l’utilisateur. Pour en savoir plus sur les options disponibles, consultez [applet de commande RBAC Azure Synapse - PowerShell](/powershell/module/az.synapse/new-azsynapseroleassignment?view=azps-6.3.0&preserve-view=true). 

### <a name="azure-cli"></a>Azure CLI

```azurecli
#Get the Object Id of the user and assign the required Azure Synapse RBAC permissions to the Azure AD user. You can provide the email address of the user (username@contoso.com) for the --assignee parameter.
az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Administrator" --assignee adasdasdd42-0000-000-xxx-xxxxxxx

az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Contributor" --assignee "user1@contoso.com"

```

Pour en savoir plus sur les options disponibles, consultez [RBAC - CLI Azure Synapse](/cli/azure/synapse/role/assignment?view=azure-cli-latest&preserve-view=true). 

## <a name="step-10-upload-workspace-packages"></a>Etape 10 : Télécharger des packages de l’espace de travail

Télécharger tous les packages d’espace de travail requis vers le nouvel espace de travail. Pour automatiser le processus de téléchargement des packages de l’espace de travail, consultez [Bibliothèque cliente des artefacts Microsoft Azure Synapse Analytics](https://www.nuget.org/packages/Azure.Analytics.Synapse.Artifacts/1.0.0-preview.10).

## <a name="step-11-permissions"></a>Étape 11 : Autorisations
    
Pour configurer le contrôle d’accès pour l’espace de travail Azure Synapse de la région cible, suivez les étapes décrites dans [Comment configurer le contrôle d’accès pour votre espace de travail Azure Synapse](security/how-to-set-up-access-control.md). 


## <a name="step-12-create-managed-private-endpoints"></a>Étape 12 : Créer des points de terminaison privés managés

Pour recréer les points de terminaison privés managés à partir de l’espace de travail de la région source dans votre espace de travail de la région cible, consultez [Créer un point de terminaison privé managé vers votre source de données](security/how-to-create-managed-private-endpoints.md). 


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [réseaux virtuels managés par Azure Synapse](security/synapse-workspace-managed-vnet.md).
- En savoir plus sur les [Points de terminaison privés managés Azure Synapse](security/synapse-workspace-managed-private-endpoints.md).
- En savoir plus sur la [Connexion aux ressources de l’espace de travail à partir d’un réseau restreint](security/how-to-connect-to-workspace-from-restricted-network.md).
