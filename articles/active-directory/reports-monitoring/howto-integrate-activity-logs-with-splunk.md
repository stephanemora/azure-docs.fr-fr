---
title: Intégrer des journaux d’activité Azure Active Directory à Splunk à l’aide d’Azure Monitor | Microsoft Docs
description: Découvrez comment intégrer des journaux d’activité Azure Active Directory avec SumoLogic à l’aide d’Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a26df2b37a249f808cc044b41960ca1e210a311a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988216"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Activation Intégrer des journaux d’activité Azure Active Directory à Splunk à l’aide d’Azure Monitor

Cet article explique comment intégrer des journaux d’activité Azure Active Directory (Azure AD) avec Splunk à l’aide d’Azure Monitor. Vous commencez pas router les journaux d’activité vers un hub d’événements Azure, puis vous intégrez le hub d’événements avec Splunk.

## <a name="prerequisites"></a>Prérequis

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :
* Un hub d’événements Azure contenant les journaux d’activité d’Azure AD. Découvrez comment [diffuser en continu vos journaux d’activité sur un hub d’événements](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Le module complémentaire Azure Monitor pour Splunk. [Télécharger et configurer votre instance Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Intégrer des journaux d’activité Azure Active Directory 

1. Ouvrez votre instance Splunk, puis sélectionnez **Data Summary** (Synthèse des données).

    ![Bouton « Synthèse des données »](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Sélectionnez l’onglet **Sourcetypes**, puis **amal: aadal:audit**

    ![Onglet Sourcetypes de Synthèse des données](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Les journaux d’activité d’Azure AD sont présentés dans la figure suivante :

    ![Journaux d’activité](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Si vous ne pouvez pas installer de module complémentaire dans votre instance Splunk (par exemple, si vous utilisez un proxy ou exécutez sur un cloud Splunk), vous pouvez transférer ces événements au collecteur d’événements HTTP Splunk. Pour ce faire, utilisez cette [fonction Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS) déclenchée par de nouveaux messages dans le hub d’événements. 
>

## <a name="next-steps"></a>Étapes suivantes

* [Interpréter le schéma des journaux d’audit dans Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpréter le schéma des journaux de connexion dans Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Questions fréquentes et problèmes connus](concept-activity-logs-azure-monitor.md#frequently-asked-questions)