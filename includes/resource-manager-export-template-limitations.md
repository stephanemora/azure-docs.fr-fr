---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/31/2021
ms.author: tomfitz
ms.openlocfilehash: 76c1ed0a2a1b1a2cca77988d218e2460f99003e6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436207"
---
## <a name="limitations"></a>Limites

Lors de l’exportation à partir d’un groupe de ressources ou d’une ressource, le modèle exporté est généré à partir des [schémas publiés](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) pour chaque type de ressource. Parfois, le schéma ne dispose pas de la dernière version d’un type de ressource. Vérifiez votre modèle exporté pour vous assurer qu’il comprend les propriétés dont vous avez besoin. Si nécessaire, modifiez le modèle exporté pour utiliser la version d’API dont vous avez besoin.

La fonctionnalité d’exportation de modèle ne prend pas en charge l’exportation des ressources Azure Data Factory. Pour en savoir plus sur la façon dont vous pouvez exporter des ressources Data Factory, consultez [Copier ou cloner une fabrique de données dans Azure Data Factory](../articles/data-factory/copy-clone-data-factory.md).

Pour exporter des ressources créées par le biais du modèle de déploiement classique, vous devez [les migrer vers le modèle de déploiement de Resource Manager](../articles/virtual-machines/migration-classic-resource-manager-overview.md).

Si vous recevez un avertissement lors de l’exportation d’un modèle qui indique qu’un type de ressource n’a pas été exporté, vous pouvez toujours découvrir les propriétés pour cette ressource. Pour connaître les propriétés des ressources, consultez les [informations de référence sur les modèles](/azure/templates). Vous pouvez également consulter l’[API REST Azure](/rest/api/azure/) pour le type de ressource.

Le groupe de ressources pour lequel vous créez le modèle exporté est limité à 200 ressources. Si vous tentez d’exporter un groupe de ressources contenant plus de 200 ressources, le message d’erreur `Export template is not supported for resource groups more than 200 resources` s’affiche.
