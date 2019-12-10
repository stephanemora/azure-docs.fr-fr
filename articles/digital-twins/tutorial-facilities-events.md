---
title: 'Didacticiel : Capturer des événements d’appareil à partir d’un espace IoT - Azure Digital Twins | Microsoft Docs'
description: Découvrez comment recevoir des notifications à partir de vos espaces en intégrant Azure Digital Twins à Logic Apps en suivant la procédure décrite dans ce didacticiel.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: 7700c61a978532a63fc5b3298d45b8e7041dba40
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790350"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Didacticiel : Recevoir des notifications à partir de vos espaces Azure Digital Twins avec Logic Apps

Après avoir déployé votre instance Azure Digital Twins, puis provisionné vos espaces et implémenté des fonctions personnalisées pour surveiller des conditions spécifiques, vous pouvez avertir par e-mail votre administrateur des bureaux lorsque ces conditions surveillées se présentent.

Dans [le premier tutoriel](tutorial-facilities-setup.md), vous avez configuré le graphe spatial d’un bâtiment imaginaire. Une salle du bâtiment contient des capteurs de mouvement, de dioxyde de carbone et de température. Dans [le deuxième tutoriel](tutorial-facilities-udf.md), vous avez provisionné votre graphe et une fonction définie par l’utilisateur pour surveiller ces valeurs de capteur et déclencher des notifications quand la salle est vide, et quand la température et le niveau de dioxyde de carbone se situent dans une plage satisfaisante. 

Ce didacticiel montre comment intégrer ces notifications à Azure Logic Apps pour envoyer des e-mails quand une salle de ce type est disponible. Un administrateur de bureau peut utiliser ces informations pour aider les employés à réserver la salle de réunion favorisant le plus la productivité.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Intégrer des événements avec Azure Event Grid.
> * Notifier des événements avec Logic Apps.

## <a name="prerequisites"></a>Prérequis

Ce didacticiel suppose que vous avez [configuré](tutorial-facilities-setup.md) et [approvisionné](tutorial-facilities-udf.md) votre configuration Azure Digital Twins. Avant de poursuivre, assurez-vous que vous avez les éléments suivants :

