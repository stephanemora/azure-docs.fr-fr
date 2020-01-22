---
title: Contrôle de sécurité Azure - Journalisation et supervision
description: Journalisation et supervision dans le cadre du contrôle de sécurité
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ceb82bf729470f9996bc9ecc766329a0d1242ec4
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930029"
---
# <a name="security-control-logging-and-monitoring"></a>Contrôle de sécurité : Journalisation et supervision

La journalisation et la supervision de la sécurité se concentrent sur les activités liées à l’activation, l’acquisition et le stockage des journaux d’audit pour les services Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1 : Utiliser des sources de synchronisation date/heure approuvées

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft gère les sources de temps pour les ressources Azure. Toutefois, vous avez la possibilité de gérer les paramètres de synchronisation date/heure pour vos ressources de calcul.

Comment configurer la synchronisation date/heure pour des ressources de calcul Azure :

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 2.2 | 6.5, 6.6 | Customer |

Ingérez des journaux par le biais d’Azure Monitor pour agréger les données de sécurité générées par les appareils de point de terminaison, les ressources réseau et d’autres systèmes de sécurité. Dans Azure Monitor, utilisez des espaces de travail Log Analytics pour interroger et effectuer l’analytique, puis utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage.

Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce. Comment intégrer Azure Sentinel :

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Comment collecter des journaux et des métriques de plateforme avec Azure Monitor :

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Comment collecter les journaux des hôtes internes des machines virtuelles Azure avec Azure Monitor :

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Comment démarrer avec Azure Monitor et l’intégration d’une solution SIEM tierce :

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 2.3 | 6.2, 6.3 | Customer |

Activez les paramètres de diagnostic sur les ressources Azure pour accéder aux journaux d’audit, de sécurité et de diagnostic. Les journaux d’activité, automatiquement disponibles, incluent la source de l’événement, la date, l’utilisateur, l’horodatage, les adresses sources, les adresses de destination et d’autres éléments utiles.

Comment collecter des journaux et des métriques de plateforme avec Azure Monitor :

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Comprendre la journalisation et les différents types de journaux dans Azure :

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 : Collecter les journaux de sécurité des systèmes d’exploitation

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 2.4 | 6.2, 6.3 | Customer |

Si la ressource de calcul appartient à Microsoft, alors Microsoft est responsable de sa supervision. Si la ressource de calcul appartient à votre organisation, il vous incombe de la superviser. Vous pouvez utiliser Azure Security Center pour superviser le système d’exploitation. Les données collectées par Security Center auprès du système d’exploitation incluent le type et la version du système d’exploitation, le système d’exploitation (journaux des événements Windows), les processus en cours d’exécution, le nom de l’ordinateur, les adresses IP et l’utilisateur connecté. L’agent Log Analytics collecte également les fichiers de vidage sur incident.

Comment collecter les journaux des hôtes internes des machines virtuelles Azure avec Azure Monitor :

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Comprendre la collecte de données par Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 2.5 | 6.4 | Customer |

Dans Azure Monitor, définissez la période de conservation de votre espace de travail Log Analytics en fonction des obligations réglementaires de votre organisation. Utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage.

Comment définir les paramètres de conservation des journaux pour les espaces de travail Log Analytics :

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 2.6 | 6.7 | Customer |

Analysez et supervisez les journaux pour détecter les comportements anormaux et examinez régulièrement les résultats. Utilisez l’espace de travail Log Analytics d’Azure Monitor pour examiner les journaux et effectuer des requêtes sur leurs données.

Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce. 

Comment intégrer Azure Sentinel :

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Comprendre l’espace de travail Log Analytics :

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Comment exécuter des requêtes personnalisées dans Azure Monitor :

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 : Activer des alertes en cas d’activité anormale

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 2.7 | 6.8 | Customer |

Utilisez Azure Security Center avec un espace de travail Log Analytics pour superviser les activités anormales détectées dans les journaux de sécurité et les événements et générer des alertes s’y rapportant.

Vous pouvez également activer et intégrer les données dans Azure Sentinel.

Comment intégrer Azure Sentinel :

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Comment gérer les alertes dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Comment générer une alerte sur des données de journal Log Analytics :

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8 : Centraliser la journalisation anti-programme malveillant

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 2.8 | 8.6 | Customer |

Activez la collecte des événements anti-programme malveillant pour les machines virtuelles et services cloud Azure.

Comment configurer Microsoft Antimalware pour les machines virtuelles :

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Comment configurer Microsoft Antimalware pour les services cloud :

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Comprendre Microsoft Antimalware :

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9 : Activer la journalisation des requêtes DNS

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 2,9 | 8,7 | Customer |

Implémentez une solution tierce pour la journalisation DNS.

## <a name="210-enable-command-line-audit-logging"></a>2.10 : Activer l’enregistrement d’audit en ligne de commande

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 2.1 | 8.8 | Customer |

Configurez manuellement la journalisation de la console et la transcription PowerShell au niveau de chaque nœud.

## <a name="next-steps"></a>Étapes suivantes

Reportez-vous au contrôle de sécurité suivant : [Identité et contrôle d’accès](security-control-identity-access-control.md)
