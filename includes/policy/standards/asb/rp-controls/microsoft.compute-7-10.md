---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 100560a14a3040d9d5f7b0ca522accf7ecbe7652
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110164008"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les vulnérabilités dans les configurations de la sécurité des conteneurs doivent être corrigées](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Auditer les vulnérabilités dans la configuration de sécurité sur les machines où Docker est installé et afficher en tant que recommandations dans Azure Security Center. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[Les vulnérabilités dans la configuration de la sécurité sur vos machines doivent être corrigées](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Les serveurs qui ne respectent pas la base de référence configurée seront supervisés par Azure Security Center en tant que recommandation |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Les vulnérabilités dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Auditez les vulnérabilités de système d’exploitation dans vos groupes de machines virtuelles identiques pour les protéger des attaques. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
