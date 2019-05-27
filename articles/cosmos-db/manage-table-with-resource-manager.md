---
title: Modèles Azure Resource Manager pour l’API de Table Azure Cosmos DB
description: Utiliser des modèles Azure Resource Manager pour créer et configurer des API de Table Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 82e2a436bf6b25b6164d845d234896390a262292
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968828"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gérer les ressources de l’API de Table Azure Cosmos DB à l’aide de modèles Azure Resource Manager

## Créer une table et compte Azure Cosmos <a id="create-resource"></a>

Créer des ressources Azure Cosmos DB à l’aide d’un modèle Azure Resource Manager. Ce modèle crée un compte Azure Cosmos pour l’API de Table avec une table à un débit de 400 RU/s. Copiez le modèle et les déployer comme indiqué ci-dessous ou visitez [galerie de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) et déployer à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un nouveau modèle et spécifiez le chemin d’accès local avec le `--template-file` paramètre.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Déployer à l’aide de PowerShell

Pour déployer le modèle Resource Manager à l’aide de PowerShell, **copie** le script et sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

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

Si vous choisissez d’utiliser une version installée localement de PowerShell plutôt qu’à partir d’Azure Cloud shell, vous devez [installer](/powershell/azure/install-az-ps) le module Azure PowerShell. Exécutez `Get-Module -ListAvailable Az` pour trouver la version.

### <a name="deploy-via-azure-cli"></a>Déployer via l’interface CLI Azure

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, **copie** le script et sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

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

Le `az cosmosdb show` commande montre le compte Azure Cosmos nouvellement créé, une fois que ce dernier a été configuré. Si vous choisissez d’utiliser une version installée localement d’Azure CLI au lieu d’utiliser CloudShell, consultez [les Interface de ligne de commande (CLI) Azure](/cli/azure/) article.

## Mettre à jour de débit (RU/s) sur une table <a id="table-ru-update"></a>

Le modèle suivant met à jour le débit d’une table. Copiez le modèle et les déployer comme indiqué ci-dessous ou visitez [galerie de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) et déployer à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un nouveau modèle et spécifiez le chemin d’accès local avec le `--template-file` paramètre.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>Déployer le débit de table par le biais de PowerShell

Pour déployer le modèle Resource Manager à l’aide de PowerShell, **copie** le script et sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

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

### <a name="deploy-table-template-via-azure-cli"></a>Déployer un modèle de table via l’interface CLI Azure

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

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
- [Schéma de fournisseur de ressources Cosmos DB Azure](/azure/templates/microsoft.documentdb/allversions)
- [Modèles de démarrage rapide de Cosmos DB Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Résolution des erreurs courantes de déploiement Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)