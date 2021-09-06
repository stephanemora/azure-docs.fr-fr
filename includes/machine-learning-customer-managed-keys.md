---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 06/24/2021
ms.author: larryfr
ms.openlocfilehash: 99d885ab35eb04963d9d2b0c6c9c14dbd10f2e2c
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113559662"
---
> [!IMPORTANT]
> Cette instance de Cosmos DB est créée dans un groupe de ressources managées par Microsoft dans __votre abonnement__. Les services suivants sont également créés dans ce groupe de ressources et sont utilisés par la configuration de clé gérée par le client :
> * Compte Stockage Azure
> * Recherche Azure
>
> Étant donné que ces services sont créés dans votre abonnement Azure, cela signifie que vous êtes facturé pour ces instances de service. 
>
> Le groupe de ressources managée est nommé au format `<AML Workspace Resource Group Name><GUID>`. Si votre espace de travail Azure Machine Learning utilise un point de terminaison privé, un réseau virtuel est également créé dans ce groupe de ressources. Ce réseau virtuel est utilisé pour sécuriser la communication entre les services de ce groupe de ressources et votre espace de travail Azure Machine Learning.
> 
> * __Ne supprimez pas le groupe de ressources__ qui contient cette instance Cosmos DB, ni les ressources créées automatiquement dans ce groupe. Si vous avez besoin de supprimer le groupe de ressources, l’instance de Cosmos DB, etc., vous devez supprimer l’espace de travail Azure Machine Learning qui l’utilise. Le groupe de ressources, l’instance de Cosmos DB et d’autres ressources créées automatiquement sont supprimés lors de la suppression de l’espace de travail associé.
> * Les [__Unités de requête__](../articles/cosmos-db/request-units.md) utilisées par ce compte Cosmos DB se mettent automatiquement à l’échelle selon les besoins. Le nombre __minimum__ de RU est __1200__. Le nombre __maximum__ de RU est __12000__.
> * Vous __ne pouvez pas fournir votre propre réseau virtuel pour une utilisation avec l’instance Cosmos DB__ créée. Vous __ne pouvez pas non plus modifier le réseau virtuel__. Par exemple, vous ne pouvez pas modifier la plage d’adresses IP qu’elle utilise.
> 
> Pour estimer les coûts supplémentaires de l’instance Azure Cosmos DB, utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).