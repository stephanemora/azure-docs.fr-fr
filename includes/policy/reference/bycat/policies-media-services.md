---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7116a9b4f585dcfcaa62c447be57376ac0f5c62b
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110074211"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les tâches Azure Media Services avec des entrées HTTPS doivent limiter les URI d’entrée aux modèles d’URI autorisés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9914afe-31cd-4b8a-92fa-c887f847d477) |Restreignez les entrées HTTPS utilisées par les tâches Media Services aux points de terminaison connus. Les entrées des points de terminaison HTTPS peuvent être entièrement désactivées en définissant une liste vide de modèles d’entrée de tâche autorisés. Quand les entrées de tâche spécifient un « baseUri », les modèles sont comparés à cette valeur. Quand « baseUri » n’est pas défini, le modèle est mis en correspondance avec la propriété « files ». |Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/Jobs_RestrictHttpInputs.json) |
