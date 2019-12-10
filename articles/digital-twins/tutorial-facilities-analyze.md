---
title: 'Didacticiel : Analyser des événements dans Time Series Insights - Azure Digital Twins | Microsoft Docs'
description: Découvrez comment visualiser et analyser des événements à partir de vos espaces Azure Digital Twins à l’aide d’Azure Time Series Insights, en suivant les étapes de ce tutoriel.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: cab13fd65e9fdbd7179e6ba759b1aa696ef95fa1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790321"
---
# <a name="tutorial-visualize-and-analyze-events-from-azure-digital-twins-by-using-time-series-insights"></a>Didacticiel : Visualiser et analyser des événements à partir d’Azure Digital Twins à l’aide de Time Series Insights

Après avoir déployé votre instance Azure Digital Twins, puis provisionné vos espaces et implémenté une fonction personnalisée visant à surveiller des conditions spécifiques, vous pouvez visualiser les événements et les données provenant de vos espaces pour rechercher des tendances et des anomalies.

Dans [le premier tutoriel](tutorial-facilities-setup.md), vous avez configuré le graphe spatial d’un bâtiment imaginaire, avec une salle contenant des capteurs de dioxyde de carbone, de température et de détection de mouvement. Dans [le deuxième didacticiel](tutorial-facilities-udf.md), vous avez approvisionné votre graphique et une fonction définie par l’utilisateur. La fonction surveille ces valeurs de capteur et déclenche des notifications pour les conditions appropriées. Autrement dit, la salle est vide, et les niveaux de température et de dioxyde de carbone sont normaux.

Ce tutoriel vous montre comment intégrer les notifications et les données provenant de votre configuration Azure Digital Twins avec Azure Time Series Insights. Vous pouvez ainsi visualiser vos valeurs de capteur dans le temps. Vous pouvez rechercher des tendances, par exemple la salle qui est la plus utilisée, et les moments de la journée où elle est la plus occupée. Vous pouvez également détecter des anomalies, comme le fait que certaines salles soient moins bien aérées ou plus chaudes, ou encore qu’une partie de votre bâtiment envoie constamment des valeurs de température élevées indiquant un climatiseur défectueux.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Envoyer des données en utilisant Azure Event Hubs.
> * Analyser avec Time Series Insights.

## <a name="prerequisites"></a>Prérequis

Ce didacticiel suppose que vous avez [configuré](tutorial-facilities-setup.md) et [approvisionné](tutorial-facilities-udf.md) votre configuration Azure Digital Twins. Avant de poursuivre, assurez-vous que vous avez les éléments suivants :

