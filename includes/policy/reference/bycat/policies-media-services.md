---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 887d2784d4949a70763e6c08ce3ec72aaddd0bcd
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112019474"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les comptes Azure Media Services doivent utiliser une API qui prend en charge Private Link](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa77d8bb4-8d22-4bc1-a884-f582a705b480) |Les comptes Media Services doivent être créés avec une API qui prend en charge la liaison privée. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_RequirePrivateLinkSupport_Audit.json) |
|[Les comptes Azure Media Services qui autorisent l’accès à l’API v2 héritée doivent être bloqués](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fccf93279-9c91-4143-a841-8d1f21505455) |L’API héritée Media Services v2 autorise les requêtes qui ne peuvent pas être managées à l’aide d’Azure Policy. Les ressources Media Services créées à l’aide de l’API 2020-05-01 ou version ultérieure bloquent l’accès à l’API v2 héritée. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_BlockRestV2_Audit.json) |
|[Les stratégies de clé de contenu Azure Media Services doivent utiliser l’authentification par jeton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdaccf7e4-9808-470c-a848-1c5b582a1afb) |Les stratégies de clés de contenu définissent les conditions requises pour accéder aux clés de contenu. Une restriction par jeton garantit que les clés de contenu sont accessibles uniquement aux utilisateurs qui ont des jetons valides à partir d’un service d’authentification, par exemple Azure Active Directory. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/ContentKeyPolicies_RequireTokenAuth_Audit.json) |
|[Les tâches Azure Media Services avec des entrées HTTPS doivent limiter les URI d’entrée aux modèles d’URI autorisés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9914afe-31cd-4b8a-92fa-c887f847d477) |Restreignez les entrées HTTPS utilisées par les tâches Media Services aux points de terminaison connus. Les entrées des points de terminaison HTTPS peuvent être entièrement désactivées en définissant une liste vide de modèles d’entrée de tâche autorisés. Quand les entrées de tâche spécifient un « baseUri », les modèles sont comparés à cette valeur. Quand « baseUri » n’est pas défini, le modèle est mis en correspondance avec la propriété « files ». |Deny, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/Jobs_RestrictHttpInputs.json) |
