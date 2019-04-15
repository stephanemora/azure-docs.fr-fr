---
title: Comment intégrer des journaux d’activité Azure Active Directory avec Splunk à l’aide d’Azure Monitor (préversion)  | Microsoft Docs
description: Découvrez comment intégrer des journaux d’activité Azure Active Directory avec Splunk à l’aide d’Azure Monitor (préversion).
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
ms.openlocfilehash: 42dbb8c7e74bd3acb99028477f34f99f1334d577
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503840"
---
# <a name="integrate-azure-ad-logs-with-splunk-by-using-azure-monitor-preview"></a>Intégrer des journaux d’activité Azure AD avec Splunk à l’aide d’Azure Monitor (préversion)

Cet article explique comment intégrer des journaux d’activité Azure Active Directory (Azure AD) avec Splunk à l’aide d’Azure Monitor. Vous commencez pas router les journaux d’activité vers un hub d’événements Azure, puis vous intégrez le hub d’événements avec Splunk.

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :
* Un hub d’événements Azure contenant les journaux d’activité d’Azure AD. Découvrez comment [diffuser en continu vos journaux d’activité sur un hub d’événements](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
* Le module complémentaire Azure Monitor pour Splunk. [Télécharger et configurer votre instance Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Didacticiel 

1. Ouvrez votre instance Splunk, puis sélectionnez **Data Summary** (Synthèse des données).

    ![Bouton « Synthèse des données »](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png)

2. Sélectionnez l’onglet **Sourcetypes**, puis **amal: aadal:audit**

    ![Onglet Sourcetypes de Synthèse des données](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png)

    Les journaux d’activité d’Azure AD sont présentés dans la figure suivante :

    ![Journaux d’activité](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png)

> [!NOTE]
> Si vous ne pouvez pas installer de module complémentaire dans votre instance Splunk (par exemple, si vous utilisez un proxy ou exécutez sur un cloud Splunk), vous pouvez transférer ces événements au collecteur d’événements HTTP Splunk. Pour ce faire, utilisez cette [fonction Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS) déclenchée par de nouveaux messages dans le hub d’événements. 
>

## <a name="next-steps"></a>Étapes suivantes

* [Interpréter le schéma des journaux d’audit dans Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpréter le schéma des journaux de connexion dans Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Questions fréquentes et problèmes connus](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)
