---
title: Modèles Azure Resource Manager pour l’API Azure Cosmos DB pour MongoDB
description: Utiliser des modèles Azure Resource Manager pour créer et configurer des API Azure Cosmos DB pour MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 99f1e41107c277c8b3f1b21f81952d5d5cadaa29
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968874"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gérer les ressources de l’API MongoDB Azure Cosmos DB à l’aide de modèles Azure Resource Manager

## Créer des API Azure Cosmos DB pour compte MongoDB, base de données et une collection <a id="create-resource"></a>

Créer des ressources Azure Cosmos DB à l’aide d’un modèle Azure Resource Manager. Ce modèle crée un compte Azure Cosmos pour l’API MongoDB avec deux collections qui partagent un débit de 400 RU/s au niveau de la base de données. Copiez le modèle et les déployer comme indiqué ci-dessous ou visitez [galerie de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) et déployer à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un nouveau modèle et spécifiez le chemin d’accès local avec le `--template-file` paramètre.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>Déployer via l’interface CLI Azure

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, **copie** le script et sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Le `az cosmosdb show` commande montre le compte Azure Cosmos nouvellement créé, une fois que ce dernier a été configuré. Si vous choisissez d’utiliser une version installée localement d’Azure CLI au lieu d’utiliser CloudShell, consultez [les Interface de ligne de commande (CLI) Azure](/cli/azure/) article.

## Mettre à jour de débit (RU/s) sur une base de données <a id="database-ru-update"></a>

Le modèle suivant met à jour le débit d’une base de données. Copiez le modèle et les déployer comme indiqué ci-dessous ou visitez [galerie de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/) et déployer à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un nouveau modèle et spécifiez le chemin d’accès local avec le `--template-file` paramètre.

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Déployer un modèle de base de données via l’interface CLI Azure

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Mettre à jour de débit (RU/s) sur une collection <a id="collection-ru-update"></a>

Le modèle suivant met à jour le débit d’une collection. Copiez le modèle et les déployer comme indiqué ci-dessous ou visitez [galerie de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/) et déployer à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un nouveau modèle et spécifiez le chemin d’accès local avec le `--template-file` paramètre.

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>Déployer le modèle de collection via l’interface CLI Azure

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

- [Documentation Azure Resource Manager](/azure/azure-resource-manager/)
- [Schéma de fournisseur de ressources Cosmos DB Azure](/azure/templates/microsoft.documentdb/allversions)
- [Modèles de démarrage rapide de Cosmos DB Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Résolution des erreurs courantes de déploiement Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)