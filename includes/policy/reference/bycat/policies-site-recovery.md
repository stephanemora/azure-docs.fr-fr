---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: df4af57a55acb0e89bf6155de9c6a5aded9f9a46
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112019514"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[Préversion\] : Configurer des coffres Azure Recovery Services pour utiliser des zones DNS privées](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942bd215-1a66-44be-af65-6a1c0318dbe2) |Utilisez des zones DNS privées pour remplacer la résolution DNS pour un point de terminaison privé. Une zone DNS privée est liée à votre réseau virtuel afin de permettre des opérations de résolution pour les coffres Recovery Services Vaults. Pour en savoir plus : [https://aka.ms/privatednszone](https://aka.ms/privatednszone). |DeployIfNotExists, Désactivé |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/SiteRecovery_PrivateDnsZones_DeployIfNotExist.json) |
|[\[Préversion\] : Configurer des points de terminaison privés sur des coffres Azure Recovery Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe95a8a5c-0987-421f-84ab-df4d88ebf7d1) |Les points de terminaison privés vous permettent de connecter votre réseau virtuel aux services Azure sans IP publique au niveau de la source ou de la destination. En mappant les points de terminaison privés aux ressources de récupération de site des coffres Recovery Services, vous pouvez réduire les risques de fuite de données. Pour utiliser des liaisons privées, vous devez affecter l’identité MSI (Managed Service Identity) aux coffres Recovery Services. En savoir plus sur les liaisons privées : [https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints). |DeployIfNotExists, Désactivé |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/SiteRecovery_PrivateEndpoints_DeployIfNotExist.json) |
|[\[Préversion\] : Les coffres Recovery Services doivent utiliser une liaison privée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11e3da8c-1d68-4392-badd-0ff3c43ab5b0) |Azure Private Link vous permet de connecter votre réseau virtuel aux services Azure sans adresse IP publique au niveau de la source ou de la destination. La plateforme Private Link gère la connectivité entre le consommateur et les services sur le réseau principal Azure. En mappant des points de terminaison privés à des coffres Azure Recovery Services, les risques de fuite de données sont réduits. Découvrez plus en détail les liaisons privées pour Azure Site Recovery sur [https://aka.ms/HybridScenarios-PrivateLink](https://aka.ms/HybridScenarios-PrivateLink) et [https://aka.ms/AzureToAzure-PrivateLink](https://aka.ms/AzureToAzure-PrivateLink). |Audit, Désactivé |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/RecoveryServices_SiteRecovery_PrivateEndpoint_Audit.json) |
