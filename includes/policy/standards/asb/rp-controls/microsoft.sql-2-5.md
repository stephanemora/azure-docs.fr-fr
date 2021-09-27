---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5e73564010d4a78c14c87ed402d2aa2fc2501d84
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909658"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les serveurs SQL avec un audit dans la destination Compte de stockage doivent être configurés avec une conservation d'au moins 90 jours](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |À des fins d’investigation d’incident, nous vous recommandons de définir la conservation des données pour la fonctionnalité d’audit de votre serveur SQL Server sur au moins 90 jours. Confirmez que vous respectez les règles de conservation nécessaires pour les régions dans lesquelles vous travaillez. Cela est parfois nécessaire pour la conformité aux normes réglementaires. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
