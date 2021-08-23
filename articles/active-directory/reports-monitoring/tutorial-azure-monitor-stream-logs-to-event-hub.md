---
title: 'Tutoriel : Diffuser des journaux en continu sur un hub d’événements Azure | Microsoft Docs'
description: Découvrez comment configurer Diagnostics Azure pour envoyer (push) des journaux Azure Active Directory vers un hub d’événements.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/23/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: cff544e3d9ba2851db02e0e4440ba9f97b240e68
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580833"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub"></a>Tutoriel : Diffuser en continu des journaux Azure Active Directory sur un hub d’événements Azure

Dans ce didacticiel, vous découvrez comment configurer les paramètres de diagnostic Azure Monitor pour diffuser en continu des journaux d’activité Azure Active Directory (Azure AD) sur un hub d’événements Azure. Ce mécanisme permet d’intégrer vos journaux d’activité Security Information and Event Management (SIEM) tiers, tels que Splunk et QRadar.

## <a name="prerequisites"></a>Prérequis 

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Un locataire Azure AD.
* Utilisateur considéré comme *administrateur général* ou *administrateur de la sécurité* pour un locataire Azure AD.
* Espace de noms Event Hubs et hub d’événements de votre abonnement Azure. Découvrez comment [créer un hub d’événements](../../event-hubs/event-hubs-create.md).

## <a name="stream-logs-to-an-event-hub"></a>Transmettre en continu des journaux d’activité vers un hub d’événements

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Sélectionnez **Azure Active Directory** > **Supervision** > **Journaux d’audit**. 

3. Sélectionnez **Paramètres d’exportation**.  
    
4. Dans le volet **Paramètres de diagnostic**, effectuez l’une des opérations suivantes :
    * Pour modifier les paramètres existants, sélectionnez **Modifier le paramètre**.
    * Pour ajouter de nouveaux paramètres, sélectionnez **Ajouter un paramètre de diagnostic**.  
      Vous pouvez avoir jusqu’à trois paramètres.

      ![Paramètres d’exportation](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. Cochez la case **Stream to an event hub (Diffuser sur un hub d’événements)** , puis cliquez sur **Event Hub/Configure (Hub d’événements/Configurer)** .

6. Sélectionnez un abonnement Azure et un espace de noms Event Hubs vers lequel acheminer les journaux d’activité.  
    L’abonnement et l’espace de noms Event Hubs doivent tous deux être associés au locataire Azure AD à partir duquel les journaux d’activité sont diffusés. Vous pouvez également spécifier un hub d’événements au sein de l’espace de noms Event Hubs vers lequel les journaux d’activité doivent être envoyés. Si aucun hub d’événements n’est spécifié, il sera créé dans l’espace de noms et aura pour nom par défaut **insights-logs-audit**.

7. Cliquez sur **OK** pour quitter la configuration du hub d’événements.

8. Faites n'importe quelle combinaison des éléments suivants :
    - Pour envoyer les journaux d’audit vers le hub d’événements, cochez la case **AuditLogs**. 
    - Pour envoyer les journaux de connexion interactifs de l’utilisateur vers le hub d’événements, cochez la case **SignInLogs**.
    - Pour envoyer les journaux de connexion non-interactifs de l’utilisateur vers le hub d’événements, cochez la case **NonInteractiveUserSignInLogs**. 
    - Pour envoyer les journaux de connexion du principal du service vers le hub d’événements, activez la case à cocher **ServicePrincipalSignInLogs**.
    - Pour envoyer les journaux de connexion à l’identité gérée vers le hub d’événements, activez la case à cocher **ManagedIdentitySignInLogs**.
    - Pour envoyer les journaux de provisionnement vers le hub d’événements, activez la case à cocher **ProvisioningLogs**.
    - Pour envoyer des connexions envoyées à Azure AD par un agent AD FS Connecter Health, activez la case à cocher **ADFSSignInLogs**.

    >[!Note]
    >Certaines catégories de connexion contiennent de grandes quantités de données de journal en fonction de la configuration de votre locataire. En général, les connexions utilisateur non interactives et les connexions de principal du service peuvent être 5 à 10 fois plus volumineuses que les connexions utilisateur interactives.

9. Sélectionnez **Enregistrer** pour enregistrer le paramètre.

10. Au bout de 15 minutes, vérifiez que les événements apparaissent bien dans votre hub d’événements. Pour cela, accédez au hub d’événements à partir du portail et vérifiez que le nombre de **messages entrants** est supérieur à zéro. 

    ![Journaux d’audit](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Accéder aux données depuis votre hub d’événements

Une fois que les données apparaissent dans le hub d’événements, vous pouvez y accéder et les lire de deux manières :

* **Configurez un outil SIEM pris en charge**. Pour lire les données du hub d’événements, la plupart des outils nécessitent la chaîne de connexion du hub d’événements, ainsi que certaines autorisations de votre abonnement Azure. Voici une liste non exhaustive des outils tiers avec intégration Azure Monitor :
    
    * **ArcSight** : Pour plus d’informations sur l’intégration des journaux Azure AD à ArcSight, consultez [Intégrer les journaux Azure AD à ArcSight à l’aide d’Azure Monitor](howto-integrate-activity-logs-with-arcsight.md).
    
    * **Splunk** : pour plus d’informations sur l’intégration des journaux d’activité Azure AD à Splunk, consultez [Comment intégrer les journaux d’activité Azure AD à Splunk à l’aide d’Azure Monitor](./howto-integrate-activity-logs-with-splunk.md).
    
    * **IBM QRadar** : vous pouvez télécharger le module DSM et le protocole Azure Event Hub depuis l’adresse du [service d’assistance d’IBM](https://www.ibm.com/support). Pour plus d’informations sur l’intégration avec Azure, accédez au site [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    * **Sumo Logic** : pour configurer l’outil Sumo Logic afin qu’il consomme les données provenant d’un Event Hub, consultez l’article [Install the Azure Active Directory App and View the Dashboards](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) (Installer l’application Azure Active Directory et visualiser les tableaux de bord). 

* **Configurer des outils personnalisés** Si votre SIEM actuel n’est pas encore pris en charge dans les diagnostics d’Azure Monitor, vous pouvez configurer des outils personnalisés à l’aide des API Event Hubs. Pour plus d’informations, consultez la section [Prise en main de la réception des messages à partir d’un hub d’événements](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).


## <a name="next-steps"></a>Étapes suivantes

* [Intégrer des journaux d’activité Azure Active Directory à ArcSight à l’aide d’Azure Monitor](howto-integrate-activity-logs-with-arcsight.md)
* [Intégrer les journaux d’activité Azure AD à Splunk à l’aide d’Azure Monitor](./howto-integrate-activity-logs-with-splunk.md)
* [Intégrer des journaux d’activité Azure AD avec SumoLogic à l’aide d’Azure Monitor](howto-integrate-activity-logs-with-sumologic.md)
* [Interpréter le schéma des journaux d’audit dans Azure Monitor](./overview-reports.md)
* [Interpréter le schéma des journaux d’activité de connexion dans Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)