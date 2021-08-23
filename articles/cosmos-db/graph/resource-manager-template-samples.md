---
title: Modèles Resource Manager pour l’API Gremlin Azure Cosmos DB
description: Utilisez des modèles Azure Resource Manager pour créer et configurer l’API Gremlin Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 46a247a55043776409e3895f9ec025c9e16c41b1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562223"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Gérer les ressources de l’API Gremlin Azure Cosmos DB à l’aide de modèles Azure Resource Manager
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Dans cet article, vous apprenez à utiliser des modèles Azure Resource Manager pour faciliter le déploiement et la gestion de vos bases de données, graphiques et comptes Azure Cosmos DB.

Les exemples qu’il contient ne portent que sur les comptes d’API Gremlin. Pour obtenir des exemples liés à d’autres comptes de type d’API, consultez les articles Utiliser des modèles Azure Resource Manager avec l’API d’Azure Cosmos DB pour [Cassandra](../cassandra/templates-samples.md), [SQL](../templates-samples-sql.md), [MongoDB](../mongodb/resource-manager-template-samples.md) et [Table](../table/resource-manager-templates.md).

> [!IMPORTANT]
>
> * Les noms de compte sont limités à 44 caractères, tout en minuscules.
> * Pour changer les valeurs de débit, redéployez le modèle avec les valeurs de RU/s mises à jour.
> * Quand vous ajoutez ou supprimez des emplacements dans un compte Azure Cosmos, vous ne pouvez pas simultanément modifier d’autres propriétés. Ces opérations doivent être effectuées séparément.

Pour créer l’une des ressources Azure Cosmos DB ci-dessous, copiez l’exemple de modèle suivant dans un nouveau fichier json. Vous pouvez éventuellement créer un fichier json de paramètres à utiliser lors du déploiement de plusieurs instances de la même ressource avec des valeurs et noms différents. Il existe de nombreuses façons de déployer des modèles Azure Resource Manager, notamment : le [Portail Azure](../../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) et [GitHub](../../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-autoscale-provisioned-throughput"></a>Compte Azure Cosmos DB pour Gremlin avec débit approvisionné en mode de mise à l’échelle automatique

Ce modèle crée un compte Azure Cosmos pour l’API Gremlin avec une base de données et un graphique configurés pour le débit approvisionné en mode de mise à l’échelle automatique. Ce modèle est également disponible pour un déploiement en un clic à partir de la galerie de modèles de démarrage rapide Azure.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Déployer sur Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-gremlin-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-standard-provisioned-throughput"></a>Compte Azure Cosmos DB pour Gremlin avec débit approvisionné en mode standard

Ce modèle crée un compte Azure Cosmos pour l’API Gremlin avec une base de données et un graphique configurés pour le débit standard (manuel). Ce modèle est également disponible pour un déploiement en un clic à partir de la galerie de modèles de démarrage rapide Azure.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Déployer sur Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-gremlin%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin/azuredeploy.json":::

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

* [Documentation Azure Resource Manager](../../azure-resource-manager/index.yml)
* [Schéma de fournisseurs de ressources Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modèles Démarrage rapide Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md)