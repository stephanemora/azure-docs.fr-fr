---
title: Modèles Azure Resource Manager pour l’API Azure Cosmos DB pour MongoDB
description: Utiliser des modèles Azure Resource Manager pour créer et configurer des API Azure Cosmos DB pour MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: bcf9e0492e58de79efbb16f9ee13adbd0f27b572
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077769"
---
# <a name="create-azure-cosmos-db-api-for-mongodb-resources-from-a-resource-manager-template"></a>Créer des API Azure Cosmos DB pour MongoDB ressources à partir d’un modèle Resource Manager

Découvrez comment créer une API Azure Cosmos DB pour les ressources de MongoDB à l’aide d’un modèle Azure Resource Manager. L’exemple suivant crée un compte Azure Cosmos DB pour l’API MongoDB à partir d’un [de modèle Azure Quickstart](https://aka.ms/mongodb-arm-qs). Ce modèle crée un compte Azure Cosmos pour l’API MongoDB avec deux collections qui partagent un débit de 400 RU/s au niveau de la base de données.

Voici une copie du modèle :

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

## <a name="deploy-via-azure-cli"></a>Déployer via l’interface CLI Azure

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

Dans l’exemple précédent, vous avez référencé un modèle qui est stocké dans GitHub. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un nouveau modèle et spécifiez le chemin d’accès local avec le `--template-file` paramètre.

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

- [Documentation Azure Resource Manager](/azure/azure-resource-manager/)
- [Schéma de fournisseur de ressources Cosmos DB Azure](/azure/templates/microsoft.documentdb/allversions)
- [Modèles de démarrage rapide de Cosmos DB Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Résolution des erreurs courantes de déploiement Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)