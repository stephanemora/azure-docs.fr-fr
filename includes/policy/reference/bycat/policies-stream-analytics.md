---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9e76afe80c204ed2fa7cb3348e17099d43543b47
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112019521"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les travaux de Azure Stream Analytics doivent utiliser des clés gérées par le client pour chiffrer les données](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |Utilisez des clés gérées par le client pour stocker de manière sécurisée les ressources de métadonnées et de données privées de vos travaux Stream Analytics dans votre compte de stockage. Cela vous permet de contrôler totalement la manière dont vos données Stream Analytics sont chiffrées. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |
|[Les journaux de ressources dans Azure Stream Analytics doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
