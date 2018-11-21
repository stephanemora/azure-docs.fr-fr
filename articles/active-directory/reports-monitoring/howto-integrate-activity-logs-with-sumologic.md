---
title: Guide pratique pour intégrer des journaux Azure Active Directory avec SumoLogic à l’aide d’Azure Monitor (préversion) | Microsoft Docs
description: Découvrez comment intégrer des journaux Azure Active Directory avec SumoLogic à l’aide d’Azure Monitor (préversion).
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 4a39ee2fb057547c44c9eb08c85afdbb971ea5d5
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622402"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor-preview"></a>Intégrer des journaux Azure Active Directory avec SumoLogic à l’aide d’Azure Monitor (préversion).

Cet article explique comment intégrer des journaux Azure Active Directory (Azure AD) avec SumoLogic à l’aide d’Azure Monitor. Vous commencez pas router les journaux vers un hub d’événements Azure, puis vous intégrez ce hub d’événements avec SumoLogic.

## <a name="prerequisites"></a>Prérequis

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :
* Un hub d’événements Azure contenant les journaux d’activité d’Azure AD. Découvrez comment [diffuser en continu vos journaux d’activité sur un hub d’événements](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Un abonnement avec l’authentification unique activée à SumoLogic.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Étapes permettant d’intégrer des journaux Azure AD avec SumoLogic 

1. Commencez par [diffuser en continu des journaux Azure AD sur un hub d’événements Azure](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Configurez votre instance SumoLogic pour [collecter des journaux pour Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Installez l’application Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) pour utiliser les tableaux de bord préconfigurés qui fournissent une analyse en temps réel de votre environnement.

 ![tableau de bord](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

* [Interpréter le schéma des journaux d’audit dans Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpréter le schéma des journaux de connexion dans Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Questions fréquentes et problèmes connus](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
