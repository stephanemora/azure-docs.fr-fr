---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 83610d93368455357cf18e2ced8df3e10f1306ce
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806289"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditer les instances Azure Spring Cloud où le suivi distribué n’est pas activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Dans Azure Spring Cloud, les outils de suivi distribué permettent de déboguer et de superviser les interconnexions complexes entre les microservices dans une application. Ces outils doivent être activés et dans un état d’intégrité normal. |Audit, Désactivé |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud doit utiliser l’injection de réseau](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Les instances Azure Spring Cloud doivent utiliser l’injection de réseau virtuel pour les motifs suivants : 1. Isoler Azure Spring Cloud d’Internet. 2. Permettre à Azure Spring Cloud d’interagir avec des systèmes de centres de données locaux ou des services Azure d’autres réseaux virtuels. 3. Permettre aux clients de contrôler les communications réseau entrantes et sortantes pour Azure Spring Cloud. |Audit, Désactivé, Refus |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
