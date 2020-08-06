---
title: Tutoriel - Utiliser des événements IoT Hub pour déclencher des actions Azure Logic Apps
description: Ce tutoriel montre comment utiliser le service de routage d’événements d’Azure Event Grid, et créer des processus automatisés pour effectuer des actions Azure Logic Apps basées sur des événements IoT Hub.
services: iot-hub, event-grid
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 07/07/2020
ms.author: robinsh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 35359c63b79d9eea6f8f6ad688bd040428a39eb8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503444"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutoriel : Envoyer des notifications par e-mail concernant des événements Azure IoT Hub à l’aide d’Event Grid et de Logic Apps

Azure Event Grid vous permet de réagir aux événements dans IoT Hub en déclenchant des actions dans vos applications d’entreprise en aval.

Cet article présente un exemple de configuration qui utilise IoT Hub et Event Grid. À la fin, vous disposez d’une application logique Azure configurée pour envoyer un e-mail de notification chaque fois qu’un appareil est ajouté à votre hub IoT. 

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure actif. Si vous ne disposez d’aucun abonnement, vous pouvez [créer un compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/).

* Un compte e-mail auprès de n’importe quel fournisseur de messagerie pris en charge par Azure Logic Apps, tel qu’Outlook Office 365, Outlook.com ou Gmail. Ce compte e-mail permet d’envoyer les notifications d’événements. Pour obtenir la liste complète des connecteurs d’application logique pris en charge, consultez la [Vue d’ensemble des connecteurs](/connectors/).

  > [!IMPORTANT]
  > Avant d’utiliser Gmail, veillez à disposer d’un compte professionnel G-Suite (adresse e-mail avec un domaine personnalisé) ou d’un compte de consommateur Gmail (adresse e-mail avec @gmail.com ou @googlemail.com). Seuls les comptes professionnels G-Suite peuvent utiliser le connecteur Gmail avec d’autres connecteurs sans restriction dans Logic Apps. Si vous disposez d’un compte de consommateur Gmail, vous pouvez utiliser le connecteur Gmail uniquement avec certains services approuvés par Google, ou vous pouvez [créer une application cliente Google à utiliser pour l’authentification](/connectors/gmail/#authentication-and-bring-your-own-application). Pour plus d’informations, consultez [Stratégies de confidentialité et de sécurité des données pour les connecteurs Google dans Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Un Hub IoT dans Azure. Si vous n’en avez pas encore créé un, consultez [Prise en main d’IoT Hub](../iot-hub/quickstart-send-telemetry-dotnet.md) pour obtenir une procédure pas à pas.

## <a name="create-a-logic-app"></a>Créer une application logique

Tout d’abord, créez une application logique et ajoutez un déclencheur Event Grid qui supervise le groupe de ressources de votre machine virtuelle. 

### <a name="create-a-logic-app-resource"></a>Créer une ressource d’application logique

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**, puis tapez « application logique » dans la zone de recherche et appuyez sur Entrée. Sélectionnez **Application logique**  dans les résultats.

   ![Créer une application logique](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Sur l’écran suivant, sélectionnez **Créer**. 

1. Donnez à votre application logique un nom qui est unique dans votre abonnement, puis sélectionnez les mêmes abonnement, groupe de ressources et emplacement que votre hub IoT. 

   ![Champs pour la création d’une application logique](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Sélectionnez **Create** (Créer).

1. Une fois la ressource créée, accédez à votre application logique. Pour ce faire, sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé pour ce tutoriel. Recherchez ensuite l’application logique dans la liste des ressources, puis sélectionnez-la. 

1. Dans le concepteur Logic Apps, faites défiler la page vers le bas pour afficher **Modèles**. Choisissez **Application logique vide** afin de générer votre application logique à partir de zéro.

### <a name="select-a-trigger"></a>Sélectionner un déclencheur

Un déclencheur désigne un événement spécifique qui démarre votre application logique. Pour ce didacticiel, le déclencheur qui lance le flux de travail reçoit une demande via HTTP.  

1. Dans la barre de recherche des connecteurs et des déclencheurs, tapez **HTTP**.

1. Sélectionnez **Requête - Lors de la réception d’une requête HTTP** comme déclencheur. 

   ![Sélectionner le déclencheur de requête HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**. 

   ![Sélectionner le déclencheur de requête HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Collez l’exemple de code JSON suivant dans la zone de texte, puis sélectionnez **Terminé** :

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. Vous pouvez recevoir une notification contextuelle indiquant **N’oubliez pas d’inclure un en-tête Content-Type défini sur application/JSON dans votre demande**. Vous pouvez ignorer cette suggestion et passer à la section suivante. 

### <a name="create-an-action"></a>Créer une action

Les actions sont toutes les étapes qui se produisent une fois que le déclencheur démarre le flux de travail de l’application logique. Pour ce didacticiel, l’action doit envoyer une notification par e-mail à partir de votre fournisseur d’e-mail. 

1. Sélectionnez **Nouvelle étape**. Une fenêtre permettant de **Choisir une action** s’ouvre.

1. Recherchez **E-mail**.

1. Selon votre fournisseur de messagerie, recherchez et sélectionnez le connecteur correspondant. Ce didacticiel utilise **Office 365 Outlook**. Les étapes pour les autres fournisseurs d’e-mail sont similaires. 

   ![Sélectionner le connecteur du fournisseur d’e-mail](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Sélectionner l’action **Envoyer un message électronique**. 

1. Si vous y êtes invité, connectez-vous à votre compte e-mail. 

1. Générez votre modèle d’e-mail. 

   * **À** : entrez l’adresse e-mail à laquelle recevoir les e-mails de notification. Pour ce didacticiel, utilisez un compte e-mail auquel vous pouvez accéder à des fins de test. 

   * **Objet** : Renseignez le texte de l’objet. Quand vous cliquez sur la zone de texte Objet, vous pouvez sélectionner du contenu dynamique à inclure. Par exemple, ce tutoriel utilise `IoT Hub alert: {event Type}`. Si vous ne voyez pas de contenu dynamique, sélectionnez le lien hypertexte **Ajouter du contenu dynamique** : cela l’active ou le désactive.

   * **Corps**: écrivez le texte de votre e-mail. Sélectionnez les propriétés JSON à partir de l’outil de sélection pour inclure du contenu dynamique en fonction des données d’événement. Si vous ne voyez pas le contenu dynamique, sélectionnez le lien hypertexte **Ajouter du contenu dynamique** sous la zone de texte **Corps**. Si cette opération n’affiche pas les champs souhaités, cliquez sur *plus* dans l’écran Contenu dynamique pour inclure les champs de l’action précédente.

   Votre modèle d’e-mail peut ressembler à cet exemple :

   ![Renseigner les informations d’e-mail](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Enregistrez votre application logique. 

### <a name="copy-the-http-url"></a>Copier l’URL HTTP

Avant de quitter le Concepteur d’applications logiques, copiez l’URL qu’écoutent vos applications logiques dans l’attente d’un déclencheur. Cette URL vous permet de configurer Event Grid. 

1. Développez la zone de configuration de déclencheur **Lors de la réception d’une demande HTTP** en cliquant dessus. 

1. Copiez la valeur du champ **URL HTTP POST** en sélectionnant le bouton de copie en regard de celui-ci. 

   ![Copier l’URL HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Enregistrez cette URL afin de pouvoir y faire référence dans la section suivante. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurer l’abonnement pour les événements IoT Hub

Dans cette section, vous configurez votre hub IoT pour publier des événements à mesure qu’ils se produisent. 

1. Accédez à votre hub IoT dans le portail Azure. Pour ce faire, sélectionnez **Groupes de ressources**, puis le groupe de ressources de ce tutoriel. Sélectionnez ensuite votre hub IoT dans la liste de ressources.

2. Sélectionnez **Événements**.

   ![Ouvrir les détails Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Sélectionnez **Abonnement aux événements**. 

   ![Créer un abonnement aux événements](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Créez l’abonnement aux événements avec les valeurs suivantes : 

    1. Dans la section **DÉTAILS DES ABONNEMENTS AUX ÉVÉNEMENTS**, effectuez les tâches suivantes :
        1. Indiquez un **nom** pour l’abonnement à un événement. 
        2. Sélectionnez **Event Grid Schema** comme **Schéma d’événement**. 
   2. Dans la section **DÉTAILS DE LA RUBRIQUE**, effectuez les tâches suivantes :
       1. Confirmez que le **Type de rubrique** est **IoT Hub**. 
       2. Confirmez que le nom du hub IoT est défini en tant que valeur du champ **Ressource source**. 
       3. Entrez un nom pour la **rubrique système** qui sera créé pour vous. Pour en savoir plus sur les rubriques système, consultez [Vue d’ensemble des rubriques système](system-topics.md).
   3. Dans la section **TYPES D’ÉVÉNEMENTS**, effectuez les tâches suivantes : 
        1. Pour **Filtrer sur les types d’événement**, décochez tous les choix sauf **Appareil créé**.

           ![types d’événements d’abonnement](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   4. Dans la section **DÉTAILS DU POINT DE TERMINAISON**, effectuez les tâches suivantes : 
       1. Sélectionnez **Webhook** comme **Type de point de terminaison**.
       2. Cliquez sur **Sélectionner un point de terminaison**, collez l’URL copiée à partir de votre application logique, puis confirmez la sélection.

         ![sélectionner l’URL du point de terminaison](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Quand vous avez terminé, le volet doit ressembler à l’exemple suivant : 

        ![Exemple de formulaire d’abonnement aux événements](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Vous pourriez enregistrer l’abonnement aux événements ici et recevoir des notifications pour chaque appareil qui est créé dans votre hub IoT. Pour ce tutoriel, cependant, nous allons utiliser les champs facultatifs pour filtrer des appareils spécifiques. Sélectionnez **Filtres** en haut du volet.

6. Sélectionnez **Ajouter un nouveau filtre**. Renseignez les champs avec ces valeurs :

   * **Clé** : Sélectionnez `Subject`.

   * **Opérateur** : Sélectionnez `String begins with`.

   * **Valeur** :  entrez `devices/Building1_` pour filtrer les événements d’appareil dans l’édifice 1.
  
   Ajoutez un autre filtre avec ces valeurs :

   * **Clé** : Sélectionnez `Subject`.

   * **Opérateur** : Sélectionnez `String ends with`.

   * **Valeur** : entrez `_Temperature` pour filtrer les événements d’appareil liés à la température.

   L’onglet **Filtres** de votre abonnement aux événements doit maintenant ressembler à l’image suivante :

   ![Ajout de filtres à l’abonnement aux événements](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Sélectionnez **Créer** pour enregistrer l’abonnement aux événements.

## <a name="create-a-new-device"></a>Créer un appareil

Testez votre application logique en créant un appareil pour déclencher un e-mail de notification d’événement. 

1. À partir de votre hub IoT, sélectionnez **Appareils IoT**. 

2. Sélectionnez **Nouveau**.

3. Pour **ID d’appareil**, entrez `Building1_Floor1_Room1_Light`.

4. Sélectionnez **Enregistrer**. 

5. Vous pouvez ajouter plusieurs appareils avec différents ID d’appareil pour tester les filtres de l’abonnement aux événements. Essayez ces exemples : 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Si vous avez ajouté les quatre exemples, votre liste d’appareils IoT doit ressembler à l’image suivante :

   ![Liste d’appareils IoT Hub](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Une fois que vous avez ajouté quelques appareils à votre hub IoT, vérifiez votre e-mail pour voir quels sont ceux qui ont déclenché l’application logique. 

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

Au lieu d’utiliser le portail Azure, vous pouvez effectuer les étapes IoT Hub à l’aide de l’interface de ligne de commande Azure. Pour plus d’informations, consultez les pages de l’interface de ligne de commande Azure consacrées à la [création d’un abonnement aux événements](/cli/azure/eventgrid/event-subscription) et à la [création d’un appareil IoT](/cli/azure/ext/azure-iot/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Ce didacticiel utilise des ressources qui peuvent entraîner des frais sur votre abonnement Azure. Quand vous avez terminé de tester le tutoriel et vos résultats, désactivez ou supprimez les ressources que vous ne souhaitez pas conserver. 

Pour supprimer toutes les ressources créées dans ce tutoriel, supprimez le groupe de ressources. 

1. Sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé pour ce tutoriel.

2. Dans le volet Groupe de ressources, sélectionnez **Supprimer un groupe de ressources**. Vous êtes invité à entrer le nom du groupe de ressources, après quoi vous pouvez le supprimer. Toutes les ressources qu’il contient sont également supprimées.

Si vous ne voulez pas supprimer toutes les ressources, vous pouvez les gérer une par une. 

Pour ne pas perdre le travail effectué sur votre application logique, désactivez-la au lieu de la supprimer. 

1. Accédez à votre application logique.

2. Dans le panneau **Vue d’ensemble**, sélectionner **Supprimer** ou **Désactiver**. 

Chaque abonnement peut avoir un hub IoT gratuit. Si vous avez créé un hub gratuit pour ce didacticiel, vous n’avez pas besoin de le supprimer pour éviter les frais.

1. Accédez à votre hub IoT. 

2. Dans le panneau **Vue d’ensemble**, sélectionnez **Supprimer**. 

Même si vous conservez votre hub IoT, vous pouvez souhaiter supprimer l’abonnement aux événements que vous avez créé. 

1. Dans votre hub IoT, sélectionnez **Grille d’événement**.

2. Sélectionnez l’abonnement aux événements que vous souhaitez supprimer. 

3. Sélectionnez **Supprimer**. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrez-en plus sur la [réaction aux événements IoT Hub en utilisant Event Grid pour déclencher des actions](../iot-hub/iot-hub-event-grid.md).
* [Apprendre à commander des événements d’état de la connexion et de la déconnexion d’appareils](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Découvrez ce que vous pouvez faire d’autre avec [Event Grid](overview.md).
