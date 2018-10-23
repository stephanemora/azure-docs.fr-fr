---
title: Capturer des événements à partir d’un espace Azure Digital Twins | Microsoft Docs
description: Découvrez comment recevoir des notifications à partir de vos espaces en intégrant Azure Digital Twins à Logic Apps en suivant la procédure décrite dans ce didacticiel.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 91dd16938efbd1e24419352f66e3238646a77e8a
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323282"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-using-logic-apps"></a>Didacticiel : Recevoir des notifications à partir de vos espaces Azure Digital Twins à l’aide de Logic Apps

Une fois que vous avez déployé votre instance Azure Digital Twins, approvisionné vos espaces et implémenté des fonctions personnalisées pour surveiller des conditions spécifiques, vous pouvez en avertir votre administrateur de bureau par e-mail quand les conditions surveillées se présentent. 

Dans [le premier didacticiel](tutorial-facilities-setup.md), vous avez configuré le graphique spatial d’un bâtiment imaginaire, avec une salle contenant des capteurs de détection des mouvements, du niveau de dioxyde de carbone et de la température. Dans [le deuxième didacticiel](tutorial-facilities-udf.md), vous avez approvisionné votre graphique et une fonction personnalisée a appelé la fonction définie par un utilisateur pour surveiller ces valeurs de capteur et déclencher des notifications quand la salle est vide et quand la température et le niveau de dioxyde de carbone se trouvent dans une plage raisonnable. Ce didacticiel montre comment intégrer ces notifications à Azure Logic Apps pour envoyer des e-mails quand une salle de ce type est disponible. Un administrateur de bureau peut utiliser ces informations pour aider les employés à réserver la salle de réunion favorisant le plus la productivité. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Intégrer des événements avec Event Grid
> * Notifier des événements avec une application logique
    
## <a name="prerequisites"></a>Prérequis

