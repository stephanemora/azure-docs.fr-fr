---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cfed9beba0b33a1b401865a8bae6cc8307235624
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112041829"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le point de terminaison privé doit être activé pour les serveurs MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |Les connexions des points de terminaison privés permettent de sécuriser les communications en rendant la connectivité à Azure Database pour MySQL privée. Configurez une connexion de point de terminaison privé pour autoriser l’accès uniquement au trafic provenant de réseaux connus et empêcher l’accès à partir de toutes les autres adresses IP, y compris dans Azure. |AuditIfNotExists, Désactivé |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
