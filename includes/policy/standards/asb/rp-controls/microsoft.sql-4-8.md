---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c5e2f35e91b6c5f4f976ab9d941cc328fb42be24
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511626"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les instances managées SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |L’implémentation de TDE (Transparent Data Encryption) avec votre propre clé vous offre les avantages suivants : transparence et contrôle améliorés sur le protecteur TDE, sécurité renforcée avec un service externe HSM et promotion de la séparation des tâches. Cette recommandation s’applique aux organisations ayant une exigence de conformité associée. |AuditIfNotExists, Désactivé |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Les serveurs SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |L’implémentation de TDE (Transparent Data Encryption) avec votre propre clé vous offre les avantages suivants : transparence et contrôle améliorés sur le protecteur TDE, sécurité renforcée avec un service externe HSM et promotion de la séparation des tâches. Cette recommandation s’applique aux organisations ayant une exigence de conformité associée. |AuditIfNotExists, Désactivé |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Transparent Data Encryption sur les bases de données SQL doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Le chiffrement transparent des données doit être activé pour protéger les données au repos et respecter les conditions de conformité requises |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
