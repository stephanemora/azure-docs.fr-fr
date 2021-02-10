---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 53d3aa63314be0d16eac0166f923d9c672447237
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99561326"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SignalR Service doit utiliser une liaison privée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link vous permet de connecter votre réseau virtuel aux services Azure sans adresse IP publique au niveau de la source ou de la destination. La plateforme de la liaison privée gère la connectivité entre le consommateur et les services sur le réseau principal Azure. Si vous mappez des points de terminaison privés sur vos ressources SignalR au lieu de l’ensemble du service, vous êtes également protégé contre les risques de fuite de données. Pour en savoir plus : [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink). |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
