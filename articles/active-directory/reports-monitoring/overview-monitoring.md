---
title: En quoi consiste la surveillance d’Azure Active Directory ? (préversion) | Microsoft Docs
description: Fournit une vue d’ensemble de la surveillance d’Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 228d54f1b2b438c05779b96025d3ad03a7757beb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165091"
---
# <a name="what-is-azure-active-directory-monitoring-preview"></a>En quoi consiste la surveillance d’Azure Active Directory ? (préversion)

La surveillance d’Azure Active Directory (Azure AD) vous permet désormais d’acheminer vos journaux d’activité Azure AD vers différents points de terminaison. Vous pouvez ensuite la conserver pour une utilisation à long terme ou l’intégrer à des outils SIEM (Security Information and Event Management) tiers pour obtenir davantage d’informations sur votre environnement.

Actuellement, vous pouvez acheminer les journaux vers :

- Un compte de stockage Azure.
- Un Azure Event Hub pour vous permettre d’intégrer vos instances Splunk et Sumologic.
- Espace de travail Azure Log Analytics, dans lequel vous pouvez analyser les données, créer un tableau de bord et signaler des événements spécifiques

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="diagnostic-settings-configuration"></a>Configuration des paramètres de diagnostic

Pour configurer les paramètres de surveillance des journaux d’activité Azure AD, connectez-vous d’abord au [Portail Azure](https://portal.azure.com), puis sélectionnez **Azure Active Directory**. Vous pouvez ensuite accéder à la page de configuration des paramètres de diagnostic de deux façons :

* Sélectionnez **Paramètres de diagnostic** dans la section **Surveillance**.

    ![Paramètres de diagnostic](./media/overview-monitoring/diagnostic-settings.png)
    
* Sélectionnez **Journaux d’audit** ou **Connexions**, puis sélectionnez **Exporter les paramètres**. 

    ![Paramètres d’exportation](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Acheminer des journaux vers un compte de stockage

Si vous acheminez les journaux vers un compte de stockage Azure, vous pouvez les conserver pendant plus longtemps que la période de rétention par défaut décrite dans nos [stratégies de rétention](reference-reports-data-retention.md). Découvrez comment [acheminer les données vers votre compte de stockage](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Transmettre en continu des journaux vers un Event Hub

L’acheminement des journaux vers un Azure Event Hub vous permet d’intégrer des outils SIEM tiers comme Sumologic et Splunk. Cette intégration vous permet d’associer les données du journal d’activité d’Azure AD à d’autres données gérées par votre SIEM pour offrir des insights plus fournis sur votre environnement. Découvrez comment [transmettre les journaux à un Event Hub](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-log-analytics"></a>Envoyer des journaux à Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) est une solution qui regroupe les données de surveillance provenant de différentes sources et fournit un langage de requête et le moteur analytique vous donne des informations sur le fonctionnement de vos applications et de vos ressources. Si vous envoyez les journaux d’activité d’Azure AD à Log Analytics, vous pouvez rapidement récupérer, surveiller et signaler les données recueillies. Découvrez comment [envoyer des données à Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).

Vous pouvez également installer les vues prédéfinies des journaux d’activité d’Azure AD afin de surveiller les scénarios courants qui entraînent des connexions et des événements d’audit. Découvrez comment [installer et utiliser les vues Log Analytics des journaux d’activité Azure AD](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Étapes suivantes

* [Journaux d’activité dans Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Transmettre en continu des journaux vers un Event Hub](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Envoyer des journaux à Log Analytics](howto-integrate-activity-logs-with-log-analytics.md)
