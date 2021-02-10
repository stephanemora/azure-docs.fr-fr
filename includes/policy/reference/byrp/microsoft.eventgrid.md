---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6b8139d183dbf4ba5cacfbb7f68e4175ccff19b2
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99559124"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les domaines Azure Event Grid doivent utiliser une liaison privée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link vous permet de connecter votre réseau virtuel aux services Azure sans adresse IP publique au niveau ni de la source ni de la destination. La plateforme de liaison privée gère la connectivité entre le consommateur et les services sur le réseau principal Azure. En faisant correspondre des points de terminaison privés à vos domaines Event Grid plutôt qu’à l’ensemble du service, vous vous protégez également contre les risques de fuite de données. Pour plus d’informations, consultez la page [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints). |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Les rubriques Azure Event Grid doivent utiliser une liaison privée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link vous permet de connecter votre réseau virtuel aux services Azure sans adresse IP publique au niveau ni de la source ni de la destination. La plateforme de liaison privée gère la connectivité entre le consommateur et les services sur le réseau principal Azure. En faisant correspondre des points de terminaison privés à vos rubriques plutôt qu’à l’ensemble du service, vous vous protégez également contre les risques de fuite de données. Pour plus d’informations, consultez la page [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints). |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
