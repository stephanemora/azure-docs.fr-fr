---
title: Modèles Resource Manager pour Azure Cosmos DB API pour MongoDB
description: Utilisez des modèles Azure Resource Manager pour créer et configurer l’API Azure Cosmos DB pour MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: mjbrown
ms.openlocfilehash: 67e07ada1b4782d2dc1a13c2bc7bb6c22371c52a
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035657"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gérer les ressources de l’API MongoDB Azure Cosmos DB à l’aide de modèles Azure Resource Manager
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Dans cet article, vous apprenez à utiliser des modèles Azure Resource Manager pour faciliter le déploiement et la gestion de vos bases de données, collections et comptes Azure Cosmos DB pour l’API MongoDB.

Les exemples qu’il contient ne portent que sur l’API d’Azure Cosmos DB pour MongoDB. Pour obtenir des exemples liés à d’autres comptes de type d’API, consultez les articles Utiliser des modèles Azure Resource Manager avec l’API d’Azure Cosmos DB pour [Cassandra](../cassandra/templates-samples.md), [Gremlin](../templates-samples-gremlin.md), [SQL](../templates-samples-sql.md) et [Table](../table/resource-manager-templates.md).

> [!IMPORTANT]
>
> * Les noms de compte sont limités à 44 caractères, tout en minuscules.
> * Pour changer les valeurs de débit, redéployez le modèle avec les valeurs de RU/s mises à jour.
> * Quand vous ajoutez ou supprimez des emplacements dans un compte Azure Cosmos, vous ne pouvez pas simultanément modifier d’autres propriétés. Ces opérations doivent être effectuées séparément.

Pour créer l’une des ressources Azure Cosmos DB ci-dessous, copiez l’exemple de modèle suivant dans un nouveau fichier json. Vous pouvez éventuellement créer un fichier json de paramètres à utiliser lors du déploiement de plusieurs instances de la même ressource avec des valeurs et noms différents. Il existe de nombreuses façons de déployer des modèles Azure Resource Manager, notamment : le [Portail Azure](../../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) et [GitHub](../../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Compte Azure Cosmos pour MongoDB avec débit approvisionné en mode de mise à l’échelle automatique

Ce modèle crée un compte Azure Cosmos pour l’API MongoDB (3.2 ou 3.6) avec deux collections qui partagent un débit approvisionné en mode de mise à l’échelle automatique au niveau de la base de données. Ce modèle est également disponible pour un déploiement en un clic à partir de la galerie de modèles de démarrage rapide Azure.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Déployer sur Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Compte Azure Cosmos pour MongoDB avec débit approvisionné en mode standard

Ce modèle crée un compte Azure Cosmos pour l’API MongoDB (3.2 ou 3.6) avec deux collections qui partagent un débit standard (manuel) de 400 RU/s au niveau de la base de données. Ce modèle est également disponible pour un déploiement en un clic à partir de la galerie de modèles de démarrage rapide Azure.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Déployer sur Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

* [Documentation Azure Resource Manager](../../azure-resource-manager/index.yml)
* [Schéma de fournisseurs de ressources Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modèles Démarrage rapide Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md)
* Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existantes pour la planification de la capacité.
    * Si vous ne connaissez que le nombre de vCore et de serveurs présents dans votre cluster de bases de données existantes, lisez l’article sur l’[estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
    * Si vous connaissez les taux de requêtes types de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-capacity-planner.md)