---
title: Modèles Resource Manager pour l’API Cassandra Azure Cosmos DB
description: Utilisez des modèles Azure Resource Manager pour créer et configurer l’API Cassandra Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: e3dd086108ba8c518bf7caf027f149de2ab70e57
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980611"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gérer les ressources de l’API Cassandra Azure Cosmos DB à l’aide de modèles Azure Resource Manager

Cet article explique comment effectuer différentes opérations pour automatiser la gestion de bases de données, conteneurs et comptes Azure Cosmos DB avec des modèles Azure Resource Manager. Les exemples qu’il contient ne portent que sur les comptes d’API SQL. Pour obtenir des exemples liés à d’autres comptes de type d’API, voir les articles Utiliser des modèles Azure Resource Manager avec l’API d’Azure Cosmos DB pour [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md) et [Table](manage-table-with-resource-manager.md).

## Créer un compte, un espace de clés et une table Azure Cosmos <a id="create-resource"></a>

Créez des ressources Azure Cosmos DB à l’aide d’un modèle Azure Resource Manager. Ce modèle crée un compte Azure Cosmos pour l’API Cassandra avec deux tables qui partagent un débit de 400 RU/s au niveau de l’espace de clés. Copiez le modèle et déployez-le comme indiqué ci-dessous ou consultez la [galerie Démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) et procédez au déploiement à partir du portail Azure. Vous pouvez également télécharger le modèle sur votre ordinateur local ou créer un modèle et spécifier le chemin d’accès local avec le paramètre `--template-file`.

> [!NOTE]
> Les noms de compte doivent comporter au maximum 44 caractères en minuscules.
> Pour mettre à jour les RU/s, renvoyez le modèle avec des valeurs de propriété de débit mises à jour.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-the-azure-cli"></a>Déployer avec Azure CLI

Pour déployer le modèle Azure Resource Manager avec Azure CLI, **copiez** le script et sélectionnez **Essayer** pour ouvrir Azure Cloud Shell. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller** :

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

La commande `az cosmosdb show` montre le compte Azure Cosmos nouvellement créé, une fois que ce dernier a été configuré. Si vous choisissez d’utiliser une version de l’interface Azure CLI installée localement au lieu d’utiliser Cloud Shell, consultez l’article [Azure CLI](/cli/azure/).


## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

- [Documentation Azure Resource Manager](/azure/azure-resource-manager/)
- [Schéma de fournisseurs de ressources Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modèles Démarrage rapide Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
