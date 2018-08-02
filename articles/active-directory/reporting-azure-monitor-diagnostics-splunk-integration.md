---
title: Comment intégrer des journaux Azure Active Directory à Splunk à l’aide d’Azure Monitor (préversion)  | Microsoft Docs
description: Découvrez comment intégrer des journaux Azure Active Directory à Splunk à l’aide d’Azure Monitor (préversion).
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240374"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>Intégrer des journaux Azure Active Directory à Splunk à l’aide d’Azure Monitor (préversion)

Dans cet article, vous allez apprendre à intégrer des journaux Azure Active Directory à Splunk à l’aide d’Azure Monitor. Vous devez tout d’abord acheminer les journaux vers un Event Hub Azure, puis intégrez celui-ci à Splunk.

## <a name="prerequisites"></a>Prérequis

1. Event Hub Azure contenant les journaux d'activité Azure AD. En savoir plus sur la [diffusion en continu des journaux d’activité sur Event Hub](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
2. Suivez les [instructions](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) ci-dessous pour télécharger le module complémentaire Azure Monitor pour Splunk et configurer votre instance Splunk.

## <a name="tutorial"></a>Didacticiel 

1. Ouvrez votre instance Splunk et cliquez sur **Data Summary** (Résumé des données).
    ![Résumé des données](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "Résumé des données")

2. Accédez à l’onglet **Sourcetypes** (Types de sources) et sélectionnez **amal: aadal:audit**. ![onglet Sourcetypes](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "onglet Sourcetypes")

3. Vous verrez les journaux d’activité Azure AD comme dans l’illustration suivante.
    ![Journaux d’activité](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "Journaux d’activité")

> [!NOTE]
> Si vous ne pouvez pas installer de module complémentaire dans votre instance Splunk (par exemple, si vous utilisez un proxy ou exécutez sur un cloud Splunk), vous pouvez transférer ces événements au collecteur d’événements HTTP Splunk en utilisant cette [fonction qui est déclenchée par les nouveaux messages dans Event Hub](https://github.com/Microsoft/AzureFunctionforSplunkVS). 
>

## <a name="next-steps"></a>Étapes suivantes

* [Interpréter le schéma des journaux d’audit dans Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpréter le schéma des journaux de connexion dans Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Questions fréquentes et problèmes connus](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)