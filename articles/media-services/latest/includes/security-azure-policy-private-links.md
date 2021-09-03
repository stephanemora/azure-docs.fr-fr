---
author: johndeu
ms.service: media-services
ms.topic: include
ms.date: 08/17/2021
ms.author: johndeu
ms.openlocfilehash: d6641a3ca8181f7b3c8538179623549cc92a098a
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122535196"
---
## <a name="azure-policy-definitions-for-private-links"></a>Définitions de Azure Policy pour les liens privés

Azure Media Services prend en charge plusieurs définitions [Azure Policy](../../../governance/policy/overview.md) basées sur des cas d'utilisation intégrés. 

Les définitions de stratégie suivantes peuvent être utilisées avec des liens privés :

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les comptes Azure Media Services doivent utiliser une API qui prend en charge Private Link](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa77d8bb4-8d22-4bc1-a884-f582a705b480) |Les comptes Media Services doivent être créés avec une API qui prend en charge la liaison privée. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_RequirePrivateLinkSupport_Audit.json) |
|[Azure Media Services doit utiliser une liaison privée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a591bf5-918e-4a5f-8dad-841863140d61) |Azure Private Link vous permet de connecter vos réseaux virtuels aux services Azure sans adresse IP publique au niveau de la source ou de la destination. La plateforme Private Link gère la connectivité entre le consommateur et les services sur le réseau principal Azure. En mappant des points de terminaison privés à Media Services, vous pouvez réduire les risques de fuite de données. En savoir plus sur les liaisons privées : [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md). |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLink_AuditIfNotExists.json) |
|[Configurer Azure Media Services pour utiliser des zones DNS privées](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4a7f6c1-585e-4177-ad5b-c2c93f4bb991) |Utilisez des zones DNS privées pour remplacer la résolution DNS pour un point de terminaison privé. Une zone DNS privée est liée à votre réseau virtuel à résoudre en compte Media Services. Pour en savoir plus : [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md). |DeployIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLinkDns_DeployIfNotExists.json) |
|[Configurer Azure Media Services avec des points de terminaison privés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5632066-946d-4766-9544-cd79bcc1286e) |Les points de terminaison privés vous permettent de connecter vos réseaux virtuels aux services Azure sans adresse IP publique au niveau de la source ou de la destination. En mappant des points de terminaison privés à Media Services, vous pouvez réduire les risques de fuite de données. En savoir plus sur les liaisons privées : [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md). |DeployIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLink_DeployIfNotExists.json) |

Reportez-vous à [Azure Policy for Media Services](../security-azure-policy.md) pour obtenir une liste complète de toutes les définitions de stratégies intégrées et de cas d'utilisation.

Pour plus d’informations sur le déploiement et l’utilisation des définitions de Azure Policy, consultez l’article [« qu’est-ce que Azure Policy ? »](../../../governance/policy/overview.md)