- Un [compte Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une instance de Digital Twins en cours d’exécution.
- Les [exemples Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) téléchargés et extraits sur votre machine de travail.
- [Le kit SDK .NET Core version 2.1.403 ou ultérieure](https://www.microsoft.com/net/download) sur votre machine de développement pour exécuter l’exemple. Exécutez `dotnet --version` pour vérifier que la version appropriée est installée.

> [!TIP]
> Utilisez un nom d’instance Digital Twins unique si vous provisionnez une nouvelle instance.

## <a name="stream-data-by-using-event-hubs"></a>Envoyer des données en utilisant Azure Event Hubs

Vous pouvez utiliser le service [Event Hubs](../event-hubs/event-hubs-about.md) pour créer un pipeline et envoyer vos données. Cette section vous montre comment créer votre hub d’événements en tant que connecteur entre vos instances Azure Digital Twins et Azure Time Series Insights.

### <a name="create-an-event-hub"></a>Créer un hub d’événements

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le volet de gauche, sélectionnez **Créer une ressource**.

1. Recherchez et sélectionnez **Event Hubs**. Sélectionnez **Create** (Créer).

    [![Créer un espace de noms Event Hubs](./media/tutorial-facilities-analyze/create-event-hubs.png)](./media/tutorial-facilities-analyze/create-event-hubs.png#lightbox)

1. Entrez un **Nom** pour votre espace de noms Event Hubs. Choisissez **Standard** comme **Niveau tarifaire**, votre **Abonnement**, le **Groupe de ressources** que vous avez utilisé pour votre instance Digital Twins et l’**Emplacement**. Sélectionnez **Create** (Créer).

1. Dans le déploiement d’espace de noms Event Hubs, sélectionnez le volet **Vue d’ensemble**, puis **Accéder à la ressource**.

    [![Espace de noms Event Hubs après le déploiement](./media/tutorial-facilities-analyze/open-event-hub-ns.png)](./media/tutorial-facilities-analyze/open-event-hub-ns.png#lightbox)

1. Dans le volet **Vue d’ensemble** de l’espace de noms Event Hubs, sélectionnez le bouton **Hub d’événements** en haut.
    [![Bouton Hub d’événements](./media/tutorial-facilities-analyze/create-event-hub.png)](./media/tutorial-facilities-analyze/create-event-hub.png#lightbox)

1. Entrez un **Nom** pour votre hub d’événements et sélectionnez **Créer**.

   Une fois déployé, le hub d’événements apparaît dans le volet **Event Hubs** de l’espace de noms Event Hubs avec un état **Actif**. Sélectionnez ce hub d’événements pour ouvrir son volet **Vue d’ensemble**.

1. Sélectionnez le bouton **Groupe de consommateurs** en haut et entrez un nom tel que **tsievents** pour le groupe de consommateurs. Sélectionnez **Create** (Créer).

    [![Groupe de consommateurs Event Hub](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   Une fois créé, le groupe de consommateurs apparaît dans la liste en bas du volet **Vue d’ensemble** du hub d’événements.

1. Ouvrez le volet **Stratégies d’accès partagé** pour votre hub d’événements et sélectionnez le bouton **Ajouter**. Entrez **ManageSend** comme nom de stratégie, assurez-vous que toutes les cases sont cochées, puis sélectionnez **Créer**.

    [![Chaînes de connexion Event Hub](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

    > [!TIP]
    > Vérifiez que vous créez une stratégie SAS pour votre instance de hub d’événements plutôt que pour votre espace de noms.

1. Ouvrez la stratégie **ManageSend** que vous avez créée, puis copiez les valeurs de **Chaîne de connexion--clé primaire** et **Chaîne de connexion--clé secondaire** dans un fichier temporaire. Vous en aurez besoin pour créer un point de terminaison du hub d’événements à la section suivante.

### <a name="create-an-endpoint-for-the-event-hub"></a>Créer un point de terminaison pour le hub d’événements

1. Dans la fenêtre de commande, vérifiez que vous êtes dans le dossier **occupancy-quickstart\src** de l’exemple Azure Digital Twins.

1. Ouvrez le fichier **actions\createEndpoints.yaml** dans votre éditeur. Remplacez le contenu par ce qui suit :

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. Remplacez les espaces réservés `Primary_connection_string_for_your_event_hub` par la valeur de la **Chaîne de connexion--clé primaire** pour le hub d’événements. Assurez-vous que le format de cette chaîne de connexion est le suivant :

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Remplacez les espaces réservés `Secondary_connection_string_for_your_event_hub` par la valeur de la **Chaîne de connexion--clé secondaire** pour le hub d’événements. Assurez-vous que le format de cette chaîne de connexion est le suivant : 

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Remplacez les espaces réservés `Name_of_your_Event_Hub` par le nom de votre hub d’événements.

    > [!IMPORTANT]
    > Entrez toutes les valeurs sans les guillemets. Veillez à laisser au moins un espace après les deux-points dans le fichier YAML. Vous pouvez également valider le contenu de votre fichier YAML par le biais d’un validateur YAML en ligne, comme [cet outil](https://onlineyamltools.com/validate-yaml).

1. Enregistrez et fermez le fichier. Exécutez la commande suivante dans la fenêtre de commande, et connectez-vous avec votre compte Azure lorsque vous y êtes invité.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Il crée deux points de terminaison pour votre hub d’événements.

   [![Points de terminaison pour Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Analyser avec Time Series Insights

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**. 

1. Recherchez et sélectionnez une ressource en disponibilité générale **Time Series Insights**. Sélectionnez **Create** (Créer).

1. Entrez un **nom** pour votre instance Time Series Insights, puis sélectionnez votre **abonnement**. Sélectionnez le **Groupe de ressources** que vous avez utilisé pour votre instance Digital Twins, et votre **Emplacement**. Sélectionnez **Suivant : Source d’événement** ou l’onglet **Source d’événement**.

    [![Sélections pour la création d’une instance Time Series Insights](./media/tutorial-facilities-analyze/create-tsi.png)](./media/tutorial-facilities-analyze/create-tsi.png#lightbox)

1. Sous l’onglet **Source de l’événement**, entrez un **Nom**, sélectionnez **Event Hub** en tant que **Type de source**, puis vérifiez que les autres valeurs sont sélectionnées correctement pour référencer le hub d’événements que vous avez créé. Sélectionnez **ManageSend** pour le **Nom de la stratégie d’accès du hub d’évènements**, puis sélectionnez le groupe de consommateurs que vous avez créé à la section précédente pour le **Groupe de consommateurs du hub d’événements**. Sélectionnez **Revoir + créer**.

    [![Sélections pour la création d’une source d’événement](./media/tutorial-facilities-analyze/tsi-event-source.png)](./media/tutorial-facilities-analyze/tsi-event-source.png#lightbox)

1. Dans le volet **Vérifier + créer**, vérifiez les informations que vous avez entrées et sélectionnez **Créer**.

1. Dans le volet de déploiement, sélectionnez la ressource Time Series Insights que vous venez de créer. Cela ouvre le volet **Vue d’ensemble** pour votre environnement Time Series Insights.

1. Sélectionnez le bouton **Accéder à l’environnement** en haut. Si vous obtenez un avertissement d’accès aux données, ouvrez le volet **Stratégies d’accès aux données** pour votre instance Time Series Insights, sélectionnez **Ajouter**, **Contributeur** en tant que rôle, puis sélectionnez l’utilisateur approprié.

1. Le bouton **Accéder à Environnement** ouvre l’[explorateur Time Series Insights](../time-series-insights/time-series-insights-explorer.md). S’il n’affiche aucun événement, simulez des événements d’appareil en accédant au projet **device-connectivity** de votre exemple Digital Twins, et en exécutant `dotnet run`.

1. Lorsque quelques événements simulés ont été générés, revenez à l’explorateur Time Series Insights et sélectionnez le bouton Actualiser, en haut. Vous devriez voir des graphiques analytiques en cours de création pour vos données de capteur simulées. 

    [![Graphique dans l’explorateur Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer.png)](./media/tutorial-facilities-analyze/tsi-explorer.png#lightbox)

1. Dans l’explorateur Time Series Insights, vous pouvez ensuite générer des graphiques et des cartes thermiques pour des événements et des données venant de vos locaux, de vos capteurs et d’autres ressources. Sur le côté gauche, utilisez les zones de liste déroulante **MESURER** et **DIVISÉ PAR** pour créer vos propres visualisations. 

   Par exemple, sélectionnez **Événements** pour **MESURER**, et **DigitalTwins-SensorHardwareId** pour **DIVISÉ PAR**, afin de générer une carte thermique pour chacun de vos capteurs. La carte thermique doit ressembler à l’image suivante :

   [![Carte thermique dans l’explorateur Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez arrêter votre exploration d’Azure Digital Twins ici, vous pouvez supprimer les ressources créées dans ce tutoriel :

1. Dans le menu de gauche du [portail Azure](https://portal.azure.com), sélectionnez **Toutes les ressources**, puis votre groupe de ressources Digital Twins et **Supprimer**.

    > [!TIP]
    > Si vous avez rencontré des difficultés pour supprimer votre instance de Digital Twins, une mise à jour du service a été déployée avec le correctif. Réessayez de supprimer votre instance.

2. Si nécessaire, supprimez les exemples d’applications de votre machine de travail.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour parfaire vos connaissances sur les graphes d’intelligence spatiale et les modèles objet dans Azure Digital Twins.

> [!div class="nextstepaction"]
> [Comprendre le graphique d’intelligence spatiale et les modèles objets Digital Twins](concepts-objectmodel-spatialgraph.md)
