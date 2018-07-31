---
title: 'Didacticiel : Diffuser en continu des journaux Azure Active Directory sur un Event Hub Azure (préversion) | Microsoft Docs'
description: Découvrez comment configurer Azure Diagnostics pour envoyer des journaux Azure Active Directory vers un Hub Event (préversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240174"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>Didacticiel : Diffuser en continu des journaux Azure Active Directory sur un hub d’événements Azure (aperçu)

Dans ce didacticiel, découvrez comment configurer les paramètres de diagnostic Azure Monitor pour diffuser en continu des journaux Azure Active Directory sur un hub d’événements Azure. Ce mécanisme permet d’intégrer vos journaux à des outils SIEM tiers tels que Splunk et QRadar.

## <a name="prerequisites"></a>Prérequis 

Ce dont vous avez besoin :

* Un abonnement Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Un client Azure Active Directory
* Un utilisateur, qui est un administrateur global ou un administrateur de la sécurité pour ce locataire
* Un espace de noms Event Hubs et un hub d’événements dans votre abonnement Azure. [Découvrez ici comment en créer un](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md).

## <a name="archive-logs-to-event-hub"></a>Archiver des journaux sur un hub d’événements

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Cliquez sur **Azure Active Directory** -> **Activité** -> **journaux d’audit**. 
3. Cliquez sur **Exporter les paramètres** pour ouvrir le panneau Paramètres de diagnostic. Cliquez sur **Modifier les paramètres** si vous souhaitez modifier les paramètres existants, ou sur **Add diagnostic setting (Ajouter un paramètre de diagnostic)** pour ajouter un nouveau paramètre. Vous pouvez avoir jusqu’à trois paramètres. 
    ![Exporter les paramètres](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "Exporter les paramètres")

4. Cochez la case **Stream to an event hub (Diffuser sur un hub d’événements)**, puis cliquez sur **Event Hub/Configure (Hub d’événements/Configurer)**.
5. Sélectionnez un abonnement Azure et un espace de noms Event Hubs vers lesquels vous voulez acheminer les journaux. L’abonnement et l’espace de noms Event Hubs doivent tous deux être associés au locataire Active Directory à partir duquel les journaux sont diffusés. Vous pouvez également spécifier un hub d’événements au sein de l’espace de noms Event Hubs vers lequel les journaux doivent être envoyés. Si aucun hub d’événements n’est spécifié, il sera créé dans l’espace de noms avec le nom par défaut **insights-logs-audit**.
6. Cliquez sur **OK** pour quitter la configuration du hub d’événements.
7. Cochez la case **AuditLogs** pour envoyer des journaux d’audit vers le compte de stockage. 
8. Cochez la case **SignInLogs** pour envoyer des journaux de connexion vers le compte de stockage.
9. Cliquez sur **Enregistrer** pour enregistrer le paramètre.
    ![Paramètres de diagnostic](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "Paramètres de diagnostic")

10. Au bout de 15 minutes, vérifiez que les événements apparaissent dans votre hub d’événements. Pour ce faire, accédez au hub d’événements à partir du portail et vérifiez que le compte de **messages entrants** est supérieur à zéro. 
    ![Journaux d’audit](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "Journaux d’Audit")


## <a name="access-data-from-event-hubs"></a>Accéder aux données depuis Event Hubs

Une fois que les données apparaissent dans le hub d’événements, vous pouvez y accéder de deux manières différentes.

* **Configurer un outil SIEM pris en charge pour lire les données** : la plupart des outils nécessitent la chaîne de connexion du hub d’événements, ainsi que certaines autorisations de votre abonnement Azure, pour lire les données du hub d’événements. Voici une liste non exhaustive d’outils avec intégration Azure Monitor :
    1. **Splunk** : pour plus d’informations sur l’intégration des journaux Azure AD à  Splunk, consultez [How to integrate Azure Active Directory logs with Splunk using Azure Monitor (Comment intégrer les journaux Azure Active Directory avec Splunk à l’aide d’Azure Monitor)](reporting-azure-monitor-diagnostics-splunk-integration.md).
    
    2. **IBM QRadar** : le module DSM Microsoft Azure et le protocole Microsoft Azure Event Hubs sont disponibles au téléchargement sur le [site web du support IBM](http://www.ibm.com/support). Pour plus d’informations sur l’intégration à Azure [cliquez ici](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    3. **SumoLogic**: suivez [ces instructions](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) pour configurer SumoLogic de manière à utiliser les données d’un hub d’événements. 

* **Configurer les outils personnalisés pour lire les données** : Si votre SIEM actuel n’est pas encore pris en charge dans les diagnostics d’Azure Monitor, vous pouvez configurer des outils personnalisés à l’aide des API de hub d’événements. Pour plus d’informations, accédez aux [API de hub d’événements](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Étapes suivantes

* [Intégrer des journaux Azure Active Directory à Splunk à l’aide des diagnostics d’Azure Monitor](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Interpréter le schéma des journaux d’audit dans les diagnostics d’Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpréter le schéma des journaux de connexion dans les diagnostics d’Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)