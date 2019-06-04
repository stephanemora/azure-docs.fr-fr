---
title: 'Didacticiel : Créer un environnement Azure Time Series Insights | Microsoft Docs'
description: Découvrez comment créer un environnement Time Series Insights, contenant des données issues d’appareils simulés.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: b8b46db043113f29f559ad44855d19f0d6ca73c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244156"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Didacticiel : Créer un environnement Azure Time Series Insights

Ce tutoriel vous guide tout au long du processus de création d’un environnement Azure Time Series Insights contenant des données issues d’appareils simulés. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez un environnement Time Series Insights.
> * Créer une solution de simulation d’appareil contenant un IoT Hub.
> * Connecter la ressource d’environnement Time Series Insights à l’IoT Hub.
> * Exécuter une simulation d’appareil pour transmettre des données en continu à l’environnement Time Series Insights.
> * Vérifier les données de télémétrie simulées.

## <a name="video"></a>Vidéo

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Découvrez comment utiliser un accélérateur de solution Azure IoT pour générer des données et bien démarrer avec Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).
* Votre compte de connexion Azure doit également être membre du rôle **Propriétaire** de l’abonnement. Pour plus d’informations, consultez [Gérer l’accès à l’aide d’un contrôle d’accès en fonction du rôle et du portail Azure](/azure/role-based-access-control/role-assignments-portal).

## <a name="overview"></a>Vue d'ensemble

L’environnement Time Series Insights désigne l’endroit où les données de l’appareil sont collectées et stockées. Une fois les données stockées, l'[Explorateur Azure Time Series Insights](time-series-quickstart.md) et l'[API de requête Time Series Insights](/rest/api/time-series-insights/ga-query-api) peuvent être utilisés pour interroger et analyser les données. Azure IoT Hub est le point de connexion utilisé par tous les appareils (qu’ils soient simulés ou physiques) pour se connecter en toute sécurité et transmettre des données au cloud Azure. La [vue d’ensemble de Time Series Insights](time-series-insights-overview.md) indique qu’Azure IoT Hub sert également de source d’événement pour la diffusion en continu des données vers un environnement Time Series Insights. Ce tutoriel utilise un [accélérateur de solution IoT](/azure/iot-accelerators/), pour générer et diffuser en streaming des exemples de données de télémétrie à IoT Hub.

>[!TIP]
> Les accélérateurs de solution IoT fournissent des solutions préconfigurées de classe entreprise, qui vous permettent d’accélérer le développement de solutions IoT personnalisées.

## <a name="create-an-environment"></a>Créer un environnement

