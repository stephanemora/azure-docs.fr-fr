---
title: Modèles Azure Resource Manager pour l’API Azure Cosmos DB Gremlin
description: Utiliser des modèles Azure Resource Manager pour créer et configurer l’API Azure Cosmos DB Gremlin.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: d1928606a22eba180ebd3f1e979362e75edaf2d7
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077784"
---
# <a name="create-azure-cosmos-db-gremlin-api-resources-from-a-resource-manager-template"></a>Créer des ressources de l’API Azure Cosmos DB Gremlin à partir d’un modèle Resource Manager

Découvrez comment créer des ressources d’API d’Azure Cosmos DB Gremlin à l’aide d’un modèle Azure Resource Manager. L’exemple suivant crée une API Azure Cosmos DB Gremlin à partir d’un [de modèle Azure Quickstart](https://aka.ms/gremlin-arm-qs). Ce modèle crée un compte Azure Cosmos pour l’API Gremlin avec deux graphes qui partagent un débit de 400 RU/s au niveau de la base de données.

Voici une copie du modèle :

[!code-json[create-cosmos-gremlin](~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

Pour déployer le modèle Resource Manager à l’aide d’Azure CLI, **copie** le script et sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first graph name: ' graph1Name
read -p 'Enter the second graph name: ' graph2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graph1Name=$graph1Name graph2Name=$graph2Name

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