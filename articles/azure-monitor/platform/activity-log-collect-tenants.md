---
title: Collecter les journaux d’activité Azure dans un espace de travail Log Analytics auprès de locataire Azure | Microsoft Docs
description: Utilisez Event Hubs et Logic Apps pour collecter des données de journal d’activité Azure et les envoyer à un espace de travail Log Analytics dans Azure Monitor d’un autre locataire.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2019
ms.openlocfilehash: e3b368f8a59d201f70bfad05125ed59b4b8551c5
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529998"
---
# <a name="collect-azure-activity-logs-into-azure-monitor-across-azure-active-directory-tenants-legacy"></a>Collecter les journaux d’activité Azure dans Azure Monitor auprès de locataires Azure Active Directory (méthode héritée)

> [!NOTE]
> Cet article décrit la méthode héritée pour la configuration du journal des activités Windows Azure entre les locataires Azure à collecter dans un espace de travail Log Analytics.  Vous pouvez maintenant collecter le journal d’activité dans un espace de travail Log Analytics à l’aide d’un paramètre de diagnostic de la même façon que vous collectez les journaux de ressources. Consultez [Collecter et analyser les journaux d’activité Azure dans l’espace de travail Log Analytics dans Azure Monitor](activity-log-collect.md).


Cet article décrit une méthode permettant de collecter les journaux d’activité Azure dans un espace de travail Log Analytics dans Azure Monitor à l’aide du connecteur Azure Log Analytics Data Collector pour Logic Apps. Utilisez la procédure de cet article lorsque vous devez envoyer des journaux d’activité à un espace de travail situé dans un autre locataire Azure Active Directory. Par exemple, si vous êtes un fournisseur de service managé, vous pouvez collecter les journaux d’activité de l’abonnement d’un client et les stocker dans un espace de travail Log Analytics de votre propre abonnement.

Si l’espace de travail Log Analytics est situé dans le même abonnement Azure, ou dans un autre abonnement mais au sein du même répertoire Azure Active Directory, utilisez la procédure décrite dans [Collecter et analyser les journaux d’activité Azure dans l’espace de travail Log Analytics dans Azure Monitor](activity-log-collect.md) pour collecter les journaux d’activité Azure.

## <a name="overview"></a>Vue d’ensemble

La stratégie utilisée dans ce scénario consiste à ce que le journal d’activité Azure envoie les événements à un [Event Hub](../../event-hubs/event-hubs-about.md), d’où une [application logique](../../logic-apps/logic-apps-overview.md) les envoie à son tour vers votre espace de travail Log Analytics. 

![image du flux de données du journal d’activité vers l’espace de travail Log Analytics](media/collect-activity-logs-subscriptions/data-flow-overview.png)

Avantages de cette approche :
- Latence faible, car le journal d’activité Azure est diffusé en continu dans l’Event Hub.  L’application logique est ensuite déclenchée et publie les données dans l’espace de travail. 
- Un code minime est requis et il n’y a aucune infrastructure de serveur à déployer.

Cet article vous explique les procédures détaillées pour :
1. Créez un concentrateur d’événements. 
2. Exporter les journaux d’activité vers un Event Hub à l’aide d’un profil d’exportation de journal d’activité Azure.
3. Créer une application logique pour lire à partir du Event Hub et envoyer des événements à l’espace de travail Log Analytics.

## <a name="requirements"></a>Spécifications
Voici la configuration requise pour les ressources Azure utilisées dans ce scénario.

- L’espace de noms Event Hub ne doit pas nécessairement se trouver dans l’abonnement qui émet les journaux d’activité. L’utilisateur qui configure le paramètre doit disposer d’autorisations d’accès appropriées aux deux abonnements. Si vous avez plusieurs abonnements dans le même répertoire Azure Active Directory, vous pouvez envoyer les journaux d’activité de tous les abonnements à un seul Event Hub.
- L’application logique peut se trouver dans un abonnement différent de celui de l’Event Hub et ne doit pas nécessairement se trouver dans le même répertoire Azure Active Directory. L’application logique lit à partir de l’Event Hub à l’aide de la clé d’accès partagé de celui-ci.
- L’espace de travail Log Analytics peut se trouver dans un abonnement et un répertoire Azure Active Directory différents de ceux de l’application logique. Toutefois, par souci de simplicité, il est recommandé qu’ils se trouvent dans le même abonnement. L’application logique envoie les données à l’espace de travail à l’aide de la clé et de l’ID de l’espace de travail Log Analytics.



