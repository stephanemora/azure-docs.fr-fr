---
title: 'Didacticiel : Créer un environnement - Azure Time Series Insights | Microsoft Docs'
description: Découvrez comment créer un environnement Time Series Insights, contenant des données issues d’appareils simulés.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 12/04/2019
ms.custom: seodec18
ms.openlocfilehash: 387f3df7313c26c5cd8f7fee7c9b58ec3b5c4552
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872407"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Didacticiel : Créer un environnement Azure Time Series Insights

Ce tutoriel vous guide tout au long du processus de création d’un environnement Azure Time Series Insights contenant des données issues d’appareils simulés. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez un environnement Time Series Insights.
> * Créer une solution de simulation d’appareil contenant un IoT Hub.
> * Connecter la ressource d’environnement Time Series Insights à l’IoT Hub.
> * Exécuter une simulation d’appareil pour transmettre des données en continu à l’environnement Time Series Insights.
> * Vérifier les données de télémétrie simulées.

> [!IMPORTANT]
> Souscrivez un [abonnement Azure gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.

## <a name="prerequisites"></a>Prérequis

* Votre compte de connexion Azure doit également être membre du rôle **Propriétaire** de l’abonnement. Pour plus d’informations, consultez [Gérer l’accès à l’aide d’un contrôle d’accès en fonction du rôle et du portail Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Regarder la vidéo

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Découvrez comment utiliser un accélérateur de solution Azure IoT pour générer des données et bien démarrer avec Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Vue d'ensemble

L’environnement Time Series Insights désigne l’endroit où les données de l’appareil sont collectées et stockées. Une fois les données stockées, l'[Explorateur Azure Time Series Insights](time-series-quickstart.md) et l'[API de requête Time Series Insights](/rest/api/time-series-insights/ga-query-api) peuvent être utilisés pour interroger et analyser les données.

Azure IoT Hub est la source d’événements qui est utilisée par tous les appareils (simulés ou physiques) dans le didacticiel pour vous connecter et transmettre des données en toute sécurité à votre cloud Azure.

Ce didacticiel utilise également un [accélérateur de solution IoT](https://www.azureiotsolutions.com), pour générer et transmettre en continu des exemples de données de télémétrie à IoT Hub.

>[!TIP]
> [Les accélérateurs de solution IoT](https://www.azureiotsolutions.com) fournissent des solutions préconfigurées de classe entreprise, qui vous permettent d’accélérer le développement de solutions IoT personnalisées.

## <a name="create-a-device-simulation"></a>Créer une simulation d’appareil

D’abord, créez la solution de simulation d’appareil, qui va générer les données de test servant à remplir votre environnement Time Series Insights.

1. Sous un onglet ou dans une fenêtre distinct(e), accédez à [azureiotsolutions.com](https://www.azureiotsolutions.com). Connectez-vous à l’aide du même compte d’abonnement Azure et sélectionnez l’accélérateur **Simulation d’appareil**.

   [![Exécuter l’accélérateur de simulation d’appareil](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1.  Sélectionnez **Essayer maintenant**. Entrez ensuite les paramètres obligatoires dans la page **Créer une solution de simulation d’appareil**.

   Paramètre|Description
   ---|---
   **Nom du déploiement** | Cette valeur unique sert à créer un groupe de ressources. Les ressources Azure répertoriées sont créées et affectées au groupe de ressources.
   **Abonnement Azure** | Spécifiez le même abonnement que celui utilisé pour la création de votre environnement Time Series Insights, dans la section précédente.
   **Options de déploiement** | Sélectionnez **Approvisionner nouveau IoT Hub** pour créer un nouvel IoT hub spécifique pour ce didacticiel.
   **Emplacement Azure** | Spécifiez la même région que celle utilisée pour la création de votre environnement Time Series Insights, dans la section précédente.

   Quand vous avez terminé, sélectionnez **Créer** pour provisionner les ressources Azure de la solution. Ce processus peut prendre jusqu’à 20 minutes.

   [![Provisionner la solution de simulation d’appareil](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Une fois le provisionnement terminé, deux mises à jour s’affichent pour indiquer que l’état du déploiement est passé de **Provisionnement** à **Prêt**. 

   >[!IMPORTANT]
   > N’entrez pas encore votre accélérateur de solution ! Gardez cette page web ouverte car vous aurez besoin d’y revenir plus tard.

   [![Provisionnement de la solution de simulation d’appareil terminé](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Observons à présent les ressources nouvellement créées dans le portail Azure. Sur la page **Groupes de ressources**, vous remarquerez qu’un nouveau groupe de ressources a été créé à l’aide du **nom de la solution** indiqué à la dernière étape. Prenez note des ressources qui ont été créées pour la simulation d’appareil.

   [![Ressources de la solution d’appareil](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Créer un environnement

Ensuite, créez un environnement Time Series Insights dans votre abonnement Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre compte d’abonnement Azure. 
1. Cliquez sur **+ Créer une ressource** dans le coin supérieur gauche. 
1. Sélectionnez la catégorie **Internet des objets**, puis **Time Series Insights**. 

   [![Sélectionner la ressource d’environnement Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Sur la page **Environnement Time Series Insights**, renseignez les paramètres requis.

   Paramètre|Description
   ---|---
   **Nom de l’environnement** | Choisissez un nom unique pour l'environnement Time Series Insights. Le nom est utilisé par l’explorateur Time Series Insights et les [API de requête](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Abonnement** | Les abonnements sont des conteneurs pour les ressources Azure. Choisissez un abonnement pour lequel créer l’environnement Time Series Insights.
   **Groupe de ressources** | Un groupe de ressources est un conteneur pour les ressources Azure. Choisissez un groupe de ressources existant, ou créez-en un pour la ressource d'environnement Time Series Insights.
   **Lieu** | Choisissez une région de centre de données pour votre environnement Time Series Insights. Pour éviter toute latence supplémentaire, créez l'environnement Time Series Insights dans la même région que les autres ressources IoT.
   **Niveau** | Choisissez le débit nécessaire. Sélectionnez **S1**.
   **Capacité** | La capacité est le multiplicateur qui s’applique à la vitesse d’entrée et à la capacité de stockage associées à la référence sélectionnée. Vous pouvez changer la capacité après la création. Sélectionnez une capacité de **1**.

   Quand vous avez terminé, sélectionnez **Suivant : Source de l’événement** pour passer à l’étape suivante.

   [![Créer une ressource d’environnement Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Connectez-vous maintenant à l’environnement Time Series Insights sur l’IoT hub créé par l’accélérateur de solution. Définissez **Sélectionner un hub** sur `Select existing`. Ensuite, choisissez l’IoT hub créé par l’accélérateur de solution lors de la définition du **Nom d’IoT Hub**.

   [![Connecter la ressource d’environnement Time Series Insights à l’IoT Hub créé](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Enfin, sélectionnez **Vérifier + créer**.

1. Vérifiez le panneau **Notifications** pour superviser l’achèvement du déploiement. 

   [![Déploiement de l’environnement Time Series Insights effectué avec succès](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Exécuter la simulation d’appareil

Maintenant que le déploiement et la configuration initiale sont terminés, remplissez l’environnement Time Series Insights avec les exemples de données des [appareils simulés créés par l’accélérateur](#create-a-device-simulation).

En même temps que IoT Hub, une application web Azure App Service a été générée pour créer et transmettre les données de télémétrie des appareils simulés.

1. Revenez à votre [tableau de bord Accélérateurs de solution](https://www.azureiotsolutions.com/Accelerators#dashboard). Connectez-vous à nouveau si nécessaire, à l’aide du même compte Azure que celui utilisé dans ce didacticiel. Sélectionnez votre « solution d’appareil », puis **accédez à votre accélérateur de solution** pour lancer votre solution déployée.

   [![Tableau de bord des accélérateurs de solution](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. L’application web de simulation d’appareil commence par vous inviter à lui accorder l’autorisation **Vous connecter et lire votre profil**. Cette autorisation permet à l’application de récupérer les informations des profils utilisateur dont l’application a besoin pour fonctionner.

   [![Autorisation de l’application web de simulation d’appareil](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Sélectionnez **+ Nouvelle simulation**. Une fois la page **Configuration de la simulation** chargée, entrez les paramètres requis.

   Paramètre|Description
   ---|---
   **IoT Hub cible** | Sélectionnez **Utiliser IoT Hub préapprovisionné**.
   **Modèle de l’appareil** | Sélectionnez **Chiller**.
   **Nombre d’appareils**  | Entrez `10` sous **Quantité**.
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

Ce tutoriel crée plusieurs services Azure en cours d’exécution, pour prendre en charge l’environnement Time Series Insights et la solution de simulation d’appareil. Pour les supprimer, revenez au portail Azure.

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
> [Consultez les exemples hébergés de visualisations du SDK client](https://tsiclientsample.azurewebsites.net/)
