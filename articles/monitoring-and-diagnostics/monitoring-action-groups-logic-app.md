---
title: Guide pratique pour déclencher des actions complexes avec des alertes et des groupes d’actions Azure Monitor
description: Découvrez comment créer une action d’application logique pour traiter des alertes Azure Monitor.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: eafb2bcf0175190748c9dd020051cbebfcaee1fd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263882"
---
# <a name="create-a-logic-app-action"></a>Créer une action d’application logique
## <a name="overview"></a>Vue d'ensemble ##
Quand une alerte Azure Monitor se déclenche, elle appelle un [groupe d’actions](monitoring-action-groups.md). Les groupes d’actions vous permettent de déclencher une ou plusieurs actions pour avertir les utilisateurs de l’alerte et même y remédier.

Cet article explique comment configurer une application logique pour qu’elle crée une conversation dans Microsoft Teams quand une alerte se déclenche.

Le processus général est le suivant :

-   Créer l’application logique pour le type d’alerte en question

-   Importer le schéma pour le type d’alerte en question dans l’application logique

-   Définir le comportement de l’application logique

-   Copier le point de terminaison HTTP de l’application logique dans un groupe d’actions Azure

Le processus est similaire si vous souhaitez que l’application logique effectue une action différente.

## <a name="create-an-activity-log-alert--administrative"></a>Créer une alerte de journal d’activité – Administration

1.  Cliquez sur le bouton **Créer une ressource** dans le coin supérieur gauche du portail Azure.

2.  Recherchez et sélectionnez **Application logique**. Cliquez sur le bouton **Créer** .

3.  Donnez un nom à votre application logique, choisissez un groupe de ressources, et ainsi de suite.

    ![Boîte de dialogue Créer une application logique](media/monitoring-action-groups/create-logic-app-dialog.png "Boîte de dialogue Créer une application logique")

4.  Cliquez sur le bouton Créer pour créer l’application logique. Une fenêtre contextuelle s’affiche une fois l’application logique créée. Cliquez sur le bouton Lancer la ressource pour ouvrir le Concepteur d’applications logiques.

5.  Sélectionnez le déclencheur **Lors de la réception d’une demande HTTP**.

    ![Déclencheurs d’application logique](media/monitoring-action-groups/logic-app-triggers.png "Déclencheurs d’application logique")

6.  Sélectionnez **Modifier** pour changer le déclencheur de requête HTTP.

    ![Forme de déclencheur de requête HTTP](media/monitoring-action-groups/http-request-trigger-shape.png "Forme de déclencheur de requête HTTP")

7.  Sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**.

    ![Bouton Utiliser l’exemple de charge utile](media/monitoring-action-groups/use-sample-payload-button.png "Bouton Utiliser l’exemple de charge utile")

8.  Copiez et collez l’exemple de schéma suivant dans la boîte de dialogue.

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. Le Concepteur d’application logique affiche une note vous rappelant que la requête envoyée à l’application logique doit définir l’en-tête Content-Type sur application/json. Fermez la boîte de dialogue. L’alerte Azure Monitor effectuera cette tâche correctement.

    ![En-tête Content-Type](media/monitoring-action-groups/content-type-header.png "En-tête Content-Type")

10. Sélectionnez **+ Nouvelle étape**, puis **Ajouter une action**.

    ![Ajouter une action](media/monitoring-action-groups/add-action.png "Ajouter une action")

11. Recherchez et sélectionnez le connecteur Microsoft Teams. Choisissez l’action **Microsoft Teams – Publier un message**.

    ![Actions Microsoft Teams](media/monitoring-action-groups/microsoft-teams-actions.png "Actions Microsoft Teams")

12. Configurez l’action Microsoft Teams. Le Concepteur d’applications logique vous invite à vous authentifier et vous connecter à votre compte Office 365. Choisissez l’**ID de l’équipe** et l’**ID du canal** pour envoyer le message.

13. Configurez le **Message** à l’aide d’une combinaison de texte statique et de références aux \<champs\> dans le contenu dynamique. Coupez et collez le texte suivant dans le champ Message.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Recherchez et remplacez les \<champs\> avec les balises de contenu dynamique du même nom.

    **[REMARQUE]**  Il existe deux champs dynamiques nommés **status**. Ajoutez les deux champs status au message. Utilisez celui du jeu de propriétés activityLog et supprimez l’autre. Si vous pointez votre souris sur le champ **status**, la référence complète au champ s’affiche comme illustré dans la capture d’écran.

    ![Action Teams - Publier un message](media/monitoring-action-groups/teams-action-post-message.png "Action Teams - Publier un message")

14. Enregistrez votre application logique en cliquant sur le bouton Enregistrer en haut du Concepteur.

