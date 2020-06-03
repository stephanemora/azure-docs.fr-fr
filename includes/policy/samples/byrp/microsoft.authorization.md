---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7c377655e1ff08b2d90b18cdb9b99a61fce3a936
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235300"
---
|Nom |Description |Effet(s) |Version |GitHub |
|---|---|---|---|---|
|[Auditer l’utilisation de règles personnalisées RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Auditer des rôles intégrés tels que « Propriétaire, contributeur, lecteur » au lieu des rôles RBAC personnalisés, qui sont susceptibles d’engendrer des erreurs. L’utilisation de rôles personnalisés est traitée comme une exception et nécessite un contrôle rigoureux et la modélisation des menaces |Audit, Désactivé |1.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Les rôles de propriétaire d’abonnement personnalisé ne doivent pas exister](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Cette stratégie garantit qu’il n’existe aucun rôle de propriétaire d’abonnement personnalisé. |Audit, Désactivé |1.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
