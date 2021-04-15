---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4c54b7f2bcad81b8113e35b115dd66e395c333da
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284622"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les machines virtuelles accessibles à partir d’Internet doivent être protégées avec des groupes de sécurité réseau](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Protégez vos machines virtuelles contre les menaces potentielles en limitant leur accès avec des groupes de sécurité réseau (NSG). Pour en savoir plus sur le contrôle du trafic avec des groupes de sécurité réseau, consultez [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc). |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Le transfert IP doit être désactivé sur votre machine virtuelle](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |L’activation du transfert IP sur la carte réseau d’une machine virtuelle permet à cette dernière de recevoir du trafic adressé à d’autres destinations. Le transfert IP n'est que rarement nécessaire (par exemple, lors de l'utilisation de la machine virtuelle en tant qu'appliance virtuelle de réseau). Par conséquent, un examen par l'équipe de sécurité réseau est requis. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Les ports de gestion doivent être fermés sur vos machines virtuelles](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Les ports de gestion à distance ouverts exposent votre machine virtuelle à un niveau de risque élevé des attaques basées sur Internet. Ces attaques tentent d’attaquer par force brute les informations d’identification afin d’obtenir un accès administrateur à l’ordinateur. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