Ce didacticiel suppose que vous avez [configuré](tutorial-facilities-setup.md) et [approvisionné](tutorial-facilities-udf.md) votre service Azure Digital Twins. Avant de poursuivre, assurez-vous que vous avez les éléments suivants :
- Un [compte Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une instance de Digital Twins en cours d’exécution.
- Les [exemples Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) téléchargés et extraits sur votre machine de travail.
- [Le kit SDK .NET Core version 2.1.403 ou supérieure](https://www.microsoft.com/net/download) sur votre machine de développement pour exécuter l’exemple. Exécutez `dotnet --version` pour vérifier que la version appropriée est installée. 
- Un compte Office 365 pour envoyer des e-mails de notification.

## <a name="integrate-events-with-event-grid"></a>Intégrer des événements avec Event Grid 
Dans cette section, vous allez configurer un service [Event Grid](../event-grid/overview.md) pour collecter des événements à partir de votre instance Digital Twins et les rediriger vers un [gestionnaire d’événements](../event-grid/event-handlers.md) tel que Logic Apps.

### <a name="create-event-grid-topic"></a>Créer une rubrique Event Grid
Les [rubriques Event Grid](../event-grid/concepts.md#topics) fournissent une interface pour acheminer les événements générés par la fonction définie par un utilisateur. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le panneau de navigation de gauche, cliquez sur **Créer une ressource**. 

1. Recherchez et sélectionnez **Rubrique Event Grid**. Cliquez sur **Créer**.

1. Entrez un **nom** pour votre rubrique Event Grid, puis choisissez **l’abonnement**. Sélectionnez le **groupe de ressources** que vous avez utilisé ou créé pour votre instance Digital Twins et **l’emplacement**. Cliquez sur **Créer**. 

    ![Créer une rubrique Event Grid](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Accédez à la rubrique Event Grid à partir de votre groupe de ressources, cliquez sur **Vue d’ensemble**et copiez la valeur du champ **Point de terminaison de la rubrique** dans un fichier temporaire. Cette URL vous est nécessaire pour la section suivante. 

1. Cliquez sur **Clés d’accès**, puis copiez **Clé 1** et **Clé 2** dans un fichier temporaire. Ces valeurs vous sont utiles pour créer le point de terminaison dans la section suivante.

    ![Clés Event Grid](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Créer un point de terminaison pour la rubrique Event Grid

1. Dans la fenêtre de commande, vérifiez que vous êtes dans le dossier **_occupation-quickstart\src_** de l’exemple Digital Twins.

1. Ouvrez le fichier **_actions\createEndpoints.yaml_** dans votre éditeur Visual Studio Code. Assurez-vous que son contenu est semblable à ce qui suit :

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. Remplacez l’espace réservé `Primary_connection_string_for_your_Event_Grid` par la valeur de **Key1**. 

1. Remplacez l’espace réservé `Secondary_connection_string_for_your_Event_Grid` par la valeur de **Key2**.

1. Remplacez l’espace réservé `Event_Grid_Topic_Path` par le chemin d’accès à la rubrique Event Grid. Récupérez ce chemin d’accès en supprimant *https://* et les chemins d’accès à la ressource de fin de l’URL de **Point de terminaison de la rubrique**. Cela doit ressembler au format suivant : **yourEventGridName.yourLocation.eventgrid.azure.net**. 

    > [!IMPORTANT]
    > Entrez toutes les valeurs sans les guillemets. Veillez à laisser au moins une espace après les deux-points dans le fichier *YAML*. Vous pouvez également valider le contenu de votre fichier *YAML* via un validateur YAML en ligne comme [cet outil](https://onlineyamltools.com/validate-yaml).

1. Enregistrez et fermez le fichier. Dans la fenêtre de commande, exécutez la commande suivante et connectez-vous lorsque vous y êtes invité. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Cette commande crée le point de terminaison d’Event Grid. 

   ![Points de terminaison pour Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-app"></a>Notifier des événements avec une application logique
Le service [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vous permet de créer des tâches automatisées pour les événements provenant d’autres services. Dans cette section, vous allez configurer Logic Apps pour créer des notifications par e-mail pour les événements acheminés depuis vos capteurs spatiaux, à l’aide d’une [rubrique Event Grid](../event-grid/overview.md).

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource**.

1. Recherchez et sélectionnez une nouvelle ressource **Application logique**. Cliquez sur **Créer**.

1. Entrez un **nom** pour votre application logique et sélectionnez votre **abonnement**, votre **groupe de ressources** et votre **emplacement**. Cliquez sur **Créer**.

    ![Créer une application logique](./media/tutorial-facilities-events/create-logic-app.png)

1. Ouvrez votre application logique une fois déployée, puis ouvrez le volet **Concepteur d’application logique**. 

1. Sélectionnez le déclencheur **When an Event Grid event occurs** (Quand un événement Event Grid se produit). **Connectez-vous** à votre locataire avec votre compte Azure lorsque vous y êtes invité. Confirmez pour **autoriser l’accès** à votre Event Grid lorsque vous y êtes invité. Cliquez sur **Continuer**.

1. Dans la fenêtre **When a resource event occurs (Preview)** (Quand un événement de ressource se produit (préversion)), 
    1. sélectionnez **l’abonnement** que vous avez utilisé pour créer l’Event Grid précédemment,

    1. sélectionnez **Microsoft.EventGrid.Topics** en tant que **Type de ressource**,

    1. sélectionnez votre ressource Event Grid dans la zone de liste déroulante pour **Nom de la ressource**.

    ![Créer une application logique](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Cliquez sur le bouton **Nouvelle étape**.

1. Dans la fenêtre **Choisir une action**,
    1. recherchez l’expression *analyser json*, puis sélectionnez l’action **Analyser JSON**.

    1. Cliquez sur le champ **Contenu** et sélectionnez **Corps** dans la liste **Contenu dynamique**.

    1. Cliquez sur **Utiliser l’exemple de charge utile pour générer le schéma**. Collez la charge utile JSON suivante, puis cliquez sur **Terminé**.

        ```JSON
        {
        "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
        "subject": "UdfCustom",
        "data": {
          "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
          "ResourceType": "Space",
          "Payload": "\"Air quality is poor.\"",
          "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
        },
        "eventType": "UdfCustom",
        "eventTime": "0001-01-01T00:00:00Z",
        "dataVersion": "1.0",
        "metadataVersion": "1",
        "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
        }
        ```
    
    Cette charge utile a des valeurs fictives. L’application logique utilise cet exemple de charge utile pour générer un **schéma**.
    
    ![Application logique - Analyser JSON pour Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Cliquez sur le bouton **Nouvelle étape**.

1. Dans la fenêtre **Choisir une action**,
    1. recherchez et sélectionnez **Contrôle des conditions** dans la liste **Actions**. 

    1. Cliquez sur la première zone de texte **Choose a value** (Choisir une valeur) et sélectionnez **eventType** dans la liste **Contenu dynamique** de la fenêtre **Analyser JSON**.

    1. Cliquez sur la deuxième zone de texte **Choose a value** (Choisir une valeur) et tapez *UdfCustom*.

    ![Application logique - Analyser JSON pour Event Grid](./media/tutorial-facilities-events/logic-app-condition.png)

1. Dans la fenêtre **Si true**,
    1. cliquez sur **Ajouter une action** et sélectionnez *Office 365 Outlook*.

    1. Dans la liste **Actions**, sélectionnez **Envoyer un message électronique**. Cliquez sur **Se connecter** et utilisez vos informations d’identification du compte de messagerie. Cliquez sur **Autoriser l’accès** lorsque vous y êtes invité.

    1. Dans la zone **To** (À), entrez votre ID de messagerie pour recevoir des notifications. Dans le champ **Objet**, entrez le texte *Digital Twins notification for poor air quality in space* (Notification Digital Twins pour signaler la mauvaise qualité de l’air dans un espace), puis sélectionnez **TopologyObjectId** dans la liste **Contenu dynamique** pour **Analyser JSON**.

    1. Dans le champ **Corps** de la même fenêtre, entrez un texte similaire à celui-ci : *Poor air quality detected in a room, and temperature needs to be adjusted* (Mauvaise qualité d’air détectée dans une salle, et température à ajuster). N’hésitez pas à apporter des précisions en utilisant les éléments de la liste **Contenu dynamique** comme indiqué ci-dessous.

    ![Application logique - Envoyer un e-mail](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Cliquez sur le bouton **Enregistrer** en haut du volet **Concepteur d’application logique**.

1. Veillez à simuler des données de capteur en accédant au dossier **_device-connectivity_** de l’exemple Digital Twins d’une fenêtre de commande et en exécutant `dotnet run`.

Dans quelques minutes, vous devez commencer à recevoir des notifications par e-mail provenant de cette application logique. 

   ![Application logique - Envoyer un e-mail](./media/tutorial-facilities-events/logic-app-notification.png)

Pour ne plus recevoir ces e-mails, accédez à votre **application logique** dans le portail et sélectionnez le volet **Vue d’ensemble**. Cliquez sur **Désactiver**.


## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez arrêter votre découverte d’Azure Digital Twins ici, vous pouvez supprimer les ressources créées dans ce didacticiel :

1. Dans le menu de gauche du [Portail Azure](http://portal.azure.com), cliquez sur **Toutes les ressources**, puis sélectionnez votre groupe de ressources Digital Twins et **supprimez-le**.
2. Si vous le souhaitez, vous pouvez également supprimer les exemples d’applications sur votre machine de travail. 


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez passer au didacticiel suivant pour apprendre à visualiser vos données de capteur, analyser les tendances et détecter les anomalies. 
> [!div class="nextstepaction"]
> [Tutorial: Visualize and analyze events from your Azure Digital Twins spaces using Time Series Insights](tutorial-facilities-analyze.md) (Didacticiel : Visualiser et analyser des événements à partir de vos espaces Azure Digital Twins à l’aide de Time Series Insights)

Vous pouvez également continuer à parfaire vos connaissances sur les graphiques d’intelligence spatiale et les modèles objets dans Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph](concepts-objectmodel-spatialgraph.md) (Comprendre le graphique d’intelligence spatiale et les modèles objets Digital Twins)