Commencez par créer un environnement Time Series Insights dans votre abonnement Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre compte d’abonnement Azure. 
1. Cliquez sur **+ Créer une ressource** dans le coin supérieur gauche. 
1. Sélectionnez la catégorie **Internet des objets**, puis **Time Series Insights**. 

   [![Sélectionner la ressource d’environnement Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Sur la page **Environnement Time Series Insights**, renseignez les paramètres requis.

   Paramètre|Description
   ---|---
   **Nom de l’environnement** | Choisissez un nom unique pour l'environnement Time Series Insights. Le nom est utilisé par l’explorateur Time Series Insights et les API de requête.
   **Abonnement** | Les abonnements sont des conteneurs pour les ressources Azure. Choisissez un abonnement pour lequel créer l’environnement Time Series Insights.
   **Groupe de ressources** | Un groupe de ressources est un conteneur pour les ressources Azure. Choisissez un groupe de ressources existant, ou créez-en un pour la ressource d'environnement Time Series Insights.
   **Lieu** | Choisissez une région de centre de données pour votre environnement Time Series Insights. Pour éviter des coûts de bande passante et une latence supplémentaires, maintenez l'environnement Time Series Insights dans la même région que les autres ressources IoT.
   **Référence de tarification** | Choisissez le débit nécessaire. Pour le coût et la capacité de départ les plus faibles, sélectionnez `S1`.
   **Capacité** | La capacité est le multiplicateur qui s’applique à la vitesse d’entrée, à la capacité de stockage et au coût associé à la référence sélectionnée. Vous pouvez changer la capacité après la création. Pour le coût le plus bas, sélectionnez une capacité de 1.

   Une fois terminé, sélectionnez **Créer** pour commencer le processus d’approvisionnement.

   [![Créer une ressource d’environnement Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Vérifiez le panneau **Notifications** pour superviser l’achèvement du déploiement. 

   [![Déploiement de l’environnement Time Series Insights effectué avec succès](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Créer une simulation d’appareil

Ensuite, créez la solution de simulation d’appareil, qui va générer les données de test servant à remplir votre environnement Time Series Insights.

1. Sous un onglet ou dans une fenêtre distinct(e), accédez à [azureiotsolutions.com](https://www.azureiotsolutions.com). Connectez-vous à l’aide du même compte d’abonnement Azure et sélectionnez l’accélérateur **Simulation d’appareil**.

   [![Exécuter l’accélérateur de simulation d’appareil](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Entrez les paramètres requis dans la page **Créer une solution de simulation d’appareil**.

   Paramètre|Description
   ---|---
   **Nom de la solution** | Cette valeur unique sert à créer un groupe de ressources. Les ressources Azure répertoriées sont créées et affectées au groupe de ressources.
   **Abonnement** | Spécifiez le même abonnement que celui utilisé pour la création de votre environnement Time Series Insights, dans la section précédente.
   **Région** | Spécifiez la même région que celle utilisée pour la création de votre environnement Time Series Insights, dans la section précédente.
   **Déployer des ressources Azure facultatives** | Laissez la case **IoT Hub** cochée. Les appareils simulés utilisent cette option pour se connecter ou pour diffuser des données en continu.

   Quand vous avez terminé, sélectionnez **Créer une solution** pour provisionner les ressources Azure de la solution. Ce processus peut prendre six à sept minutes.

   [![Provisionner la solution de simulation d’appareil](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Une fois le provisionnement terminé, le texte situé au-dessus de votre nouvelle solution passe de **En cours d’approvisionnement** à **Prêt**.

   >[!IMPORTANT]
   > Ne sélectionnez pas encore l’option **Lancer** ! Gardez cette page web ouverte car vous aurez besoin d’y revenir plus tard.

   [![Provisionnement de la solution de simulation d’appareil terminé](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Revenez maintenant au portail Azure et vérifiez les nouvelles ressources créées dans votre abonnement. Sur la page **Groupes de ressources** du portail, vous remarquerez qu’un nouveau groupe de ressources a été créé à l’aide du **nom de la solution** indiqué à la dernière étape. Notez également toutes les ressources qui ont été créées pour prendre en charge la solution de simulation d’appareil.

   [![Ressources de la solution de simulation d’appareil](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-environment-to-the-iot-hub"></a>Connecter l’environnement au hub IoT

À ce stade, vous avez appris à créer deux jeux de ressources, chacun dans son propre groupe de ressources :

- Un environnement Time Series Insights vide.
- Les ressources de la solution de simulation d’appareil, y compris un IoT Hub, générées par un accélérateur de solution.

Rappelez-vous que les appareils simulés doivent se connecter à un IoT Hub pour transmettre en continu les données de l’appareil. Pour transmettre les données à l’environnement Time Series Insights, vous devez apporter des modifications à la configuration de votre IoT Hub et de votre environnement Time Series Insights.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Configuration de l’IoT Hub : Définir un groupe de consommateurs

IoT Hub fournit différents points de terminaison pour partager une fonctionnalité avec d’autres intervenants. Le point de terminaison « Événements » permet aux autres applications de consommer des données, car il est transmis en continu à une instance IoT Hub. Plus précisément, les « groupes de consommateurs » fournissent un mécanisme permettant aux applications d’écouter et d’extraire des données à partir de l’IoT Hub.

Définissez ensuite une nouvelle propriété **Groupe de consommateurs** sur le **point de terminaison Événements** de l’IoT Hub de la solution de simulation d’appareil.

1. Dans le portail Azure, accédez à la page **Vue d’ensemble** du groupe de ressources que vous avez créé pour la solution de simulation d’appareil. Sélectionnez la ressource IoT Hub.

   [![Groupe de ressources de la solution de simulation d’appareil](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Notez le **nom** de la ressource IoT Hub générée pour la solution. Vous en aurez à nouveau besoin ultérieurement.

1. Faites défiler vers le bas et sélectionnez la page **Points de terminaison**, puis sélectionnez le point de terminaison **Événements**. Sur la page **Propriétés** du point de terminaison, entrez un nom unique pour votre point de terminaison sous le groupe de consommateurs « $Default ». Sélectionnez **Enregistrer**.

   [![Points de terminaison IoT Hub de la solution de simulation d’appareil](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="environment-configuration-define-an-event-source"></a>Configuration de l’environnement : définir une source d’événement

Connectez maintenant le nouveau point de terminaison d’événement **groupe de consommateurs** de l’IoT Hub à l’environnement Time Series Insights, en tant que **source d’événement**.

1. Accédez à la page **Vue d’ensemble** du groupe de ressources que vous avez créé pour l’environnement Time Series Insights. Sélectionnez l’environnement Time Series Insights.

   [![Groupe de ressources et environnement Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. Dans la page de l’environnement Time Series Insights, sélectionnez **Sources d’événements**. Sélectionnez ensuite **+ Ajouter**.

   [![Vue d'ensemble de l'environnement Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Entrez les paramètres requis dans la page **Nouvelle source d’événement**.

   Paramètre|Description
   ---|---
   **Nom de la source d’événement** | Requiert une valeur unique, utilisée pour nommer la source d’événement.
   **Source** | Sélectionnez **Hub IoT**.
   **Option d’importation** | Sélectionnez l’option par défaut `Use IoT hub from available subscriptions`. Cette option a pour effet de renseigner la prochaine liste déroulante avec les abonnements disponibles.
   **Abonnement** | Sélectionnez le même abonnement que celui dans lequel vous avez créé l’environnement Time Series Insights et les ressources de la simulation d’appareil.
   **Nom de l’IoT Hub** | Doit être défini par défaut sur le nom de l’IoT Hub que vous avez noté précédemment. Si ce n’est pas le cas, sélectionnez l’IoT Hub approprié.
   **Nom de la stratégie IoT Hub** | sélectionnez **iothubowner**.
   **Groupe de consommateurs IoT Hub** | Doit être défini par défaut sur le nom du groupe de consommateurs IoT Hub que vous avez créé précédemment. Si ce n’est pas le cas, sélectionnez le nom de groupe de consommateurs approprié.
   **Format de sérialisation de l’événement** | Conservez la valeur par défaut de `JSON`.
   **Nom de la propriété d’horodatage** | Spécifiez `timestamp`.

   Lorsque vous avez terminé, sélectionnez **Créer** pour ajouter la source de l'événement. Lorsque vous revenez sur la page **Vue d’ensemble** du groupe de ressources, avec la ressource de votre environnement Time Series Insights, vous pouvez maintenant voir une nouvelle ressource « Source de l’événement Time Series Insights ».

   [![Nouvelle source d'événements de l'environnement Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Exécuter une simulation d’appareil pour diffuser des données en contenu

Maintenant que la configuration est terminée, il est temps de renseigner l’environnement Time Series Insights avec les échantillons de données issues des appareils simulés.

Comme nous l’avons vu dans la [section Créer une simulation d’appareil](#create-a-device-simulation), plusieurs ressources Azure ont été créées par l’accélérateur afin de prendre en charge la solution. En même temps que IoT Hub, une application web Azure App Service a été générée pour créer et transmettre les données de télémétrie des appareils simulés.

1. Revenez à votre [tableau de bord Accélérateurs de solution](https://www.azureiotsolutions.com/Accelerators#dashboard). Connectez-vous à nouveau si nécessaire, à l’aide du même compte Azure que celui utilisé dans ce didacticiel. Vous pouvez maintenant sélectionner **Lancer** sous votre solution « Simulation d’appareil ».

     [![Tableau de bord des accélérateurs de solution](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. L’application web de simulation d’appareil va maintenant démarrer. Le chargement initial peut prendre quelques instants. Vous êtes également invité à accorder à l’application web l’autorisation « Vous connecter et lire votre profil ». Cette autorisation permet à l’application de récupérer les informations des profils utilisateur dont l’application a besoin pour fonctionner.

     [![Autorisation de l’application web de simulation d’appareil](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Une fois la page **Configuration de la simulation** chargée, entrez les paramètres requis.

   Paramètre|Description
   ---|---
   **IoT Hub cible** | Sélectionnez **Utiliser IoT Hub préapprovisionné**.
   **Modèle de l’appareil** | Sélectionnez **Chiller**.
   **Nombre d’appareils**  | Entrez `1000` sous **Quantité**.
   **Fréquence de télémétrie** | Entrez `10` secondes.
   **Durée de la simulation** | Sélectionnez **Se termine dans :** et entrez `5` minutes.

   Quand vous avez terminé, sélectionnez **Démarrer la simulation**. La simulation s’exécute pendant 5 minutes. Elle génère toutes les 10 secondes des données provenant de 1 000 appareils simulés. 

   [![Configuration de la simulation d’appareil](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Pendant l’exécution de la simulation, vous remarquerez que les champs **Nombre total de messages** et **Messages par seconde** sont mis à jour, toutes les 10 secondes environ. La simulation se termine après environ 5 minutes. Vous revenez alors à **Configuration de la simulation**.

   [![Exécution de la simulation d’appareil](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Vérifier les données de télémétrie

Dans cette dernière section, vous allez vérifier que les données de télémétrie ont bien été générées et stockées dans l’environnement Time Series Insights. Pour vérifier les données, vous utilisez l’explorateur Time Series Insights, qui sert à interroger et analyser les données de télémétrie.

1. Revenez à la page **Vue d’ensemble** du groupe de ressources de l’environnement Time Series Insights. Sélectionnez l’environnement Time Series Insights.

   [![Groupe de ressources et environnement Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Dans la page **Vue d’ensemble** de l’environnement Time Series Insights, sélectionnez l’**URL de l’explorateur Time Series Insights** pour ouvrir l’explorateur Time Series Insights.

   [![Explorateur Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. L’explorateur Time Series Insights se charge et s’authentifie à l’aide de votre compte de portail Azure. Dans la vue initiale, vous pouvez voir dans la zone de graphique que l’environnement Explorateur Time Series Insights a été renseigné avec les données de télémétrie simulées. Pour filtrer sur une période plus restreinte, sélectionnez la liste déroulante en haut à gauche. Saisissez une période suffisamment longue pour couvrir la durée de la simulation d’appareil. Sélectionnez ensuite la loupe de recherche.

   [![Filtre d'intervalle de temps de l’explorateur Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Restreindre la période de recherche permet d’identifier les différentes rafales de transfert de données, l’IoT Hub et l’environnement Time Series Insights. Notez également le message **Diffusion en continu terminée** en haut à droite, qui indique le nombre total d’événements identifiés. Vous pouvez également faire glisser le curseur **Taille de l’intervalle** afin d’ajuster la précision du tracé sur le graphique.

   [![Vue filtrée d'intervalle de temps de l’explorateur Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Pour finir, vous pouvez aussi cliquer sur une région pour filtrer une période. Effectuez ensuite un clic droit et utilisez **Explorer les événements** pour afficher les détails des événements dans le tableau **Événements**.

   [![Vue filtrée d'intervalle de temps et événements de l’explorateur Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Supprimer des ressources

Ce tutoriel crée plusieurs services Azure en cours d’exécution, pour prendre en charge l’environnement Time Series Insights et la solution de simulation d’appareil. Si vous souhaitez arrêter ou reporter cette série de tutoriels, supprimez toutes les ressources afin d’éviter des frais inutiles.

Dans le menu de gauche du portail Azure :

1. Sélectionnez l’icône **Groupes de ressources**. Puis choisissez le groupe de ressources que vous avez créé pour l’environnement Time Series Insights. En haut de la page, sélectionnez **Supprimer le groupe de ressources**, entrez le nom du groupe de ressources, puis choisissez **Supprimer**.

1. Sélectionnez l’icône **Groupes de ressources**. Puis choisissez le groupe de ressources créé par l’accélérateur de la solution de simulation d’appareil. En haut de la page, sélectionnez **Supprimer le groupe de ressources**, entrez le nom du groupe de ressources, puis choisissez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créez un environnement Time Series Insights.
> * Créer une solution de simulation d’appareil contenant un IoT Hub.
> * Connecter la ressource d’environnement Time Series Insights à l’IoT Hub.
> * Exécuter une simulation d’appareil pour transmettre des données en continu à l’environnement Time Series Insights.
> * Vérifier les données de télémétrie simulées.

Maintenant que vous savez comment créer votre propre environnement Time Series Insights, apprenez à créer une application web qui utilise des données à partir d’un environnement Time Series Insights :

> [!div class="nextstepaction"]
> [Créer une application web monopage Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)
