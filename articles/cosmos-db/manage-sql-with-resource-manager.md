---
title: Créer et gérer Azure Cosmos DB avec des modèles Resource Manager
description: Utiliser des modèles Azure Resource Manager pour créer et configurer l’API Azure Cosmos DB pour l’API SQL (Core)
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: ce9ca202ee57fe9c42906ebeb62a44c724bd1a52
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978724"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Gérer les ressources de l’API SQL (Core) Azure Cosmos DB avec des modèles Azure Resource Manager

Dans cet article, vous apprenez à utiliser des modèles Azure Resource Manager pour contribuer à automatiser la gestion de bases de données, conteneurs et comptes Azure Cosmos DB.

Cet article présente uniquement des exemples de modèles Azure Resource Manager pour les comptes d’API SQL. Vous trouverez également des exemples de modèles pour les API [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md) et [Table](manage-table-with-resource-manager.md).

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Créer un compte, une base de données et un conteneur Azure Cosmos

Le modèle Azure Resource Manager ci-dessous crée un compte Azure Cosmos avec :

* Deux conteneurs qui partagent un débit de 400 unités de requête par seconde (RU/s) au niveau de la base de données.
* Un conteneur avec un débit de 400 RU/s dédié.

Pour créer les ressources Azure Cosmos DB, copiez l’exemple de modèle suivant et déployez-le comme décrit, au moyen de [PowerShell](#deploy-via-powershell) ou d’[Azure CLI](#deploy-via-azure-cli).

* Si vous le souhaitez, vous pouvez consulter la [galerie Démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) et déployer le modèle à partir du portail Azure.
* Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un modèle et spécifier le chemin d’accès local avec le paramètre `--template-file`.

> [!IMPORTANT]
>
> * Quand vous ajoutez ou supprimez des emplacements dans un compte Azure Cosmos, vous ne pouvez pas simultanément modifier d’autres propriétés. Ces opérations doivent être effectuées séparément.
> * Les noms de compte sont limités à 44 caractères, tout en minuscules.
> * Pour changer les valeurs de débit, renvoyez le modèle avec les valeurs de RU/s mises à jour.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> Pour créer un conteneur avec une grande clé de partition, modifiez le modèle précédent en incluant la propriété `"version":2` dans l’objet `partitionKey`.

### <a name="deploy-via-powershell"></a>Déployer avec PowerShell

Pour utiliser PowerShell afin de déployer le modèle Azure Resource Manager :

1. **Copiez** le script.
2. Sélectionnez **Essayer** pour ouvrir Azure Cloud Shell.
3. Cliquez avec le bouton droit dans la fenêtre Azure Cloud Shell, puis sélectionnez **Coller**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Vous pouvez choisir de déployer le modèle avec une version installée localement de PowerShell au lieu d’Azure Cloud Shell. Vous devez alors [installer le module Azure PowerShell](/powershell/azure/install-az-ps). Exécutez `Get-Module -ListAvailable Az` pour trouver la version requise.

### <a name="deploy-via-azure-cli"></a>Déployer avec Azure CLI

Pour utiliser Azure CLI afin de déployer le modèle Azure Resource Manager :

1. **Copiez** le script.
2. Sélectionnez **Essayer** pour ouvrir Azure Cloud Shell.
3. Cliquez avec le bouton droit dans la fenêtre Azure Cloud Shell, puis sélectionnez **Coller**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

La commande `az cosmosdb show` montre le compte Azure Cosmos créé, une fois que ce dernier a été provisionné. Vous pouvez choisir de déployer le modèle à l’aide d’une version installée localement d’Azure CLI au lieu d’Azure Cloud Shell. Pour plus d’informations, consultez l’article [Interface de ligne de commande Azure (CLI)](/cli/azure/).

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Créer un conteneur Azure Cosmos DB avec des fonctionnalités côté serveur

Vous pouvez utiliser un modèle Azure Resource Manager pour créer un conteneur Azure Cosmos DB avec une procédure stockée, un déclencheur et une fonction définie par l’utilisateur.

Copiez l’exemple de modèle suivant et déployez-le comme décrit, avec [PowerShell](#deploy-with-powershell) ou [Azure CLI](#deploy-with-azure-cli).

* Si vous le souhaitez, vous pouvez consulter la [galerie Démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) et déployer le modèle à partir du portail Azure.
* Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un modèle et spécifier le chemin d’accès local avec le paramètre `--template-file`.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-with-powershell"></a>Déployer avec PowerShell

Pour utiliser PowerShell afin de déployer le modèle Azure Resource Manager :

1. **Copiez** le script.
1. Sélectionnez **Essayer** pour ouvrir Azure Cloud Shell.
1. Cliquez avec le bouton droit sur la fenêtre Azure Cloud Shell, puis sélectionnez **Coller**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Vous pouvez choisir de déployer le modèle avec une version installée localement de PowerShell au lieu d’Azure Cloud Shell. Vous devez alors [installer le module Azure PowerShell](/powershell/azure/install-az-ps). Exécutez `Get-Module -ListAvailable Az` pour trouver la version requise.

### <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

Pour utiliser Azure CLI afin de déployer le modèle Azure Resource Manager :

1. **Copiez** le script.
2. Sélectionnez **Essayer** pour ouvrir Azure Cloud Shell.
3. Cliquez avec le bouton droit dans la fenêtre Azure Cloud Shell, puis sélectionnez **Coller**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

* [Documentation Azure Resource Manager](/azure/azure-resource-manager/)
* [Schéma de fournisseurs de ressources Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modèles Démarrage rapide Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
