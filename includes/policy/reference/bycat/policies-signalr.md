---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8081624a533925b5a9c513b540c4296ed16a346e
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91820606"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SignalR Service doit utiliser des liaisons privées](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Auditez les ressources Azure SignalR Service qui n’ont pas au moins une connexion de point de terminaison privé approuvée. Les clients d’un réseau virtuel peuvent accéder de façon sécurisée aux ressources qui ont des connexions de point de terminaison privés via des liens privés. Pour plus d’informations, consultez [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink). |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
