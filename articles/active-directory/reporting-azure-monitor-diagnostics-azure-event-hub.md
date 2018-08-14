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
ms.openlocfilehash: 50e7b7c35386bafe521078308a8809c4d3038dd8
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501694"
---
# <a name="tutorial-stream-azure-ad-logs-to-an-azure-event-hub-preview"></a>Didacticiel : diffuser en continu des journaux Azure Active Directory sur un hub d’événements Azure (aperçu)

Dans ce didacticiel, découvrez comment configurer les paramètres de diagnostic Azure Monitor pour diffuser en continu des journaux Azure Active Directory sur un hub d’événements Azure. Ce mécanisme permet d’intégrer vos journaux Security Information and Event Management (SIEM) tiers, tels que Splunk et QRadar.

## <a name="prerequisites"></a>Prérequis 

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Un locataire Azure AD.
* Utilisateur considéré comme *administrateur général* ou *administrateur de la sécurité* pour un locataire Azure AD.
* Espace de noms Event Hubs et hub d’événements de votre abonnement Azure. Découvrez comment [créer un hub d’événements](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md).

## <a name="archive-logs-to-an-event-hub"></a>Archiver des journaux dans un hub d’événements

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 

2. Sélectionnez **Azure Active Directory** > **Activité** > **Journaux d’audit**. 

3. Sélectionnez **Paramètres d’exportation**.  
    
4. Dans le volet **Paramètres de diagnostic**, effectuez l’une des opérations suivantes :
    * Pour modifier les paramètres existants, sélectionnez **Modifier le paramètre**.
    * Pour ajouter de nouveaux paramètres, sélectionnez **Ajouter un paramètre de diagnostic**.  
      Vous pouvez avoir jusqu’à trois paramètres.

      ![Paramètres d’exportation](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png)

5. Cochez la case **Stream to an event hub (Diffuser sur un hub d’événements)**, puis cliquez sur **Event Hub/Configure (Hub d’événements/Configurer)**.

6. Sélectionnez un abonnement Azure et un espace de noms Event Hubs vers lequel acheminer les journaux.  
    L’abonnement et l’espace de noms Event Hubs doivent tous deux être associés au locataire Azure AD à partir duquel les journaux sont diffusés. Vous pouvez également spécifier un hub d’événements au sein de l’espace de noms Event Hubs vers lequel les journaux doivent être envoyés. Si aucun hub d’événements n’est spécifié, il sera créé dans l’espace de noms et aura pour nom par défaut **insights-logs-audit**.

7. Cliquez sur **OK** pour quitter la configuration du hub d’événements.

8. Effectuez une ou plusieurs des actions suivantes :
    * Cochez la case **AuditLogs** pour envoyer les journaux d’audit vers le compte de stockage. 
    * Cochez la case **SignInLogs** pour envoyer les journaux de connexion vers le compte de stockage.

9. Sélectionnez **Enregistrer** pour enregistrer le paramètre.

    ![Paramètres de diagnostic](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png)

10. Au bout de 15 minutes, vérifiez que les événements apparaissent bien dans votre hub d’événements. Pour cela, accédez au hub d’événements à partir du portail et vérifiez que le nombre de **messages entrants** est supérieur à zéro. 

    ![Journaux d’audit](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Accéder aux données depuis votre hub d’événements

Une fois que les données apparaissent dans le hub d’événements, vous pouvez y accéder et les lire de deux manières :

* **Configurez un outil SIEM pris en charge**. Pour lire les données du hub d’événements, la plupart des outils nécessitent la chaîne de connexion du hub d’événements, ainsi que certaines autorisations de votre abonnement Azure. Voici une liste non exhaustive des outils tiers avec intégration Azure Monitor :
    * **Splunk** : pour plus d’informations sur l’intégration des journaux Azure AD à Splunk, consultez [Comment intégrer les journaux Azure AD à Splunk à l’aide d’Azure Monitor](reporting-azure-monitor-diagnostics-splunk-integration.md).
    
    * **IBM QRadar** : vous pouvez télécharger le module DSM et le protocole Azure Event Hub depuis l’adresse du [service d’assistance d’IBM](http://www.ibm.com/support). Pour plus d’informations sur l’intégration avec Azure, accédez au site [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    * **Sumo Logic** : pour configurer Sumo Logic et consommer des données à partir d’un hub d’événements, consultez [Collecter les journaux pour les journaux d’activité Azure à partir d’un hub d’événements](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). 

* **Configurer des outils personnalisés** Si votre SIEM actuel n’est pas encore pris en charge dans les diagnostics d’Azure Monitor, vous pouvez configurer des outils personnalisés à l’aide des API Event Hubs. Pour plus d’informations, consultez la section [Prise en main de la réception des messages à partir d’un hub d’événements](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Étapes suivantes

* [Intégrer les journaux Azure AD à Splunk à l’aide d’Azure Monitor](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Interpréter le schéma des journaux d’audit dans Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpréter le schéma des journaux de connexion dans Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
