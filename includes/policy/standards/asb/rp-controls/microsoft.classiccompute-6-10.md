---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7dcb488e28bdbb2e3fea113ee834e73a69144902
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122635524"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les contrôles d’application adaptatifs pour définir les applications sécurisées doivent être activés sur vos machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Activer les contrôles d’application pour définir la liste des applications reconnues fiables qui s’exécutent sur vos machines, et vous avertir quand d’autres applications s’exécutent. La sécurité de vos machines contre les logiciels malveillants s’en trouve renforcée. Pour simplifier le processus de configuration et de maintenance de vos règles, Security Center utilise le Machine Learning pour analyser les applications qui s’exécutent sur chaque machine et suggérer la liste des applications reconnues fiables. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
