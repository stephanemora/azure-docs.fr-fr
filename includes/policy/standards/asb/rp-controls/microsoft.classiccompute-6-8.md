---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4a42c886337f9ad222e28a1c0e2c9b413d29c1a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908639"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les contrôles d’application adaptatifs pour définir les applications sécurisées doivent être activés sur vos machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Activer les contrôles d’application pour définir la liste des applications reconnues fiables qui s’exécutent sur vos machines, et vous avertir quand d’autres applications s’exécutent. La sécurité de vos machines contre les logiciels malveillants s’en trouve renforcée. Pour simplifier le processus de configuration et de maintenance de vos règles, Security Center utilise le Machine Learning pour analyser les applications qui s’exécutent sur chaque machine et suggérer la liste des applications reconnues fiables. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
