---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cf4cb251ea84c6913502b4d518a0266ec93b8399
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108790983"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’évaluation des vulnérabilités doit être activée sur SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditez chaque instance managée SQL qui n’a pas d’analyses récurrentes d’évaluation des vulnérabilités activées. L’évaluation des vulnérabilités peut découvrir et suivre les potentielles vulnérabilités de base de données, et vous aider à y remédier. |AuditIfNotExists, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Auditez les serveurs Azure SQL qui n’ont pas d’analyses récurrentes d’évaluation des vulnérabilités activées. L’évaluation des vulnérabilités peut découvrir et suivre les potentielles vulnérabilités de base de données, et vous aider à y remédier. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
