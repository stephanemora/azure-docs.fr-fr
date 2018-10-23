---
title: Analyser des événements à partir d’une configuration Azure Digital Twins | Microsoft Docs
description: Découvrez comment visualiser et analyser des événements à partir de vos espaces Azure Digital Twins à l’aide d’Azure Time Series Insights, en utilisant les étapes de ce didacticiel.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1366cafe5d2c526e86905c108b9c7b865aac8f69
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323298"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-using-time-series-insights"></a>Didacticiel : Visualiser et analyser des événements à partir de vos espaces Azure Digital Twins à l’aide de Time Series Insights

Après avoir déployé votre instance Azure Digital Twins, approvisionné vos espaces et implémenté une fonction personnalisée visant à surveiller des conditions spécifiques, vous pouvez alors visualiser les événements et les données provenant de vos espaces pour rechercher des tendances et des anomalies. 

Dans [le premier didacticiel](tutorial-facilities-setup.md), vous avez configuré le graphique spatial d’un bâtiment imaginaire, avec une salle contenant des capteurs de mouvement, de dioxyde de carbone et de température. Dans [le deuxième didacticiel](tutorial-facilities-udf.md), vous avez approvisionné votre graphique et une fonction définie par l’utilisateur. La fonction surveille ces valeurs de capteur et déclenche des notifications pour les conditions appropriées, autrement dit, la salle est vide, et les niveaux de température et de dioxyde de carbone sont normales. Ce didacticiel vous montre comment vous pouvez intégrer les notifications et les données provenant de votre configuration Digital Twins avec Azure Time Series Insights. Vous pouvez ensuite visualiser vos valeurs de capteur dans le temps et rechercher des tendances, comme l’identité de la personne utilisant le plus la pièce, les moments de la journée où elle est la plus occupée, et ainsi de suite. Vous pouvez également détecter des anomalies, comme le fait que les salles soient moins bien aérées ou plus chaudes, ou le fait qu’une partie de votre bâtiment envoie des valeurs de température élevées indiquant un climatiseur défectueux.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Diffuser en continu des données à l’aide d’Event Hubs
> * Analyser avec Time Series Insights

## <a name="prerequisites"></a>Prérequis

