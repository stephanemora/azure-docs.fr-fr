---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 51d6a18a39c6c82c5c923d8da96bfab155022cce
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659655"
---
|Nom |Description |Effet(s) |Version |GitHub |
|---|---|---|---|---|
|[Auditer l’utilisation de règles personnalisées RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Auditer des rôles intégrés tels que « Propriétaire, contributeur, lecteur » au lieu des rôles RBAC personnalisés, qui sont susceptibles d’engendrer des erreurs. L’utilisation de rôles personnalisés est traitée comme une exception et nécessite un contrôle rigoureux et la modélisation des menaces |Audit, Désactivé |1.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Les rôles de propriétaire d’abonnement personnalisé ne doivent pas exister](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Cette stratégie garantit qu’il n’existe aucun rôle de propriétaire d’abonnement personnalisé. |Audit, Désactivé |1.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
