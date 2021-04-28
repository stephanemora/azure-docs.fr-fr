---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9e8033f292c8ab94f6f5371b7bf0480bf2654be0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867713"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audite les machines virtuelles pour détecter si elles exécutent une solution d’évaluation des vulnérabilités prise en charge. L’identification et l’analyse des vulnérabilités constituent un composant fondamental de chaque programme de sécurité et d’évaluation des cyber-risques. Le niveau tarifaire Standard d’Azure Security Center comprend l’analyse des vulnérabilités de vos machines virtuelles sans coût supplémentaire. De plus, Security Center peut déployer cet outil automatiquement pour vous. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
