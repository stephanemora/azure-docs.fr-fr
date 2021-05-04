---
title: Microsoft Cloud App Security des alertes non importées dans Azure Sentinel via l’intégration Microsoft 365 Defender | Microsoft Docs
description: Cet article affiche les alertes de Microsoft Cloud App Security qui doivent être ingérées directement dans Azure Sentinel, car elles ne sont pas collectées par Microsoft 365 Defender.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 0232cbde5aaddad268aa0e4725ee3587a2461800
ms.sourcegitcommit: 18cd3c1c8cc47258c6a1a04e0e03d6248c52ef24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107992732"
---
# <a name="microsoft-cloud-app-security-alerts-not-imported-into-azure-sentinel-through-microsoft-365-defender-integration"></a>Microsoft Cloud App Security des alertes non importées dans Azure Sentinel via l’intégration Microsoft 365 Defender

À l’instar des autres composants Microsoft Defender (Defender pour point de terminaison, Defender pour Identity et Defender pour Office 365), Microsoft Cloud App Security génère des alertes collectées par Microsoft 365 Defender. Microsoft 365 Defender à son tour produit des incidents qui sont ingérés par et [synchronisés avec Azure Sentinel](microsoft-365-defender-sentinel-integration.md#microsoft-365-defender-incidents-and-microsoft-incident-creation-rules) lorsque le connecteur Microsoft 365 Defender est activé.

Contrairement aux trois autres composants, **certains types d’alertes de** Cloud App Security ne sont pas intégrés à Microsoft 365 Defender. Ainsi, si vous souhaitez accéder aux incidents de toutes les alertes Cloud App Security dans Azure Sentinel, vous devez ajuster vos règles d’analyse de création d’incident Microsoft en conséquence, afin que les alertes qui sont ingérées directement dans Sentinel continuent à générer des incidents, contrairement à celles intégrées à Microsoft 365 Defender (il n’y a donc pas de doublons).

## <a name="cloud-app-security-alerts-not-onboarded-to-microsoft-365-defender"></a>Alertes Cloud App Security non intégrées à Microsoft 365 Defender

Les alertes suivantes ne sont pas intégrées à Microsoft 365 Defender, et les règles de création d’incident Microsoft qui se traduisent par ces alertes doivent continuer à être configurées pour générer des incidents.

| Nom d’affichage de l’alerte Cloud App Security | Nom d’alerte Cloud App Security |
|-|-|
| **Alerte de stratégie d’accès** | `ALERT_CABINET_INLINE_EVENT_MATCH` |
| **La création d’activité à partir du journal Discovered Traffic a dépassé la limite quotidienne** | `ALERT_DISCOVERY_TRAFFIC_LOG_EXCEEDED_LIMIT` |
| **Alerte de stratégie d’activité** | `ALERT_CABINET_EVENT_MATCH_AUDIT` |
| **Alerte d’exfiltration anormale** | `ALERT_EXFILTRATION_DISCOVERY_ANOMALY_DETECTION` |
| **Compte compromis** | `ALERT_COMPROMISED_ACCOUNT` |
| **Alerte de violation de la sécurité de l’application découverte** | `ALERT_MANAGEMENT_DISCOVERY_BREACHED_APP` |
| **Compte inactif** | `ALERT_ZOMBIE_USER` |
| **Le score de priorité d’investigation a augmenté** | `ALERT_UEBA_INVESTIGATION_PRIORITY_INCREASE` |
| **Consentement de l’application OAuth malveillante** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MALICIOUS_OAUTH_APP_CONSENT` |
| **Nom d’application OAuth équivoque** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MISLEADING_APP_NAME` |
| **Nom d’éditeur équivoque pour une application OAuth** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MISLEADING_PUBLISHER_NAME` |
| **Nouvelle application découverte** | `ALERT_CABINET_DISCOVERY_NEW_SERVICE` |
| **Une URL de redirection non sécurisée est utilisée par une application OAuth** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_NON_SECURE_REDIRECT_URL` |
| **Alerte de stratégie d’application OAuth** | `ALERT_CABINET_APP_PERMISSION` |
| **Alerte d’activité suspecte** | `ALERT_SUSPICIOUS_ACTIVITY` |
| **Alerte d’utilisation suspecte du cloud** | `ALERT_DISCOVERY_ANOMALY_DETECTION` |
| **Nom d’application OAuth suspect** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_SUSPICIOUS_APP_NAME` |
| **Erreur de connecteur d’applications d’alerte système** | `ALERT_MANAGEMENT_DISCONNECTED_API` |
| **Alerte système d’erreur de chargement automatique du journal Cloud Discovery** | `ALERT_MANAGEMENT_LOG_COLLECTOR_LOW_RATE` |
| **Alerte système d’erreur de traitement du journal Cloud Discovery** | `ALERT_MANAGEMENT_LOG_COLLECTOR_CONSTANTLY_FAILED_PARSING` |
| **Erreur de connecteur ICAP d’alerte système** | `ALERT_MANAGEMENT_DLP_CONNECTOR_ERROR` |
| **Erreur d’agent SIEM d’alerte système** | `ALERT_MANAGEMENT_DISCONNECTED_SIEM` |
| **Notifications d’agent SIEM d’alerte système** | `ALERT_MANAGEMENT_NOTIFICATIONS_SIEM` |
| **Région inhabituelle pour la ressource cloud** | `MCAS_ALERT_ANUBIS_DETECTION_UNCOMMON_CLOUD_REGION` |
|

## <a name="next-steps"></a>Étapes suivantes

- [Connecter Microsoft 365 Defender à Azure Sentinel](connect-microsoft-365-defender.md)
- En savoir plus sur [Azure Sentinel](overview.md), [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender) et [Cloud App Security](/cloud-app-security/what-is-cloud-app-security).
