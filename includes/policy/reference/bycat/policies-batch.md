---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 865bbb093c9a02c0989a54d08bf1d956ffb3a3ef
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429190"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le compte Azure Batch doit utiliser des clés gérées par le client pour chiffrer les données](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99e9ccd8-3db9-4592-b0d1-14b1715a4d8a) |Utilisez des clés gérées par le client pour gérer le chiffrement au repos des données de votre compte Batch. Par défaut, les données client sont chiffrées avec des clés gérées par le service, mais des clés gérées par le client sont généralement requises pour répondre aux normes de conformité réglementaire. Les clés gérées par le client permettent de chiffrer les données avec une clé Azure Key Vault que vous avez créée et dont vous êtes le propriétaire. Vous avez le contrôle total et la responsabilité du cycle de vie des clés, notamment leur permutation et leur gestion. Pour en savoir plus, rendez-vous sur [https://aka.ms/Batch-CMK](https://aka.ms/Batch-CMK). |Audit, Refuser, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_CustomerManagedKey_Audit.json) |
|[Des règles d’alerte de métrique doivent être configurées sur les comptes Batch](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |Auditez la configuration des règles d’alerte de métrique sur le compte Batch pour activer la métrique requise. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |
|[L’accès au réseau public doit être désactivé pour les comptes Batch](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c5a0ae-5e48-4738-b093-65e23a060488) |Le fait de désactiver l’accès au réseau public sur un compte Batch permet d’améliorer la sécurité, car votre compte Batch n’est accessible qu’à partir d’un point de terminaison privé. Apprenez-en davantage sur la désactivation de l’accès au réseau public à la page [https://docs.microsoft.com/azure/batch/private-connectivity](https://docs.microsoft.com/azure/batch/private-connectivity). |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_DisablePublicNetworkAccess_Audit.json) |
|[Les journaux de ressources dans les comptes Batch doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