- Un [compte Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une instance de Digital Twins en cours d’exécution.
- Les [exemples Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) téléchargés et extraits sur votre machine de travail.
- [Le kit SDK .NET Core version 2.1.403 ou ultérieure](https://www.microsoft.com/net/download) sur votre machine de développement pour exécuter l’exemple. Exécutez `dotnet --version` pour vérifier que la version appropriée est installée.
- Un compte [Office 365](https://products.office.com/home) pour envoyer des e-mails de notification.

> [!TIP]
> Utilisez un nom d’instance Digital Twins unique si vous provisionnez une nouvelle instance.

## <a name="integrate-events-with-event-grid"></a>Intégrer des événements avec Event Grid

Dans cette section, vous configurez [Event Grid](../event-grid/overview.md) pour collecter des événements à partir de votre instance Azure Digital Twins et les rediriger vers un [gestionnaire d’événements](../event-grid/event-handlers.md) tel que Logic Apps.

### <a name="create-an-event-grid-topic"></a>Créer une rubrique de grille d’événements

Une [rubrique de grille d’événements](../event-grid/concepts.md#topics) offre une interface qui permet d’acheminer les événements générés par la fonction définie par l’utilisateur. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le volet de gauche, sélectionnez **Créer une ressource**. 

1. Recherchez et sélectionnez **Rubrique Event Grid**. Sélectionnez **Create** (Créer).

1. Entrez un **nom** pour votre rubrique Event Grid, puis choisissez **l’abonnement**. Sélectionnez le **Groupe de ressources** que vous avez utilisé ou créé pour votre instance Digital Twins, puis l’**Emplacement**. Sélectionnez **Create** (Créer). 

    [![Créer une rubrique de grille d’événements](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. Accédez à la rubrique de grille d’événements à partir de votre groupe de ressources, sélectionnez **Vue d’ensemble** et copiez la valeur du **Point de terminaison de la rubrique** dans un fichier temporaire. Vous aurez besoin de cette URL à la section suivante. 

1. Sélectionnez **Clés d’accès**, puis copiez **Clé 1** et **Clé 2** dans un fichier temporaire. Ces valeurs vous seront utiles pour créer le point de terminaison à la section suivante.

    [![Clés Event Grid](./media/tutorial-facilities-events/event-grid-keys.png)](./media/tutorial-facilities-events/event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Créer un point de terminaison pour la rubrique de grille d’événements

1. Dans la fenêtre de commande, vérifiez que vous êtes dans le dossier **occupancy-quickstart\src** de l’exemple Digital Twins.

1. Ouvrez le fichier **actions\createEndpoints.yaml** dans votre éditeur Visual Studio Code. Assurez-vous que son contenu est semblable à ce qui suit :

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: <Primary connection string for your Event Grid>
      secondaryConnectionString: <Secondary connection string for your Event Grid>
      path: <Event Grid Topic Name without https:// and /api/events, e.g. eventgridname.region.eventgrid.azure.net>
    ```

1. Remplacez l’espace réservé `<Primary connection string for your Event Grid>` par la valeur de **Key 1**.

1. Remplacez l’espace réservé `<Secondary connection string for your Event Grid>` par la valeur de **Key 2**.

1. Remplacez l’espace réservé pour **path** par le chemin d’accès à la rubrique Event Grid. Récupérez ce chemin à partir de l’URL du **Point de terminaison de la rubrique**, en supprimant **https://** et les chemins de ressource à la fin. Cela doit ressembler au format suivant : *yourEventGridName.yourLocation.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Entrez toutes les valeurs sans les guillemets. Veillez à laisser au moins un espace après les deux-points dans le fichier YAML. Vous pouvez également valider le contenu de votre fichier YAML au moyen d’un validateur YAML en ligne, tel que [cet outil](https://onlineyamltools.com/validate-yaml).

1. Enregistrez et fermez le fichier. Dans la fenêtre de commande, exécutez la commande suivante et connectez-vous lorsque vous y êtes invité. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Cette commande crée le point de terminaison d’Event Grid. 

   [![Points de terminaison pour Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>Notifier des événements avec Logic Apps

Vous pouvez utiliser le service [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et créer des tâches automatisées pour les événements provenant d’autres services. Dans cette section, vous configurez Logic Apps et créez des notifications par e-mail pour les événements acheminés depuis vos capteurs spatiaux, à l’aide d’une [rubrique de grille d’événements](../event-grid/overview.md).

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.

1. Recherchez et sélectionnez une nouvelle ressource **Application logique**. Sélectionnez **Create** (Créer).

1. Entrez un **Nom** pour votre ressource Logic Apps et sélectionnez vos **Abonnement**, **Groupe de ressources** et **Emplacement**. Sélectionnez **Create** (Créer).

    [![Créer une ressource Logic Apps](./media/tutorial-facilities-events/create-logic-app.png)](./media/tutorial-facilities-events/create-logic-app.png#lightbox)

1. Ouvrez votre ressource Logic Apps durant son déploiement, puis ouvrez le volet **Concepteur d’application logique**. 

1. Sélectionnez le déclencheur **When an Event Grid ressource event occurs** (Quand un événement de ressource Event Grid se produit). Développez l’option **Azure Event Grid**, puis connectez-vous à votre locataire à l’aide de votre compte Azure quand vous y êtes invité. Sélectionnez **Autoriser l’accès** pour votre ressource Event Grid si vous y êtes invité. Sélectionnez **Continuer**.

1. Dans la fenêtre **Quand un événement de ressource se produit** : 
   
   a. Sélectionnez l’**Abonnement** que vous avez utilisé pour créer la rubrique de grille d’événements.

   b. Sélectionnez **Microsoft.EventGrid.Topics** pour le **Type de ressource**.

   c. Sélectionnez votre ressource Event Grid dans la zone de liste déroulante pour le **Nom de la ressource**.

   [![Volet Concepteur d’application logique](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. Sélectionnez le bouton **Nouvelle étape**.

1. Dans la fenêtre **Choisir une action** :

   a. recherchez l’expression **analyser json**, puis sélectionnez l’action **Analyser JSON**.

   b. Dans le champ **Contenu**, sélectionnez **Corps** à partir de la liste **Contenu dynamique**.

   c. Sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**. Collez la charge utile JSON suivante, puis sélectionnez **Terminé**.

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

    Cette charge utile a des valeurs fictives. Logic Apps utilise cet exemple de charge utile pour générer un *schéma*.

    [![Fenêtre Analyser JSON de Logic Apps pour Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. Sélectionnez le bouton **Nouvelle étape**.

1. Dans la fenêtre **Choisir une action** :

   a. Sélectionnez **Contrôle > Condition** ou recherchez **Condition** dans la liste **Actions**. 

   b. Dans la première zone de texte **Choisir une valeur**, sélectionnez **eventType** dans la liste **Contenu dynamique** de la fenêtre **Analyser JSON**.

   c. Dans la deuxième zone de texte **Choisir une valeur**, entrez `UdfCustom`.

   [![Conditions sélectionnées](./media/tutorial-facilities-events/logic-app-condition.png)](./media/tutorial-facilities-events/logic-app-condition.png#lightbox)

1. Dans la fenêtre **Si true** :

   a. Sélectionnez **Ajouter une action**, puis **Office 365 Outlook**.

   b. Dans la liste **Actions**, sélectionnez **Envoyer un e-mail (V2)** . Sélectionnez **Se connecter** et utilisez vos informations d’identification de compte e-mail. Sélectionnez **Autoriser l’accès** si vous y êtes invité.

   c. Dans la zone **To** (À), entrez votre ID de messagerie pour recevoir des notifications. Dans **Objet**, entrez le texte **Notification Digital Twins pour la mauvaise qualité de l’air dans l’espace**. Sélectionnez ensuite **TopologyObjectId** à partir de la liste **Contenu dynamique** pour **Analyser JSON**.

   d. Sous **Corps** dans la même fenêtre, entrez du texte similaire à ceci : **Qualité de l’air médiocre détectée dans une pièce. La température doit être ajustée**. N’hésitez pas à apporter des précisions en utilisant les éléments de la liste **Contenu dynamique**.

   [![Sélections de « Envoyer un e-mail » dans Logic Apps](./media/tutorial-facilities-events/logic-app-send-email.png)](./media/tutorial-facilities-events/logic-app-send-email.png#lightbox)

1. Sélectionnez le bouton **Enregistrer** en haut du volet **Concepteur Logic Apps**.

1. Veillez à simuler des données de capteur en accédant dans une fenêtre de commande au dossier **device-connectivity** de l’exemple Digital Twins et en exécutant `dotnet run`.

Dans quelques minutes, vous devriez commencer à recevoir des notifications par e-mail provenant de cette ressource Logic Apps. 

   [![Notification par e-mail](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

Pour ne plus recevoir ces e-mails, accédez à votre ressource Logic Apps dans le portail et sélectionnez le volet **Vue d’ensemble**. Sélectionnez **Désactiver**.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez arrêter votre exploration d’Azure Digital Twins ici, vous pouvez supprimer les ressources créées dans ce tutoriel :

1. Dans le menu de gauche du [portail Azure](https://portal.azure.com), sélectionnez **Toutes les ressources**, puis votre groupe de ressources Digital Twins et **Supprimer**.

    > [!TIP]
    > Si vous avez rencontré des difficultés pour supprimer votre instance de Digital Twins, une mise à jour du service a été déployée avec le correctif. Réessayez de supprimer votre instance.

2. Si nécessaire, supprimez les exemples d’applications de votre machine de travail.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment visualiser vos données de capteur, analyser les tendances et détecter les anomalies, passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Tutoriel : Visualiser et analyser des événements à partir de vos espaces Azure Digital Twins à l’aide de Time Series Insights](tutorial-facilities-analyze.md)

Vous pouvez également parfaire vos connaissances sur les modèles objet et les graphes d’intelligence spatiale dans Azure Digital Twins :

> [!div class="nextstepaction"]
> [Comprendre le graphique d’intelligence spatiale et les modèles objets Digital Twins](concepts-objectmodel-spatialgraph.md)