Ce didacticiel suppose que vous avez [configuré](tutorial-facilities-setup.md) et [approvisionné](tutorial-facilities-udf.md) votre configuration Azure Digital Twins. Avant de poursuivre, assurez-vous que vous avez :
- Un [compte Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une instance de Digital Twins en cours d’exécution.
- Les [exemples Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) téléchargés et extraits sur votre machine de travail.
- [Le kit SDK .NET Core version 2.1.403 ou supérieure](https://www.microsoft.com/net/download) sur votre machine de développement pour exécuter l’exemple. Exécutez `dotnet --version` pour vérifier que la version appropriée est installée. 


## <a name="stream-data-using-event-hubs"></a>Diffuser en continu des données à l’aide d’Event Hubs
Le service [Event Hubs](../event-hubs/event-hubs-about.md) vous permet de créer un pipeline pour diffuser vos données. Cette section vous montre comment créer votre concentrateur d’événements comme le connecteur entre vos instances Digital Twins et TSI.

### <a name="create-an-event-hub"></a>Créer un hub d’événements

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le panneau de navigation de gauche, sélectionnez **Créer une ressource**. 

1. Recherchez et sélectionnez **Event Hubs**. Cliquez sur **Créer**.

1. Entrez un **nom** pour votre espace de noms Event Hubs, choisissez *Standard* **Niveau tarifaire**, votre **Abonnement**, le **Groupe de ressources** que vous avez utilisé pour votre instance Digital Twins et l’**emplacement**. Cliquez sur **Créer**. 

1. Une fois déployé, accédez au *déploiement* de l’espace de noms Event Hubs, puis cliquez sur l’espace de noms sous **RESSOURCES**.

    ![Espace de noms Event Hub](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. Dans le volet **Vue d’ensemble** de l’espace de noms Event Hubs, cliquez sur le bouton **Event Hub** en haut. 
    ![Concentrateur d’événements](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Entrez un **nom** pour votre concentrateur d’événements et cliquez sur **Créer**. Une fois déployé, il apparaît dans le volet **Event Hubs** de l’espace de noms Event Hubs avec un **ÉTAT** *Actif*. Cliquez sur ce concentrateur d’événements pour ouvrir son volet **Vue d’ensemble**.

1. Cliquez sur le bouton **Groupe de consommateurs** en haut et entrez un nom tel que *tsievents* pour le groupe de consommateurs. Cliquez sur **Créer**.
    ![Groupe de consommateurs du concentrateur d’événements](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   Une fois créé, le groupe de consommateurs apparaîtra dans la liste en bas du volet **Vue d’ensemble** du concentrateur d’événements. 

1. Ouvrez le volet **Stratégies d’accès partagé** pour votre concentrateur d’événements, cliquez sur le bouton **Ajouter**. **Créez** une stratégie nommée *ManageSend*et assurez-vous que toutes les cases sont cochées. 

    ![Chaînes de connexion du concentrateur d’événements](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Ouvrez la stratégie *ManageSend* que vous avez créée, copiez les valeurs de **Chaîne de connexion--clé primaire** et **Chaîne de connexion--clé secondaire** dans un fichier temporaire. Vous en aurez besoin pour créer un point de terminaison pour le concentrateur d’événements dans la section suivante.

### <a name="create-endpoint-for-the-event-hub"></a>Créer un point de terminaison pour le concentrateur d’événements

1. Dans la fenêtre de commande, vérifiez que vous êtes dans le dossier **occupation-quickstart\src** de l’exemple Digital Twins.

1. Ouvrez le fichier **_actions\createEndpoints.yaml_** dans votre éditeur. Remplacez le contenu par ce qui suit :

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    ```

1. Remplacez les espaces réservés `Primary_connection_string_for_your_event_hub` par la valeur de la **Chaîne de connexion--clé primaire** pour le concentrateur d’événements. Assurez-vous que le format de cette chaîne de connexion est comme suit :
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
```

1. Remplacez les espaces réservés `Secondary_connection_string_for_your_event_hub` par la valeur de la **Chaîne de connexion--clé secondaire** pour le concentrateur d’événements. Assurez-vous que le format de cette chaîne de connexion est comme suit : 
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
```

1. Remplacez les espaces réservés `Name_of_your_Event_Hubs_namespace` par le nom de votre espace de noms Event Hubs.

    > [!IMPORTANT]
    > Entrez toutes les valeurs sans les guillemets. Veillez à laisser au moins un espace après les deux-points dans le fichier *YAML*. Vous pouvez également valider le contenu de votre fichier *YAML* via un validateur YAML en ligne comme [cet outil](https://onlineyamltools.com/validate-yaml).


1. Enregistrez et fermez le fichier. Exécutez la commande suivante dans la fenêtre de commande, et connectez-vous avec votre compte Azure lorsque vous y êtes invité.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   Il crée deux points de terminaison pour votre concentrateur d’événements.

   ![Points de terminaison pour Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Analyser avec Time Series Insights

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource**. 

1. Recherchez et sélectionnez une nouvelle ressource **Time Series Insights**. Cliquez sur **Créer**.

1. Entrez un **nom** pour votre instance Time Series Insights, puis sélectionnez votre **abonnement**. Sélectionnez le **groupe de ressources** que vous avez utilisé pour votre instance Digital Twins et votre **emplacement**. Cliquez sur **Créer**.

    ![Créer un TSI](./media/tutorial-facilities-analyze/create-tsi.png)

1. Une fois déployé, ouvrez l’environnement Time Series Insights, puis ouvrez son volet **Sources d’événements**. Cliquez sur le bouton **Ajouter** en haut pour ajouter un groupe de consommateurs.

1. Dans le volet **Nouvelle source d’événements**, entrez un **nom**et assurez-vous que les autres valeurs sont correctement sélectionnées. Sélectionnez *ManageSend* comme **nom de la stratégie du concentrateur d’évènements**, puis sélectionnez le *groupe de consommateurs* créé dans la section précédente comme **groupe de consommateurs du concentrateur d’événements**. Cliquez sur **Créer**.

    ![Source de l’événement TSI](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Ouvrez le volet **Vue d’ensemble** pour votre environnement Time Series Insights et cliquez sur le bouton **Accédez à l’environnement** en haut. Si vous obtenez un *avertissement d’accès aux données*, ouvrez le volet **Stratégies d’accès aux données** pour votre instance TSI, cliquez sur **Ajouter**, sélectionnez **Contributeur**en tant que rôle, puis sélectionnez l’utilisateur approprié.

1. Le bouton **Accéder à l’environnement** ouvre [l’explorateur Time Series Insights](../time-series-insights/time-series-insights-explorer.md). S’il n’affiche pas tous les événements, simulez des événements d’appareil en accédant au projet **_device-connectivity_** de votre exemple Digital Twins, et en l’exécutant`dotnet run`.

1. Lorsque quelques événements simulés ont été générés, revenez à l’explorateur Time Series Insights, puis cliquez sur le bouton Actualiser en haut. Vous devriez voir des graphiques analytiques créés pour vos données de capteur simulé. 

    ![Explorateur TSI](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. Dans l’explorateur Time Series, vous pouvez ensuite générer des graphiques et des cartes thermiques pour des événements et des données venant de vos locaux, de vos capteurs et d’autres ressources. Sur le côté gauche, cliquez sur les zones de liste déroulante nommées **MESURE** et **FRACTIONNER PAR** pour créer vos propres visualisations. Par exemple, sélectionnez *Événements* comme **MESURE** et *DigitalTwins-SensorHardwareId* à **FRACTIONNER PAR**, afin de générer une carte thermique pour chacun de vos capteurs, similaire à l’image suivante :

    ![Explorateur TSI](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez arrêter votre découverte d’Azure Digital Twins ici, vous pouvez supprimer les ressources créées dans ce didacticiel :

1. Dans le menu de gauche du [Portail Azure](http://portal.azure.com), cliquez sur **Toutes les ressources**, puis sélectionnez votre groupe de ressources Digital Twins et **supprimez-le**.
2. Si vous le souhaitez, vous pouvez également supprimer les exemples d’applications sur votre machine de travail. 


## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour parfaire vos connaissances sur les graphiques d’intelligence spatiale et les modèles objets dans Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Comprendre le graphique d’intelligence spatiale et les modèles objets Digital Twins](concepts-objectmodel-spatialgraph.md)

