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
ms.openlocfilehash: 14e562234152d2f1f2f2d2b57b34cd5724d3dd14
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753091"
---
# <a name="create-a-logic-app-action"></a>Créer une action d’application logique

Cet article explique comment configurer une application logique pour qu’elle crée une conversation dans Microsoft Teams quand une alerte se déclenche.

## <a name="overview"></a>Vue d'ensemble
Quand une alerte Azure Monitor se déclenche, elle appelle un [groupe d’actions](monitoring-action-groups.md). Les groupes d’actions permettent de déclencher une ou plusieurs actions pour avertir les autres en cas d’alerte et y remédier.

Le processus général est le suivant :

-   Créez l’application logique pour le type d’alerte en question.

-   Importez le schéma associé au type d’alerte en question dans l’application logique.

-   Définissez le comportement de l’application logique.

-   Copiez le point de terminaison HTTP de l’application logique dans un groupe d’actions Azure.

Le processus est similaire si vous souhaitez que l’application logique effectue une autre action.

## <a name="create-an-activity-log-alert-administrative"></a>Créer une alerte de journal d’activité : Administration

1.  Sur le Portail Azure, sélectionnez **Créer une ressource** en haut à gauche.

2.  Recherchez et sélectionnez **Application logique**, puis **Créer**.

3.  Donnez un **Nom** à votre application logique, choisissez un **Groupe de ressources** et ainsi de suite.

    ![Créer une application logique](media/monitoring-action-groups/create-logic-app-dialog.png "Créer une application logique")

4.  Sélectionnez **Créer** pour créer l’application logique. Un message contextuel indique que l’application logique est créée. Sélectionnez **Lancer la ressource** pour ouvrir le **Concepteur d’applications logiques**.

5.  Sélectionnez le déclencheur **Lors de la réception d’une requête HTTP**.

    ![Déclencheurs d’application logique](media/monitoring-action-groups/logic-app-triggers.png "Déclencheurs d’application logique")

6.  Sélectionnez **Modifier** pour changer de déclencheur de requête HTTP.

    ![Déclencheurs de requête HTTP](media/monitoring-action-groups/http-request-trigger-shape.png "Déclencheurs de requête HTTP")

7.  Sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**.

    ![Utiliser un exemple de charge utile](media/monitoring-action-groups/use-sample-payload-button.png "Utiliser un exemple de charge utile")

8.  Copiez et collez l’exemple de schéma suivant dans la boîte de dialogue :

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

9. Le **Concepteur d’applications logiques** affiche une fenêtre indépendante rappelant que la requête envoyée à l’application logique doit définir l’en-tête **Content-Type** sur **application/json**. Fermez la fenêtre indépendante. L’alerte Azure Monitor définit l’en-tête.

    ![Définir l’en-tête Content-Type](media/monitoring-action-groups/content-type-header.png "Définir l’en-tête Content-Type")

10. Sélectionnez **+** **Nouvelle étape**, puis **Ajouter une action**.

    ![Ajouter une action](media/monitoring-action-groups/add-action.png "Ajouter une action")

11. Recherchez et sélectionnez le connecteur Microsoft Teams. Choisissez l’action **Microsoft Teams – Publier un message**.

    ![Actions Microsoft Teams](media/monitoring-action-groups/microsoft-teams-actions.png "Actions Microsoft Teams")

12. Configurez l’action Microsoft Teams. Le **Concepteur d’applications logiques** vous invite à vous authentifier pour vous connecter à votre compte Office 365. Choisissez l’**ID de l’équipe** et l’**ID du canal** pour envoyer le message.

13. Configurez le message en utilisant une combinaison de texte statique et de références aux \<champs\> du contenu dynamique. Coupez et collez le texte suivant dans le champ **Message** :

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Recherchez et remplacez les \<champs\> par des balises de contenu dynamique du même nom.

    > [!NOTE]
    > Il existe deux champs dynamiques nommés **status**. Ajoutez les deux au message. Utilisez celui du jeu de propriétés **activityLog** et supprimez l’autre. Placez le curseur sur le champ **status** pour voir la référence complète au champ, comme sur la capture d’écran suivante :

    ![Action Microsoft Teams : Publier un message](media/monitoring-action-groups/teams-action-post-message.png "Action Microsoft Teams : Publier un message")

14. En haut du **Concepteur d’applications logiques**, sélectionnez **Enregistrer** pour enregistrer votre application logique.

15. Ouvrez votre groupe d’actions existant et ajoutez une action pour référencer l’application logique. Si vous n’avez pas de groupe d’actions, consultez [Créer et gérer des groupes d’actions sur le Portail Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups) pour en créer un. N’oubliez pas d’enregistrer vos modifications.

    ![Mettre à jour le groupe d’actions](media/monitoring-action-groups/update-action-group.png "Mettre à jour le groupe d’actions")

