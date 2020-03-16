---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: bcb1198b83904ce62021ce195b02042b22e3df0e
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669214"
---
|Nom |Description |Effet(s) |Version |GitHub |
|---|---|---|---|---|
|[Les journaux de diagnostic dans Azure Data Lake Store doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Auditer l’activation des journaux de diagnostic. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |2.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json)
|[Les journaux de diagnostic dans Data Lake Analytics doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Auditer l’activation des journaux de diagnostic. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |2.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json)
|[Exiger un chiffrement sur les comptes Data Lake Store](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Cette stratégie garantit que le chiffrement est activé sur tous les comptes Data Lake Store |deny |1.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json)
