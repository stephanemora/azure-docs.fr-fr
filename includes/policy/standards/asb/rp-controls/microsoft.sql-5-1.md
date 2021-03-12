---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d4dec37643ca695eeeddec9e41e0623e68201791
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442633"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’évaluation des vulnérabilités doit être activée sur SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditez chaque instance managée SQL qui n’a pas d’analyses récurrentes d’évaluation des vulnérabilités activées. L’évaluation des vulnérabilités peut découvrir et suivre les potentielles vulnérabilités de base de données, et vous aider à y remédier. |AuditIfNotExists, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Auditez les serveurs Azure SQL qui n’ont pas d’analyses récurrentes d’évaluation des vulnérabilités activées. L’évaluation des vulnérabilités peut découvrir et suivre les potentielles vulnérabilités de base de données, et vous aider à y remédier. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
