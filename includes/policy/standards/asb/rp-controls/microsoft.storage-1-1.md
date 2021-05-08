---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/28/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a542dcd11107aa14fc20a2e1d41195a5eed0af6
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108212643"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les comptes de stockage doivent limiter l’accès réseau](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |L’accès réseau aux comptes de stockage doit être limité. Configurez les règles du réseau de telle manière que seules les applications des réseaux autorisés puissent accéder au compte de stockage. Pour autoriser les connexions de clients Internet ou locaux spécifiques, l’accès au trafic peut être autorisé à partir de réseaux virtuels Azure spécifiques ou vers des plages d’adresses IP Internet publiques. |Audit, Refuser, Désactivé |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Les comptes de stockage doivent utiliser un point de terminaison de service de réseau virtuel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Cette stratégie audite les comptes de stockage non configurés pour utiliser un point de terminaison de service de réseau virtuel. |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
