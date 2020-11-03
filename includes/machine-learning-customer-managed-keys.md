---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 1fde2947719079e411bc233bcce426feec8fa996
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631039"
---
> [!IMPORTANT]
> L’instance Cosmos DB est créée dans un groupe de ressources managées par Microsoft dans __votre abonnement__ , avec toutes les ressources nécessaires. Cela signifie que vous êtes facturé pour cette instance Cosmos DB. Le groupe de ressources managée est nommé au format `<AML Workspace Resource Group Name><GUID>`. Si votre espace de travail Azure Machine Learning utilise un point de terminaison privé, un réseau virtuel est également créé pour l’instance Cosmos DB. Ce réseau virtuel est utilisé pour sécuriser la communication entre Cosmos DB et Azure Machine Learning.
> 
> * __Ne supprimez pas le groupe de ressources__ qui contient cette instance Cosmos DB, ni les ressources créées automatiquement dans ce groupe. Si vous avez besoin de supprimer le groupe de ressources, l’instance de Cosmos DB, etc., vous devez supprimer l’espace de travail Azure Machine Learning qui l’utilise. Le groupe de ressources, l’instance de Cosmos DB et d’autres ressources créées automatiquement sont supprimés lors de la suppression de l’espace de travail associé.
> * La valeur [__Unités de requête__](../articles/cosmos-db/request-units.md) par défaut pour ce compte Cosmos DB est définie sur __8000__. __La modification de cette valeur n’est pas prise en charge.__
> * Vous __ne pouvez pas fournir votre propre réseau virtuel pour une utilisation avec l’instance Cosmos DB__ créée. Vous __ne pouvez pas non plus modifier le réseau virtuel__. Par exemple, vous ne pouvez pas modifier la plage d’adresses IP qu’elle utilise.