## <a name="step-1---create-an-event-hub"></a>Étape 1 : Créer un Event Hub

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Dns le portail Azure, sélectionnez **Créer une ressource** > **Internet des objets** > **Event Hubs**.

   ![Nouvel Event Hub dans la Place de marché](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. Sous **Créer un espace de noms**, entrez un nouvel espace de noms ou sélectionnez-en un existant. Le système vérifie immédiatement si le nom est disponible.

   ![image de la boîte de dialogue créer un event hub](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. Choisissez le niveau tarifaire (De base ou Standard), un abonnement Azure, un groupe de ressources et un emplacement pour la nouvelle ressource.  Cliquez sur **Créer** pour créer l’espace de noms. Vous devrez peut-être attendre quelques minutes pour que le système approvisionne entièrement les ressources.
6. Sélectionnez l’espace de noms que vous venez de créer dans la liste.
7. Cliquez sur **Stratégies d’accès partagé**, puis sur **RootManageSharedAccessKey**.

   ![image des stratégies d’accès partagé de l’event hub](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. Cliquez sur le bouton Copier pour copier la chaîne de connexion **RootManageSharedAccessKey** dans le Presse-papiers. 

   ![image des clés d’accès partagé de l’event hub](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. Dans un emplacement temporaire tel que le Bloc-notes, conservez une copie du nom de l’Event Hub ainsi que la chaîne de connexion principale ou secondaire de celui-ci. Ces valeurs sont requises par l’application logique.  Pour la chaîne de connexion de l’Event Hub, vous pouvez utiliser la chaîne de connexion **RootManageSharedAccessKey** ou en créer une.  La chaîne de connexion que vous utilisez doit commencer par `Endpoint=sb://` et être associée à une stratégie dotée de la stratégie d’accès **Gestion**.


## <a name="step-2---export-activity-logs-to-event-hub"></a>Étape 2 : Exporter les journaux d’activité vers Event Hub

Pour permettre le streaming du journal d’activité, vous pouvez choisir un espace de noms Event Hub et une stratégie d’accès partagé pour cet espace de noms. Un Event Hub est créé dans cet espace de noms lorsque le premier nouvel événement de journal d’activité se produit. 

Vous pouvez utiliser un espace de noms Event Hub situé dans un abonnement différent de celui qui émet les journaux d’activité. Les abonnements doivent cependant figurer dans le même répertoire Azure Active Directory. L’utilisateur qui configure le paramètre doit disposer d’un accès RBAC approprié aux deux abonnements. 

1. Dans le portail Azure, sélectionnez **Moniteur** > **Journal d’activité**.
3. Cliquez sur le bouton **Exporter** en haut de la page.

   ![image du moniteur azure dans la navigation](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. Sélectionnez l’**abonnement** à partir duquel effectuer l’exportation, puis cliquez sur **Sélectionner tout** dans la liste déroulante **Régions** afin de sélectionner les événements concernant les ressources de toutes les régions. Cochez la case **Exporter vers un hub d’événements**.
7. Cliquez sur **Espace de noms Service bus**, puis sélectionnez l’**abonnement** avec l’Event Hub, l’**espace de noms Event Hub** et un **nom de stratégie Event Hub**.

    ![image de la page exporter vers un event hub](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. Cliquez sur **OK**, puis sur **Enregistrer** pour enregistrer ces paramètres. Les paramètres sont immédiatement appliqués à votre abonnement.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>Étape 3 : Créer une application logique

Une fois que les journaux d’activité écrivent à l’Event Hub, vous créez une application logique pour collecter les journaux d’activité à partir de l’Event Hub et les écrire dans l’espace de travail Log Analytics.

L’application logique contient les éléments suivants :
- Un déclencheur [Connecteur Event Hub](https://docs.microsoft.com/connectors/eventhubs/) pour lire à partir de l’Event Hub.
- Une [action Analyser JSON](../../logic-apps/logic-apps-content-type.md) pour extraire les événements JSON.
- Une [action Composer](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) pour convertir l’événement JSON en objet.
- Un [connecteur d’envoi de données Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) pour publier les données dans l’espace de travail Log Analytics.

   ![image de l’ajout d’un déclencheur event hub dans logic apps](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Configuration requise de l’application logique
Avant de créer votre application logique, vérifiez que vous disposez des informations suivantes issues des étapes précédentes :
- Nom de l’Event Hub
- Chaîne de connexion de l’Event Hub (principale ou secondaire) pour l’espace de noms Event Hub.
- ID d’espace de travail Log Analytics
- Clé partagée Log Analytics

Pour obtenir le nom et la chaîne de connexion de l’Event Hub, procédez comme décrit dans [Vérifier les autorisations d’espace de noms Event Hubs et rechercher la chaîne de connexion](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string).


### <a name="create-a-new-blank-logic-app"></a>Créer une application logique vide

1. Dans le portail Azure, choisissez **Créer une ressource** > **Intégration Entreprise** > **Application logique**.

    ![Nouvelle application logique dans la Place de marché](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Entrez les paramètres dans le tableau ci-dessous.

    ![Créer une application logique](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |Paramètre | Description  |
   |:---|:---|
   | Name           | Nom unique de l’application logique. |
   | Subscription   | Sélectionnez l’abonnement Azure qui contiendra l’application logique. |
   | Groupe de ressources | Sélectionnez un groupe de ressources Azure existant ou créez-en un pour l’application logique. |
   | Location       | Sélectionnez la région du centre de données où déployer votre application logique. |
   | Log Analytics  | Choisissez d’enregistrer ou non l’état de chaque exécution de votre application logique dans un espace de travail Log Analytics.  |

    
3. Sélectionnez **Create** (Créer). Lorsque la notification **Le déploiement a été effectué** apparaît, cliquez sur **Accéder à la ressource** pour ouvrir votre application logique.

4. Sous **Modèles**, choisissez **Application logique vide**. 

Le concepteur d’applications logiques affiche à présent les connecteurs disponibles et leurs déclencheurs, que vous utilisez pour démarrer le flux de travail de votre application logique.

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Ajouter un déclencheur Event Hub

1. Dans la zone de recherche du concepteur d’applications logiques, entrez *event hubs* comme filtre. Sélectionnez le déclencheur **Event Hubs - Lorsque les événements sont disponibles dans un Event Hub**.

   ![image de l’ajout d’un déclencheur event hub dans logic apps](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Lorsque vous êtes invité à entrer les informations d’identification, connectez-vous à votre espace de noms Event Hubs. Nommez votre connexion et entrez la chaîne de connexion que vous avez copiée.  Sélectionnez **Create** (Créer).

   ![image de l’ajout d’une connexion event hub dans logic apps](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. Après avoir créé la connexion, modifiez les paramètres du déclencheur. Commencez par sélectionner **insights-operational-logs** dans la liste déroulante **Nom de l’Event Hub**.

   ![Boîte de dialogue Lorsque les événements sont disponibles](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Développez **Afficher les options avancées** et définissez le **type de contenu** sur *application/json.*

   ![Boîte de dialogue de configuration d’un Event Hub](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Ajouter une action Analyser JSON

La sortie de l’Event Hub contient une charge utile JSON avec un tableau d’enregistrements. L’action [Analyser JSON](../../logic-apps/logic-apps-content-type.md) permet d’extraire uniquement le tableau d’enregistrements pour l’envoyer à l’espace de travail Log Analytics.

1. Cliquez sur **Nouvelle étape** > **Ajouter une action**
2. Dans la zone de recherche, entrez *analyser json* comme filtre. Sélectionnez l’action **Opérations sur les données - Analyser JSON**.

   ![Ajout d’une action analyser json dans logic apps](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Cliquez dans le champ **Contenu**, puis sélectionnez *Corps*.

4. Copiez-collez le schéma suivant dans le champ **Schéma**.  Ce schéma correspond à la sortie de l’action Event Hub.  

   ![Boîte de dialogue Analyser json](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> Vous pouvez obtenir un exemple de charge utile en cliquant sur **Exécuter** et en examinant la **sortie brute** de l’Event Hub.  Vous pouvez ensuite utiliser cette sortie avec **Utiliser un exemple de charge utile pour générer un schéma** dans l’activité **Analyser JSON** pour générer le schéma.

### <a name="add-compose-action"></a>Ajouter une action Composer
L’action [Composer](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) prend la sortie JSON et crée un objet qui peut être utilisé par l’action Log Analytics.

1. Cliquez sur **Nouvelle étape** > **Ajouter une action**
2. Entrez *composer* comme filtre et sélectionnez l’action **Opérations sur les données - Composer**.

    ![Ajouter une action Composer](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Cliquez sur le champ **Entrées** et sélectionnez **Corps** sous l’activité **Analyser JSON**.


### <a name="add-log-analytics-send-data-action"></a>Ajouter une action Envoyer des données Log Analytics
L’action [Collecteur de données Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) prend l’objet de l’action Composer et l’envoie à un espace de travail Log Analytics.

1. Cliquez sur **Nouvelle étape** > **Ajouter une action**
2. Entrez *Log Analytics* comme filtre et sélectionnez l’action **Collecteur de données Azure Log Analytics - Envoyer des données**.

   ![Ajout d’une action envoyer des données Log Analytics dans logic apps](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Nommez votre connexion et collez l’**ID de l’espace de travail** et la **Clé de l’espace de travail** pour votre espace de travail Log Analytics.  Cliquez sur **Créer**.

   ![Ajout d’une connexion Log Analytics dans logic apps](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Après avoir créé la connexion, modifiez les paramètres dans le tableau suivant. 

    ![Configuration de l’action Envoyer des données](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Paramètre        | Valeur           | Description  |
   |---------------|---------------------------|--------------|
   |JSON Request body (Corps de la requête JSON)  | **Sortie** de l’action **Composer** | Récupère les enregistrements à partir du corps de l’action Composer. |
   | Custom Log Name (Nom de journal personnalisé) | AzureActivity | Nom de la table de journal personnalisée à créer dans l’espace de travail Log Analytics pour contenir les données importées. |
   | Time-generated-field | time | Ne sélectionnez pas le champ JSON pour **time**. Entrez simplement le mot « time ». Si vous sélectionnez le champ JSON, le concepteur place l’action **Envoyer des données** en boucle *Pour chaque*, ce que vous ne souhaitez pas. |




10. Cliquez sur **Enregistrer** pour enregistrer les modifications apportées à votre application logique.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>Étape 4 - Tester et résoudre les problèmes de l’application logique
Une fois le flux de travail terminé, vous pouvez effectuer un test dans le concepteur pour vérifier qu’il fonctionne correctement.

Dans le concepteur d’applications logiques, cliquez sur **Exécuter** pour tester l’application logique. Chaque étape de l’application logique présente une icône d’état représentant une coche blanche dans un cercle vert, qui indique la réussite de l’opération.

   ![Tester l’application logique](media/collect-activity-logs-subscriptions/test-logic-app.png)

Pour afficher des informations détaillées sur chaque étape, cliquez sur le nom de l’étape pour le développer. Cliquez sur **Afficher les entrées brutes** et **Afficher les sorties brutes** pour afficher plus d’informations sur les données reçues et envoyées à chaque étape.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Étape 5 : Afficher le journal d’activité Azure dans Log Analytics
La dernière étape consiste à consulter l’espace de travail Log Analytics pour vérifier que les données sont collectées comme prévu.

1. Dans le portail Azure, cliquez sur **Tous les services** en haut à gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Dans votre liste d’espaces de travail Log Analytics, sélectionnez votre espace de travail.
3.  Cliquez sur la vignette **Recherche dans les journaux**. Ensuite, dans le volet Recherche dans les journaux, dans le champ de requête, tapez `AzureActivity_CL`, puis appuyez sur Entrée ou cliquez sur le bouton de recherche à droite du champ de requête. Si vous n’avez pas nommé votre journal personnalisé *AzureActivity*, entrez le nom choisi et ajoutez `_CL`.

>[!NOTE]
> Lors du premier envoi d’un nouveau journal personnalisé à l’espace de travail Log Analytics, il peut être nécessaire d’attendre jusqu’à une heure pour qu’il puisse faire l’objet d’une recherche.

>[!NOTE]
> Les journaux d’activité sont écrits dans une table personnalisée et ne s’affichent pas dans la [solution Activity Log](./activity-log-collect.md).


![Tester l’application logique](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé une application logique pour lire des journaux d’activité Azure à partir d’un Event Hub et les envoyer à l’espace de travail Log Analytics pour analyse. Pour en savoir plus sur la visualisation de données dans un espace de travail, notamment sur la création de tableaux de bords, consultez le didacticiel consacré à ce sujet.

> [!div class="nextstepaction"]
> [Didacticiel Visualiser des données de recherche dans les journaux](./../../azure-monitor/learn/tutorial-logs-dashboards.md)
