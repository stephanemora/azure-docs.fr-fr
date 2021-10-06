---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 09/14/2021
ms.author: larryfr
ms.openlocfilehash: 757179959035c6bfce77b1feaaf5bfeff5aab001
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621588"
---
> [!IMPORTANT]
> Cette instance de Cosmos DB est créée dans un groupe de ressources managées par Microsoft dans __votre abonnement__. Les services suivants sont également créés dans ce groupe de ressources et sont utilisés par la configuration de clé gérée par le client :
> * Compte Stockage Azure
> * Recherche Azure
>
> Étant donné que ces services sont créés dans votre abonnement Azure, cela signifie que vous êtes facturé pour ces instances de service. Si votre abonnement ne dispose pas d’un quota suffisant pour le service Azure Cosmos DB, une défaillance se produit. Pour plus d’informations sur les quotas, consultez les [quotas du service Azure Cosmos DB](/azure/cosmos-db/concepts-limits).
>
> Le groupe de ressources managée est nommé au format `<AML Workspace Resource Group Name><GUID>`. Si votre espace de travail Azure Machine Learning utilise un point de terminaison privé, un réseau virtuel est également créé dans ce groupe de ressources. Ce réseau virtuel est utilisé pour sécuriser la communication entre les services de ce groupe de ressources et votre espace de travail Azure Machine Learning.
> 
> * __Ne supprimez pas le groupe de ressources__ qui contient cette instance Cosmos DB, ni les ressources créées automatiquement dans ce groupe. Si vous avez besoin de supprimer le groupe de ressources, l’instance de Cosmos DB, etc., vous devez supprimer l’espace de travail Azure Machine Learning qui l’utilise. Le groupe de ressources, l’instance de Cosmos DB et d’autres ressources créées automatiquement sont supprimés lors de la suppression de l’espace de travail associé.
> * Les [__Unités de requête__](../articles/cosmos-db/request-units.md) utilisées par ce compte Cosmos DB se mettent automatiquement à l’échelle selon les besoins. Le nombre __minimum__ de RU est __1200__. Le nombre __maximum__ de RU est __12000__.
> * Vous __ne pouvez pas fournir votre propre réseau virtuel pour une utilisation avec l’instance Cosmos DB__ créée. Vous __ne pouvez pas non plus modifier le réseau virtuel__. Par exemple, vous ne pouvez pas modifier la plage d’adresses IP qu’elle utilise.
> 
> Pour estimer les coûts supplémentaires de l’instance Azure Cosmos DB, utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).