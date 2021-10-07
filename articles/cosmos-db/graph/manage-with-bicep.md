---
title: Créer et gérer l’API Gremlin pour Azure Cosmos DB avec Bicep
description: Utilisez Bicep pour créer et configurer l’API Gremlin pour Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 9/13/2021
ms.author: mjbrown
ms.openlocfilehash: a5ac7ad31cb09ab57948f9a7b1eb86e07ec608a5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700544"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-bicep"></a>Gérer les ressources de l’API Gremlin pour Azure Cosmos DB en utilisant Bicep

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Dans cet article, vous découvrez comment utiliser Bicep pour déployer et gérer vos comptes, bases de données et graphes de l’API Gremlin pour Azure Cosmos DB.

Cet article montre des exemples Bicep pour des comptes d’API Gremlin. Vous pouvez également trouver des exemples Bicep pour les API [SQL](../sql/manage-with-bicep.md), [Cassandra](../cassandra/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md) et [Table](../table/manage-with-bicep.md).

> [!IMPORTANT]
>
> * Les noms de compte sont limités à 44 caractères, tout en minuscules.
> * Pour changer les valeurs de débit, redéployez le modèle avec les valeurs de RU/s mises à jour.
> * Quand vous ajoutez ou supprimez des emplacements dans un compte Azure Cosmos, vous ne pouvez pas simultanément modifier d’autres propriétés. Ces opérations doivent être effectuées séparément.

Pour créer une des ressources Azure Cosmos DB ci-dessous, copiez l’exemple suivant dans un nouveau fichier Bicep. Si vous le souhaitez, vous pouvez créer un fichier de paramètres à utiliser lors du déploiement de plusieurs instances de la même ressource avec des valeurs et des noms différents. Il existe de nombreuses façons de déployer des modèles Azure Resource Manager, notamment [Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) et [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="gremlin-api-with-autoscale-provisioned-throughput"></a>API Gremlin avec débit provisionné avec mise à l’échelle automatique

Crée un compte Azure Cosmos pour l’API Gremlin avec une base de données et un graphe configurés pour le débit provisionné avec mise à l’échelle automatique.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="gremlin-api-with-standard-provisioned-throughput"></a>API Gremlin avec débit provisionné standard

Crée un compte Azure Cosmos pour l’API Gremlin avec une base de données et un graphe configurés avec un débit provisionné standard.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin/main.bicep":::

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

* [Documentation Bicep](../../azure-resource-manager/bicep/index.yml)
* [Installer les outils Bicep](../../azure-resource-manager/bicep/install.md)
