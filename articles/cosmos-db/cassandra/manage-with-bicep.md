---
title: Créer et gérer l’API Cassandra Azure Cosmos DB avec Bicep
description: Utilisez Bicep pour créer et configurer l’API Cassandra Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 9/13/2021
ms.author: mjbrown
ms.openlocfilehash: 0433f6830100fc23420006ec2b980b7ccf7f9692
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602523"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-bicep"></a>Gérer les ressources de l’API Cassandra Azure Cosmos DB à l’aide de Bicep

[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Dans cet article, vous allez apprendre à utiliser Bicep pour faciliter le déploiement et la gestion de vos espaces de clés, tables et comptes d’API Cassandra Azure Cosmos DB.

Cet article présente des exemples Bicep pour des comptes d’API Cassandra. Vous trouverez également des exemples Bicep pour les API [SQL](../sql/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md) et [Table](../table/manage-with-bicep.md).

> [!IMPORTANT]
>
> * Les noms de compte sont limités à 44 caractères, tout en minuscules.
> * Pour changer les valeurs de débit, redéployez le modèle avec les valeurs de RU/s mises à jour.
> * Quand vous ajoutez ou supprimez des emplacements dans un compte Azure Cosmos, vous ne pouvez pas simultanément modifier d’autres propriétés. Ces opérations doivent être effectuées séparément.

Pour créer l’une des ressources Azure Cosmos DB ci-dessous, copiez l’exemple suivant dans un nouveau fichier bicep. Vous pouvez éventuellement créer un fichier de paramètres à utiliser lors du déploiement de plusieurs instances de la même ressource avec des valeurs et noms différents. Il existe de nombreuses façons de déployer des modèles Azure Resource Manager, notamment avec : [Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) et [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="cassandra-api-with-autoscale-provisioned-throughput"></a>API Cassandra avec débit approvisionné avec mise à l’échelle automatique

Créez un compte Azure Cosmos dans deux régions avec des options de cohérence et de basculement, ainsi qu’un espace de clés et une table configurés pour le débit approvisionné en mode de mise à l’échelle automatique.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-cassandra-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="cassandra-api-with-standard-provisioned-throughput"></a>API Cassandra avec débit approvisionné en mode standard

Créez un compte Azure Cosmos dans deux régions avec des options de cohérence et de basculement, ainsi qu’un espace de clés et une table configurés pour le débit approvisionné en mode standard.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-cassandra/main.bicep":::

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

* [Documentation Bicep](../../azure-resource-manager/bicep/index.yml)
* [Installer les outils Bicep](../../azure-resource-manager/bicep/install.md)