15. Cliquez sur la forme de requête HTTP pour la développer. Copiez l’URL HTTP POST.

    ![URL HTTP POST](media/monitoring-action-groups/http-post-url.png "URL HTTP POST")

16. Ouvrez votre groupe d’actions existant et ajoutez une action pour référencer l’application logique. Si vous n’avez pas de groupe d’actions, consultez <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> pour en créer un. N’oubliez pas d’enregistrer vos modifications.

    ![Mise à jour du groupe d’actions](media/monitoring-action-groups/update-action-group.png "Mise à jour du groupe d’actions")

La prochaine fois qu’une alerte appelle votre groupe d’actions, votre application logique est appelée.

## <a name="create-a-service-health-alert"></a>Créer une alerte d’intégrité du service

Les entrées d’intégrité de service faisant partie du journal d’activité, le processus est similaire, à quelques changements près.

1.  Les étapes 1 à 7 sont identiques.
2.  Utilisez l’exemple de schéma suivant pour le déclencheur HTTP à l’étape 8.

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

3.  Les étapes 9 et 10 sont identiques.
4.  À partir de l’étape 11, appliquez la procédure ci-dessous.
5.  Cliquez sur le bouton **+ Nouvelle étape** et choisissez **Ajouter une condition**. Définissez les conditions suivantes pour vous assurer que l’application logique s’exécute uniquement quand les données d’entrée correspondent à ces valeurs.
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - version == 0.1.1

    ![« Condition de charge utile d’intégrité du service »](media/monitoring-action-groups/service-health-payload-condition.png "Condition de charge utile d’intégrité du service")

6. Dans la condition **Si true**, ajoutez l’action Microsoft Teams à l’aide des étapes 11 à 13 de l’exemple précédent.

7. Définissez le message à l’aide d’une combinaison de HTML et de [contenu dynamique]. Copiez et collez le contenu ci-dessous dans le champ de message. Remplacez les champs [incidentType], [trackingID], [title] et [communication] par des balises de contenu dynamique du même nom.

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    ![« Action de publication pour condition vraie d’intégrité du service »](media/monitoring-action-groups/service-health-true-condition-post-action.png "Action de publication pour condition vraie d’intégrité du service")

8. Pour la condition **Si false**, fournissez un message utile.

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    ![« Action de publication pour condition fausse d’intégrité du service »](media/monitoring-action-groups/service-health-false-condition-post-action.png "Action de publication pour condition fausse d’intégrité du service")

9.  Suivez les étapes 15 à 16 de l’exemple précédent pour enregistrer votre application logique et mettre à jour votre groupe d’actions.

## <a name="metric-alert"></a>Alerte de métrique

1.  Les étapes 1 à 7 sont les mêmes que pour le premier exemple.
2.  Utilisez l’exemple de schéma suivant pour le déclencheur HTTP à l’étape 8.

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

3.  Les étapes 9 et 10 sont identiques.
4.  À partir de l’étape 11, appliquez la procédure ci-dessous.
5.  Cliquez sur le bouton **+ Nouvelle étape** et choisissez **Ajouter une condition**. Définissez les conditions suivantes pour vous assurer que l’application logique s’exécute uniquement quand les données d’entrée correspondent à ces valeurs.

    - schemaId == AzureMonitorMetricAlert
    - version == 2.0

    ![« Condition de charge utile d’alerte de métrique »](media/monitoring-action-groups/metric-alert-payload-condition.png "Condition de charge utile d’alerte de métrique")

6.  Dans la condition **Si true**, nous allons ajouter une forme **For each** et l’action Microsoft Teams, et définir le message à l’aide d’une combinaison de HTML et de [contenu dynamique].

    ![« Action de publication pour condition vraie d’alerte de métrique »](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "Action de publication pour condition vraie d’alerte de métrique")

7.  Dans la forme **Si false**, définissez une action Microsoft Teams qui signale que l’alerte de métrique ne correspond pas aux attentes de l’application logique et incluez la charge utile JSON. Notez comment nous référençons le contenu dynamique triggerBody dans l’expression json().

    ![« Action de publication pour condition fausse d’alerte de métrique »](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "Action de publication pour condition fausse d’alerte de métrique")

8.  Suivez les étapes 15 à 16 du premier exemple pour enregistrer votre application logique et mettre à jour votre groupe d’actions.

## <a name="next-steps"></a>Étapes suivantes ##
* Obtenir une [vue d’ensemble des alertes du journal d’activité](monitoring-overview-alerts.md) et découvrir comment recevoir des alertes.  
* Découvrir comment [configurer des alertes lorsqu’une notification d’intégrité de service est publiée](monitoring-activity-log-alerts-on-service-notifications.md).
* En savoir plus sur les [groupes d’actions](monitoring-action-groups.md)