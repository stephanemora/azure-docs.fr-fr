---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 85ffe8c4477a2226f570323f6f7beefa5f513a4a
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84712006"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Auditez les registres de conteneurs pour lesquels le chiffrement n’est pas activé avec des clés gérées par le client (CMK). Pour plus d’informations sur le chiffrement CMK, consultez : [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Les registres de conteneurs ne doivent pas autoriser l’accès réseau non restreint](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Auditez les registres de conteneurs qui n’ont pas de règles réseau (IP ou réseau virtuel) configurées et autorisent tous l’accès réseau par défaut. Les registres de conteneurs avec au moins une règle IP/pare-feu ou un réseau virtuel configuré sont considérés comme conformes. Pour plus d’informations sur les règles réseau de Container Registry, consultez : [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Les registres de conteneurs doivent utiliser des liaisons privées](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Auditez les registres de conteneurs qui n’ont pas au moins une connexion de point de terminaison privé approuvée. Les clients d’un réseau virtuel peuvent accéder de façon sécurisée aux ressources qui ont des connexions de point de terminaison privés via des liens privés. Pour plus d’informations, consultez [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[Container Registry doit utiliser un point de terminaison de service de réseau virtuel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Cette stratégie audite Container Registry s’il n’est pas configuré pour utiliser un point de terminaison de service de réseau virtuel. |Audit, Désactivé |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
