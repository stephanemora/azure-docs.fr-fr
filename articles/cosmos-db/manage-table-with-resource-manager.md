---
title: Modèles Azure Resource Manager pour l’API Table Azure Cosmos DB
description: Utilisez des modèles Azure Resource Manager pour créer et configurer l’API Table Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 4dd636be60233beafca8e8680551bd7c711a4ccc
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814874"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gérer les ressources de l’API Table Azure Cosmos DB à l’aide de modèles Azure Resource Manager

## Créer un compte et une table Azure Cosmos <a id="create-resource"></a>

Créez des ressources Azure Cosmos DB à l’aide d’un modèle Azure Resource Manager. Ce modèle crée un compte Azure Cosmos pour l’API Table avec une table à un débit de 400 RU/s. Copiez le modèle et déployez-le comme indiqué ci-dessous ou consultez la [galerie Démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) et procédez au déploiement à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un modèle et spécifier le chemin d’accès local avec le paramètre `--template-file`.

> [!NOTE]
> Les noms de comptes doivent être en minuscules et inférieurs à 31 caractères.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Déployer avec PowerShell

Pour déployer le modèle Resource Manager à l’aide de PowerShell, **copiez** le script et sélectionnez **Essayer** pour ouvrir Azure Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller** :

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Si vous choisissez d’utiliser une version locale de PowerShell et non Azure Cloud Shell, vous devez [installer](/powershell/azure/install-az-ps) le module Azure PowerShell. Exécutez `Get-Module -ListAvailable Az` pour trouver la version.

### <a name="deploy-via-azure-cli"></a>Déployer avec Azure CLI

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, **copiez** le script et sélectionnez **Essayer** pour ouvrir Azure Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller** :

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

La commande `az cosmosdb show` montre le compte Azure Cosmos nouvellement créé, une fois que ce dernier a été configuré. Si vous choisissez d’utiliser une version d’Azure CLI installée localement au lieu d’utiliser Cloud Shell, consultez l’article [Interface de ligne de commande Azure (CLI)](/cli/azure/).

## Mettre à jour le débit (RU/s) sur une table <a id="table-ru-update"></a>

Le modèle suivant met à jour le débit d’une table. Copiez le modèle et déployez-le comme indiqué ci-dessous ou consultez la [galerie Démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) et procédez au déploiement à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un modèle et spécifier le chemin d’accès local avec le paramètre `--template-file`.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>Déployer un débit de table avec PowerShell

Pour déployer le modèle Resource Manager à l’aide de PowerShell, **copiez** le script et sélectionnez **Essayer** pour ouvrir Azure Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller** :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter new throughput for table"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -tableName $tableName `
    -throughput $throughput
```

### <a name="deploy-table-template-via-azure-cli"></a>Déployer un modèle de table avec Azure CLI

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, sélectionnez **Essayer** afin d’ouvrir Azure Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller** :

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

- [Documentation Azure Resource Manager](/azure/azure-resource-manager/)
- [Schéma de fournisseurs de ressources Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modèles Démarrage rapide Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)