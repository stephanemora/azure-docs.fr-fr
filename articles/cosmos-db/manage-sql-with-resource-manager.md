---
title: Créer et gérer Azure Cosmos DB à l’aide de modèles Azure Resource Manager
description: Utiliser des modèles Azure Resource Manager pour créer et configurer Azure Cosmos DB pour SQL API (principale)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: a3798ac0c73c7bc6c4012dbb089275254f4c3504
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968835"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Gérer les ressources de SQL Azure Cosmos DB (Core) API à l’aide de modèles Azure Resource Manager

## Créer un compte Azure Cosmos, la base de données et le conteneur <a id="create-resource"></a>

Créer des ressources Azure Cosmos DB à l’aide d’un modèle Azure Resource Manager. Ce modèle crée un compte Azure Cosmos avec deux conteneurs qui partagent un débit de 400 RU/s au niveau de la base de données. Copiez le modèle et les déployer comme indiqué ci-dessous ou visitez [galerie de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) et déployer à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un nouveau modèle et spécifiez le chemin d’accès local avec le `--template-file` paramètre.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Déployer à l’aide de PowerShell

Pour déployer le modèle Resource Manager à l’aide de PowerShell, **copie** le script et sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$container1Name = Read-Host -Prompt "Enter the first container name"
$container2Name = Read-Host -Prompt "Enter the second container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -container1Name $container1Name `
    -container2Name $container2Name

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Si vous choisissez d’utiliser une version installée localement de PowerShell plutôt qu’à partir d’Azure Cloud shell, vous devez [installer](/powershell/azure/install-az-ps) le module Azure PowerShell. Exécutez `Get-Module -ListAvailable Az` pour trouver la version.

### <a name="deploy-via-azure-cli"></a>Déployer via l’interface CLI Azure

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first container name: ' container1Name
read -p 'Enter the second container name: ' container2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   container1Name=$container1Name container2Name=$container2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Le `az cosmosdb show` commande montre le compte Azure Cosmos nouvellement créé, une fois que ce dernier a été configuré. Si vous choisissez d’utiliser une version installée localement d’Azure CLI au lieu d’utiliser CloudShell, consultez [les Interface de ligne de commande (CLI) Azure](/cli/azure/) article.

## Mettre à jour de débit (RU/s) sur une base de données <a id="database-ru-update"></a>

Le modèle suivant met à jour le débit d’une base de données. Copiez le modèle et les déployer comme indiqué ci-dessous ou visitez [galerie de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-database-ru-update/) et déployer à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un nouveau modèle et spécifiez le chemin d’accès local avec le `--template-file` paramètre.

[!code-json[cosmosdb-sql-database-ru-update](~/quickstart-templates/101-cosmosdb-sql-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-powershell"></a>Déployer un modèle de base de données par le biais de PowerShell

Pour déployer le modèle Resource Manager à l’aide de PowerShell, **copie** le script et sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$throughput = Read-Host -Prompt "Enter new throughput for database"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -throughput $throughput
```

### <a name="deploy-database-template-via-azure-cli"></a>Déployer un modèle de base de données via l’interface CLI Azure

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Mettre à jour de débit (RU/s) sur un conteneur <a id="container-ru-update"></a>

Le modèle suivant met à jour le débit d’un conteneur. Copiez le modèle et les déployer comme indiqué ci-dessous ou visitez [galerie de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-ru-update/) et déployer à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un nouveau modèle et spécifiez le chemin d’accès local avec le `--template-file` paramètre.

[!code-json[cosmosdb-sql-container-ru-update](~/quickstart-templates/101-cosmosdb-sql-container-ru-update/azuredeploy.json)]

### <a name="deploy-container-template-via-powershell"></a>Déployer un modèle de conteneur via PowerShell

Pour déployer le modèle Resource Manager à l’aide de PowerShell, **copie** le script et sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter new throughput for container"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -containerName $containerName `
    -throughput $throughput
```

### <a name="deploy-container-template-via-azure-cli"></a>Déployer un modèle de conteneur via l’interface CLI Azure

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName containerName=$containerName throughput=$throughput
```

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

- [Documentation Azure Resource Manager](/azure/azure-resource-manager/)
- [Schéma de fournisseur de ressources Cosmos DB Azure](/azure/templates/microsoft.documentdb/allversions)
- [Modèles de démarrage rapide de Cosmos DB Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Résolution des erreurs courantes de déploiement Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)