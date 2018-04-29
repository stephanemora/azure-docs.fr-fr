---
title: Configurer la surveillance des événements avec Azure Event Hubs pour Azure Logic Apps | Microsoft Docs
description: Surveiller des flux de données afin de recevoir et d’envoyer des événements avec vos applications logique en utilisant Azure Event Hubs
services: logic-apps
keywords: flux de données, observateur d’événements, event hubs
author: ecfan
manager: anneta
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: ''
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 8de56cd64f38791fb27d9bcce1e16641fb162c2f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Surveillez, recevez et envoyez des événements avec le connecteur Event Hubs

Pour configurer un observateur d’événements afin que votre application logique puisse détecter, recevoir et envoyer des événements, connectez-vous à un hub [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs) à partir de votre application logique. Apprenez-en davantage sur [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) et la [tarification des connecteurs Logic Apps](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Prérequis


Pour pouvoir utiliser le connecteur Event Hubs, vous devez disposer des éléments suivants :

* Un [espace de noms Azure Event Hubs et un hub d’événements](../event-hubs/event-hubs-create.md)
* Une [application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-event-hubs"></a>Se connecter à Azure Event Hubs

Pour que votre application logique puisse accéder à tout service, vous devez créer une [*connexion*](./connectors-overview.md) entre votre application logique et le service, si ce n’est déjà fait. Cette connexion autorise votre application logique à accéder aux données. Pour que votre application logique accède à votre hub d’événements, vérifiez vos autorisations et obtenez la chaîne de connexion pour votre espace de noms Event Hubs.

1.  Connectez-vous au [portail Azure](https://portal.azure.com "portail Azure"). 

2.  Accédez à votre *espace de noms* Event Hubs, et non à un hub d’événements spécifique. Dans la page de l’espace de noms, sous **Paramètres**, choisissez **Stratégies d’accès partagé**. Sous **Revendications**, vérifiez que vous disposez des autorisations **Gérer** pour cet espace de noms.

    ![Gérer les autorisations pour votre espace de noms Event Hubs](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Si vous souhaitez pouvoir entrer manuellement vos informations de connexion par la suite, obtenez la chaîne de connexion pour votre espace de noms Event Hubs. Choisissez **RootManageSharedAccessKey**. En regard de votre chaîne de connexion de clé primaire, cliquez sur le bouton de copie. Enregistrez la chaîne de connexion en vue d’une utilisation ultérieure.

    ![Copier la chaîne de connexion d’espace de noms Event Hubs](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Pour vérifier que votre chaîne de connexion est bien associée à votre espace de noms Event Hubs ou à un hub d’événements spécifique, vérifiez la chaîne de connexion pour le paramètre `EntityPath`. Si vous trouvez ce paramètre, la chaîne de connexion concerne une « entité » Event Hubs spécifique et elle ne peut pas être utilisée avec votre application logique.

## <a name="trigger-workflow-when-your-event-hub-gets-new-events"></a>Déclencher un flux de travail quand votre hub d’événements reçoit de nouveaux événements

Un [*déclencheur*](../logic-apps/logic-apps-overview.md#logic-app-concepts) désigne un événement qui démarre un workflow dans votre application logique. Pour démarrer un workflow lorsque de nouveaux événements sont envoyés à votre hub Event Hubs, procédez comme suit afin d’ajouter le déclencheur qui détecte cet événement.

1. Dans le [portail Azure](https://portal.azure.com "portail Azure"), accédez à votre application logique existante ou créez une application logique vide.

2. Dans le concepteur d’applications logiques, entrez « hubs d’événements » en tant que filtre dans la zone de recherche. Sélectionnez ce déclencheur : **When events are available in Event Hub (Lorsque les événements sont disponibles dans un hub Event Hubs)**

   ![Sélectionner le déclencheur lorsque votre hub Event Hubs reçoit de nouveaux événements](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

   1. Si vous ne disposez pas encore d’une connexion à votre espace de noms Event Hubs, vous êtes invité à créer cette connexion dès maintenant. Donnez un nom à votre connexion, puis sélectionnez l’espace de noms Event Hubs à utiliser.

      ![Créer une connexion de hub d’événements](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

      Ou bien, pour entrer manuellement la chaîne de connexion, choisissez **Entrer manuellement les informations de connexion**. 
      Découvrez [comment trouver votre chaîne de connexion](#permissions-connection-string).

   2. Sélectionnez à présent la stratégie Event Hubs à utiliser, puis choisissez **Créer**.

      ![Créer une connexion de hub d’événements, partie 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Sélectionnez le hub d’événements à surveiller, puis définissez l’intervalle et la fréquence de contrôle du hub d’événements.

    ![Spécifier un hub Event Hubs ou un groupe de consommateurs](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)
    
    > [!NOTE]
    > Tous les déclencheurs Event Hub sont des déclencheurs *d’interrogation longue*, ce qui signifie que lorsqu’un déclencheur est activé, celui-ci traite tous les événements puis attend 30 secondes le temps qu’un plus grand nombre d’événements s’affichent dans l’Event Hub.
    > Si aucun événement n’est reçu dans les 30 secondes, l’exécution du déclencheur est ignorée. Dans le cas contraire, le déclencheur poursuit la lecture des événements jusqu’à ce que l’Event Hub soit vide.
    > La prochaine interrogation de déclencheur est basée sur l’intervalle de récurrence spécifié dans les propriétés du déclencheur.


4. Pour sélectionner certaines des options avancées du déclencheur, choisissez **Afficher les options avancées**.

    ![Options avancées du déclencheur](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

    | Propriété | Détails |
    | --- | --- |
    | Type de contenu  |Dans la liste déroulante, sélectionnez le type de contenu des événements. Par défaut, application/octet-stream est sélectionné. |
    | Schéma du contenu |Entrez le schéma du contenu au format JSON pour les événements qui sont lus à partir de l’Event Hub. |
    | Nom du groupe de consommateurs |Entrez le [nom du groupe de consommateurs](../event-hubs/event-hubs-features.md#consumer-groups) de l’Event Hub pour lire les événements. Lorsque le nom du groupe de consommateurs n’est pas spécifié, le groupe de consommateurs par défaut est utilisé. |
    | Clé de partition minimum |Entrez l’ID de [partition](../event-hubs/event-hubs-features.md#partitions) minimum à lire. Par défaut, toutes les partitions sont lues. |
    | Clé de partition maximum |Entrez l’ID de [partition](../event-hubs/event-hubs-features.md#partitions) maximum à lire. Par défaut, toutes les partitions sont lues. |
    | Nombre d’événements maximum |Entrez une valeur pour le nombre maximum d’événements. Le déclencheur retourne entre 1 et le nombre d’événements spécifié par cette propriété. |
    |||

5. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Désormais, quand votre application logique vérifie le hub d’événements sélectionné et trouve un nouvel événement, le déclencheur exécute les actions de votre logique d’application pour l’événement trouvé.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Envoyer des événements à votre hub Event Hubs à partir de votre application logique

Une [*action*](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une tâche effectuée par le flux de travail de votre application logique. Après avoir ajouté un déclencheur à votre application logique, vous pouvez ajouter une action permettant d’effectuer des opérations avec les données générées par ce déclencheur. Pour envoyer un événement à votre hub Event Hubs à partir de votre application logique, procédez comme suit.

1. Dans le concepteur d’applications logiques, sous votre déclencheur, choisissez **Nouvelle étape** > **Ajouter une action**.

2. Dans la zone de recherche, entrez « hubs d’événements » en tant que filtre.
Sélectionnez l’action **Event Hubs - Envoyer un événement**

   ![Sélectionner « Event Hubs - Envoyer un événement »](./media/connectors-create-api-azure-event-hubs/select-event-hubs-send-event-action.png)

3. Sélectionnez le hub d’événements auquel envoyer l’événement. Entrez ensuite le contenu de l’événement et tous les autres détails.

   ![Sélectionnez le nom du hub d’événements et fournissez le contenu de l’événement](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

4. Enregistrez votre application logique.

Vous avez configuré une action qui envoie des événements à partir de votre application logique. 

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Pour en savoir plus sur les déclencheurs et les actions définies par le fichier Swagger, ainsi que les limites, voir les [détails du connecteur](/connectors/eventhubs/).

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [tous les autres connecteurs pour Azure Logic apps](../connectors/apis-list.md)