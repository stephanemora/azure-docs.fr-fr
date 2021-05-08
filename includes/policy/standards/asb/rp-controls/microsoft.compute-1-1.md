---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/28/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: db8ad25bc9df99ef721d2b8db64575b9e34016f6
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108211469"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les recommandations de renforcement de réseau adaptatif doivent être appliquées sur les machines virtuelles accessibles à partir d’Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyse les tendances du trafic des machines virtuelles accessibles sur Internet et fournit des recommandations sur les règles de groupe de sécurité réseau à utiliser pour réduire la surface d’attaque potentielle |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Les machines virtuelles accessibles à partir d’Internet doivent être protégées avec des groupes de sécurité réseau](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Protégez vos machines virtuelles contre les menaces potentielles en limitant leur accès avec des groupes de sécurité réseau (NSG). Pour en savoir plus sur le contrôle du trafic avec des groupes de sécurité réseau, consultez [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc). |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Le transfert IP doit être désactivé sur votre machine virtuelle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |L’activation du transfert IP sur la carte réseau d’une machine virtuelle permet à cette dernière de recevoir du trafic adressé à d’autres destinations. Le transfert IP n'est que rarement nécessaire (par exemple, lors de l'utilisation de la machine virtuelle en tant qu'appliance virtuelle de réseau). Par conséquent, un examen par l'équipe de sécurité réseau est requis. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Les ports de gestion des machines virtuelles doivent être protégés par un contrôle d’accès réseau juste-à-temps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |L’éventuel accès juste-à-temps (JIT) au réseau sera supervisé par Azure Security Center en tant que recommandation |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Les ports de gestion doivent être fermés sur vos machines virtuelles](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Les ports de gestion à distance ouverts exposent votre machine virtuelle à un niveau de risque élevé des attaques basées sur Internet. Ces attaques tentent d’attaquer par force brute les informations d’identification afin d’obtenir un accès administrateur à l’ordinateur. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
