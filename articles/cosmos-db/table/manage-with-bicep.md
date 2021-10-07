---
title: Créer et gérer l’API Table Azure Cosmos DB avec Bicep
description: Utilisez Bicep pour créer et configurer l’API Table Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: fe911a945de6b7b15c49641b7aff3850c3759da2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602487"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-bicep"></a>Gérer les ressources de l’API Table Azure Cosmos DB à l’aide de Bicep

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Dans cet article, vous allez apprendre à utiliser Bicep pour faciliter le déploiement et la gestion de vos tables et de vos comptes de l’API Table Azure Cosmos DB.

Cet article contient des exemples pour les comptes de l’API Table uniquement. Vous pouvez également trouver des exemples Bicep pour les articles [Cassandra](../cassandra/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md) et [SQL](../sql/manage-with-bicep.md).

> [!IMPORTANT]
>
> * Les noms de compte sont limités à 44 caractères, tout en minuscules.
> * Pour changer les valeurs de débit, redéployez le modèle avec les valeurs de RU/s mises à jour.
> * Quand vous ajoutez ou supprimez des emplacements dans un compte Azure Cosmos, vous ne pouvez pas simultanément modifier d’autres propriétés. Ces opérations doivent être effectuées séparément.

Pour créer l’une des ressources Azure Cosmos DB ci-dessous, copiez l’exemple suivant dans un nouveau fichier Bicep. Vous pouvez éventuellement créer un fichier de paramètres à utiliser lors du déploiement de plusieurs instances de la même ressource avec des valeurs et noms différents. Il existe de nombreuses façons de déployer des modèles Azure Resource Manager, notamment : [Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) et [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

> [!TIP]
> Pour activer le débit partagé lorsque vous utilisez l’API Table, activez le débit au niveau d’un compte dans le Portail Azure. Le débit partagé au niveau du compte ne peut pas être défini à l’aide de Bicep.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Compte Azure Cosmos pour Table avec débit approvisionné en mode de mise à l’échelle automatique

Créez un compte Azure Cosmos pour l’API Table avec une table avec débit approvisionné en mode de mise à l’échelle automatique.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Compte Azure Cosmos pour Table avec débit approvisionné en mode standard

Créez un compte Azure Cosmos pour l’API Table avec une table avec débit approvisionné en mode standard.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table/main.bicep":::

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

* [Documentation Bicep](../../azure-resource-manager/bicep/index.yml)
* [Installer les outils Bicep](../../azure-resource-manager/bicep/install.md)
