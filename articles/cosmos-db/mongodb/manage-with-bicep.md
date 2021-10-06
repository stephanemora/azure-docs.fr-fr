---
title: Créer et gérer une API MongoDB pour Azure Cosmos DB avec Bicep
description: Utilisez Bicep pour créer et configurer une API MongoDB pour Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: e6ded17c15b9a126a996f4e6f368a5ab5c735ed9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700698"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-bicep"></a>Gérer les ressources d’une API MongoDB pour Azure Cosmos DB en utilisant Bicep

[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Dans cet article, vous découvrez comment utiliser Bicep pour déployer et gérer vos comptes Azure Cosmos DB pour une API, des bases de données et des collections MongoDB.

Cet article montre des exemples Bicep pour des comptes d’API Gremlin. Vous pouvez également trouver des exemples Bicep pour les API [SQL](../sql/manage-with-bicep.md), [Cassandra](../cassandra/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md) et [Table](../table/manage-with-bicep.md).

> [!IMPORTANT]
>
> * Les noms de compte sont limités à 44 caractères, tout en minuscules.
> * Pour changer les valeurs de débit, redéployez le modèle avec les valeurs de RU/s mises à jour.
> * Quand vous ajoutez ou supprimez des emplacements dans un compte Azure Cosmos, vous ne pouvez pas simultanément modifier d’autres propriétés. Ces opérations doivent être effectuées séparément.

Pour créer une des ressources Azure Cosmos DB ci-dessous, copiez l’exemple suivant dans un nouveau fichier Bicep. Si vous le souhaitez, vous pouvez créer un fichier de paramètres à utiliser lors du déploiement de plusieurs instances de la même ressource avec des valeurs et des noms différents. Il existe de nombreuses façons de déployer des modèles Azure Resource Manager, notamment [Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) et [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="mongodb-api-with-autoscale-provisioned-throughput"></a>API MongoDB avec débit provisionné avec mise à l’échelle automatique

Ce modèle va créer un compte Azure Cosmos pour l’API MongoDB (3.2, 3.6 ou 4.0) avec deux collections qui partagent un débit provisionné avec mise à l’échelle automatique au niveau de la base de données.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="mongodb-api-with-standard-provisioned-throughput"></a>API MongoDB avec débit provisionné standard

Crée un compte Azure Cosmos pour l’API MongoDB (3.2, 3.6 ou 4.0) avec deux collections qui partagent un débit standard (manuel) de 400 RU/s au niveau de la base de données.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb/main.bicep":::

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

* [Documentation Bicep](../../azure-resource-manager/bicep/index.yml)
* [Installer les outils Bicep](../../azure-resource-manager/bicep/install.md)
* Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
  * Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md)
  * Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-capacity-planner.md)
