---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 04/21/2021
ms.author: larryfr
ms.openlocfilehash: 6d9b6fe18d7eb4ce532c6c2960944420eee952f0
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107903958"
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
> * La valeur [__Unités de requête__](../articles/cosmos-db/request-units.md) par défaut pour ce compte Cosmos DB est définie sur __8000__.
> * Vous __ne pouvez pas fournir votre propre réseau virtuel pour une utilisation avec l’instance Cosmos DB__ créée. Vous __ne pouvez pas non plus modifier le réseau virtuel__. Par exemple, vous ne pouvez pas modifier la plage d’adresses IP qu’elle utilise.