La prochaine fois qu’une alerte appellera votre groupe d’actions, votre application logique sera appelée.

## <a name="create-a-service-health-alert"></a>Créer une alerte d’intégrité du service

Les entrées Azure Service Health font partie du journal d’activité. Le processus de création de l’alerte est semblable à celui de [création d’une alerte de journal d'activité](#create-an-activity-log-alert-administrative), mais avec quelques modifications :

- Les étapes 1 à 7 sont identiques.
- À l’étape 8, utilisez l’exemple de schéma suivant pour le déclencheur de requête HTTP :

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

-  Les étapes 9 et 10 sont identiques.
-  De l’étape 11 à l’étape 14, suivez le processus ci-dessous :

   1. Sélectionnez **+** **Nouvelle étape**, puis **Ajouter une condition**. Définissez les conditions suivantes pour que l’application logique s’exécute seulement lorsque les données d’entrée correspondent à ces valeurs :
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == 0.1.1`

      ![« Condition de charge utile d’intégrité du service »](media/monitoring-action-groups/service-health-payload-condition.png "Condition de charge utile d’intégrité du service")

   1. Dans la condition **If true**, suivez les instructions des étapes 11 à 13 de [Créer une alerte de journal d’activité](#create-an-activity-log-alert-administrative) pour ajouter l’action Microsoft Teams.

   1. Définissez le message en utilisant une combinaison de HTML et de contenu dynamique. Coupez et collez le contenu suivant dans le champ **Message**. Remplacez les champs `[incidentType]`, `[trackingID]`, `[title]` et `[communication]` par des balises de contenu dynamique du même nom :

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       ![« Action de publication pour condition vraie d’intégrité du service »](media/monitoring-action-groups/service-health-true-condition-post-action.png "Action de publication pour condition vraie d’intégrité du service")

   1. Pour la condition **If false**, indiquez un message utile :

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       ![« Action de publication pour condition fausse d’intégrité du service »](media/monitoring-action-groups/service-health-false-condition-post-action.png "Action de publication pour condition fausse d’intégrité du service")

- L’étape 15 est identique. Suivez les instructions pour enregistrer votre application logique et mettre à jour votre groupe d’actions.

## <a name="create-a-metric-alert"></a>Créer une alerte de métrique

Le processus de création d’une alerte de métrique est semblable à celui de [création d’une alerte de journal d'activité](#create-an-activity-log-alert-administrative), mais avec quelques modifications :

- Les étapes 1 à 7 sont identiques.
- À l’étape 8, utilisez l’exemple de schéma suivant pour le déclencheur de requête HTTP :

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

- Les étapes 9 et 10 sont identiques.
- De l’étape 11 à l’étape 14, suivez le processus ci-dessous :

   1. Sélectionnez **+** **Nouvelle étape**, puis **Ajouter une condition**. Définissez les conditions suivantes pour que l’application logique s’exécute seulement lorsque les données d’entrée correspondent à ces valeurs :
       - `schemaId == AzureMonitorMetricAlert`
       - `version == 2.0`

       ![« Condition de charge utile d’alerte de métrique »](media/monitoring-action-groups/metric-alert-payload-condition.png "Condition de charge utile d’alerte de métrique")

   1. Dans la condition **If true**, ajoutez une boucle **For each** et l’action Microsoft Teams. Définissez le message en utilisant une combinaison de HTML et de contenu dynamique.

       ![« Action de publication pour condition vraie d’alerte de métrique »](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "Action de publication pour condition vraie d’alerte de métrique")

   1. Dans la condition **If false**, définissez une action Microsoft Teams pour signaler que l’alerte de métrique ne correspond pas aux attentes de l’application logique. Incluez la charge utile JSON. Notez comment faire référence au contenu dynamique `triggerBody` de l’expression `json()`.

       ![« Action de publication pour condition fausse d’alerte de métrique »](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "Action de publication pour condition fausse d’alerte de métrique")

- L’étape 15 est identique. Suivez les instructions pour enregistrer votre application logique et mettre à jour votre groupe d’actions.

## <a name="next-steps"></a>Étapes suivantes
* Dans une [vue d’ensemble des alertes du journal d’activité Azure](monitoring-overview-alerts.md), découvrez comment recevoir des alertes.  
* Découvrez comment [configurer des alertes lorsqu’une notification Azure Service Health est publiée](monitoring-activity-log-alerts-on-service-notifications.md).
* En savoir plus sur les [groupes d’actions](monitoring-action-groups.md).