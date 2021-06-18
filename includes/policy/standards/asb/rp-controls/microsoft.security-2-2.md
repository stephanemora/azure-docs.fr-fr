---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 821544a7d8d61350d969e0ae5dea7d23e5cd1d3a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112041404"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le provisionnement automatique de l’agent Log Analytics doit être activé sur votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Pour superviser les menaces et les vulnérabilités de sécurité, Azure Security Center collecte des données à partir de vos machines virtuelles Azure. Les données sont collectées par l’agent Log Analytics, auparavant appelé Microsoft Monitoring Agent (MMA). Cet agent lit divers journaux d’événements et configurations liés à la sécurité de la machine, puis copie les données dans votre espace de travail Log Analytics à des fins d’analyse. Nous vous recommandons d’activer l’approvisionnement automatique pour déployer automatiquement l’agent sur toutes les machines virtuelles Azure prises en charge et sur toutes celles qui sont créées. |AuditIfNotExists, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
