---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 90f4131a616d0cac93ac2a08999178ac7ea8a0eb
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "101728482"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le provisionnement automatique de l’agent Log Analytics doit être activé sur votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Pour superviser les menaces et les vulnérabilités de sécurité, Azure Security Center collecte des données à partir de vos machines virtuelles Azure. Les données sont collectées par l’agent Log Analytics, auparavant appelé Microsoft Monitoring Agent (MMA). Cet agent lit divers journaux d’événements et configurations liés à la sécurité de la machine, puis copie les données dans votre espace de travail Log Analytics à des fins d’analyse. Nous vous recommandons d’activer l’approvisionnement automatique pour déployer automatiquement l’agent sur toutes les machines virtuelles Azure prises en charge et sur toutes celles qui sont créées. |AuditIfNotExists, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Le profil de journal Azure Monitor doit collecter des journaux pour les catégories « écriture », « suppression » et « action »](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Cette stratégie garantit qu’un profil de journal collecte les journaux pour les catégories « write », « delete » et « action » |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[Azure Monitor doit collecter les journaux d’activité dans toutes les régions](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Cette stratégie effectue l’audit du profil de journal Azure Monitor qui n’exporte pas d’activités à partir de toutes les régions Azure prises en charge, notamment la région globale. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
