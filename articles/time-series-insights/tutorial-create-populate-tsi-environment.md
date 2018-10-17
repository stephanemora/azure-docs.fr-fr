---
title: Créer un environnement Azure Time Series Insights
description: Découvrez comment créer un environnement Time Series Insights, contenant des données issues d’appareils simulés.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/04/2018
ms.author: anshan
manager: cshankar
ms.openlocfilehash: 46fecf6f1b2826f0e23b7b6c4f4511ce9a662d1c
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365687"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Didacticiel : Créer un environnement Azure Time Series Insights

Ce didacticiel vous guidera tout au long du processus de création d’un environnement Time Series Insight (TSI) contenant des données issues d’appareils simulés. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un environnement TSI 
> * Créer une solution de simulation d’appareil contenant un IoT Hub
> * Connecter l’environnement TSI à l’IoT Hub
> * Exécuter une simulation d’appareil pour transmettre des données en continu à l’environnement TSI
> * Vérifier les données de télémétrie simulées

## <a name="video"></a>Vidéo : 

### <a name="in-this-video-we-show-you-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-that-can-be-used-to-get-started-with-time-series-insightsbr"></a>Dans cette vidéo, nous vous montrons comment utiliser un accélérateur de solution Azure IoT pour générer des données qui peuvent être utilisées pour démarrer avec Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. 

Votre compte de connexion Azure doit également être membre du rôle Propriétaire de l’abonnement. Pour plus d’informations, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](/azure/role-based-access-control/role-assignments-portal)

## <a name="overview"></a>Vue d’ensemble

L’environnement TSI désigne l’endroit où les données de l’appareil sont collectées et stockées. Une fois stockées dans l’environnement TSI, vous pouvez utiliser l’[Explorateur TSI](time-series-quickstart.md) et [l’API de requête TSI](/rest/api/time-series-insights/time-series-insights-reference-queryapi) pour interroger et analyser les données.

Comme tous les appareils, qu’ils soient simulés ou physiques, IoT Hub est le point de connexion utilisé par les appareils pour se connecter en toute sécurité et transmettre des données au cloud Azure. Comme indiqué dans la [Vue d’ensemble de TSI](time-series-insights-overview.md), IoT Hub sert également de source d’événement, pour la transmission en continu des données vers l’environnement TSI. 

Ce didacticiel utilise également un [accélérateur de solution IoT](/azure/iot-accelerators/), pour générer et transmettre en continu des exemples de données de télémétrie à IoT Hub. Les accélérateurs de solution IoT fournissent des solutions préconfigurées de classe entreprise, qui vous permettent d’accélérer le développement de solutions IoT personnalisées. 

## <a name="create-a-tsi-environment"></a>Créer un environnement TSI

Commencez d’abord par créer un environnement TSI dans votre abonnement Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre compte d’abonnement Azure.  
2. Cliquez sur **+ Créer une ressource** dans le coin supérieur gauche.  
3. Sélectionnez la catégorie **Internet des objets**, puis **Time Series Insights**.  
   
   [![Sélectionner la ressource d’environnement Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

4. Sur la page **Environnement Time Series Insights**, renseignez les paramètres requis :
   
   Paramètre|Description
   ---|---
   **Nom de l’environnement** | Choisissez un nom unique pour l’environnement TSI. Le nom est utilisé par l’Explorateur TSI et l’API de requête.
   **Abonnement** | Les abonnements sont des conteneurs pour les ressources Azure. Choisissez l’abonnement dans lequel vous souhaitez créer l’environnement TSI.
   **Groupe de ressources** | Un groupe de ressources est un conteneur pour les ressources Azure. Choisissez un groupe de ressources existant ou créez-en un pour la ressource d’environnement TSI.
   **Lieu** | Choisissez une région de centre de données pour votre environnement TSI. Pour éviter les coûts supplémentaires de bande passante et la latence, il est préférable de conserver l’environnement TSI dans la même région que les autres ressources IoT.
   **Référence de tarification** | Choisissez le débit nécessaire. Pour le coût le plus bas et une capacité de démarrage, sélectionnez S1.
   **Capacité** | La capacité est le multiplicateur qui s’applique à la vitesse d’entrée, à la capacité de stockage et au coût associé à la référence sélectionnée.  Vous pouvez modifier la capacité d’un environnement après sa création. Pour le coût le plus bas, sélectionnez une capacité de 1. 

   Une fois terminé, cliquez sur **Créer** pour commencer le processus d’approvisionnement.

   ![Créer une ressource d’environnement Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)

5. Vous pouvez vérifier le panneau **Notifications** afin de suivre l’avancée du déploiement, qui devrait prendre moins d’une minute :  

   ![Déploiement de l’environnement Time Series Insights effectué avec succès](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)

## <a name="create-a-device-simulation"></a>Créer une simulation d’appareil

Ensuite, créez la solution de simulation d’appareil, qui va générer les données de test servant à remplir votre environnement TSI :

1. Dans une fenêtre/un onglet distinct, accédez à https://www.azureiotsolutions.com, connectez-vous à l’aide du même compte d’abonnement Azure et sélectionnez l’accélérateur « Simulation d’appareil » :

   ![Exécuter l’accélérateur de simulation d’appareil](media/tutorial-create-populate-tsi-environment/sa-main.png)

2. Entrez les paramètres requis sur la page **Créer une solution de simulation d’appareil** :

   Paramètre|Description
   ---|---
   **Nom de la solution** | Valeur unique, utilisée pour la création d’un nouveau groupe de ressources. Les ressources Azure répertoriées sont créées et affectées au groupe de ressources.
   **Abonnement** | Spécifiez le même abonnement que celui utilisé pour la création de votre environnement TSI, dans la section précédente.
   **Région** | Spécifiez la même région que celle utilisée pour la création de votre environnement TSI, dans la section précédente. 
   **Déployer des ressources Azure facultatives** | Ne décochez pas l’option **IoT Hub**, car les appareils simulés l’utiliseront pour se connecter/transmettre des données en continu.

   Lorsque vous avez terminé, cliquez sur **Créer une solution** pour approvisionner les ressources Azure de la solution, ce qui devrait prendre environ 6 à 7 minutes :

   ![Approvisionner la solution de simulation d’appareil](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)

3. Une fois l’approvisionnement terminé, le texte situé au-dessus de votre nouvelle solution passe de « En cours d’approvisionnement... » à « Prêt » :

   >[!IMPORTANT]
   > Ne cliquez pas encore sur le bouton **Lancer** ! Gardez cette page web ouverte car vous aurez besoin d’y revenir plus tard.

   ![Approvisionnement de la solution de simulation d’appareil terminé](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)

4. Revenez maintenant au portail Azure et vérifiez les nouvelles ressources créées dans votre abonnement. Sur la page **Groupes de ressources** du portail, vous remarquerez qu’un nouveau groupe de ressources a été créé à l’aide du **nom de la solution** indiqué à la dernière étape. Notez également toutes les ressources qui ont été créées pour prendre en charge la solution de simulation d’appareil :

   [![Ressources de la solution de simulation d’appareil](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Connecter l’environnement TSI à l’IoT Hub 

À ce stade, vous avez appris à créer deux jeux de ressources, chacun dans son propre groupe de ressources : 
- Un environnement TSI vide 
- Les ressources de la solution de simulation d’appareil, y compris un IoT Hub, générées par un accélérateur de solution

Rappelez-vous que les appareils simulés doivent se connecter à un IoT Hub pour transmettre en continu les données de l’appareil. Pour transmettre les données à l’environnement TSI, vous devez apporter des modifications à la configuration de votre IoT Hub et de votre environnement TSI. 

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Configuration de l’IoT Hub : définir un groupe de consommateurs

IoT Hub fournit différents points de terminaison pour partager une fonctionnalité avec d’autres intervenants. Le point de terminaison « Événements » permet aux autres applications de consommer des données, car il est transmis en continu à une instance IoT Hub. Plus précisément, les « groupes de consommateurs » fournissent un mécanisme permettant aux applications d’écouter et d’extraire des données à partir de l’IoT Hub. 

Définissez ensuite une nouvelle propriété « Groupe de consommateurs », sur le point de terminaison « Événements » de l’IoT Hub de la solution de simulation d’appareil. 

1. Dans le portail Azure, accédez à la page **Vue d’ensemble** du groupe de ressources que vous avez créé pour la solution de simulation d’appareil, puis sélectionnez la ressource IoT Hub :

   [![Groupe de ressources de la solution de simulation d’appareil](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Notez également le **nom** de la ressource IoT Hub générée pour la solution, car vous en aurez à nouveau besoin ultérieurement.

2. Faites défiler vers le bas et sélectionnez la page **Points de terminaison**, puis sélectionnez le point de terminaison **Événements**. Sur la page **Propriétés** du point de terminaison, entrez un nom unique pour votre point de terminaison sous le groupe de consommateurs « $Default », puis cliquez sur **Enregistrer** :

   [![Points de terminaison IoT Hub de la solution de simulation d’appareil](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-environment-configuration-define-an-event-source"></a>Configuration de l’environnement TSI : définir une source d’événement

Connectez maintenant le nouveau point de terminaison d’événement « groupe de consommateurs » de l’IoT Hub à l’environnement TSI, en tant que « source d’événement ».

1. Accédez à la page **Vue d’ensemble** du groupe de ressources que vous avez créé pour l’environnement TSI, puis sélectionnez l’environnement TSI :

   [![Environnement et groupe de ressources de l’environnement TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

2. Sur la page de l’environnement TSI, sélectionnez **Sources d’événements**, puis cliquez sur **+ Ajouter** :

   ![Vue d’ensemble de l’environnement TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)

3. Saisissez les paramètres requis sur la page **Nouvelle source d’événement** : 

   Paramètre|Description
   ---|---
   **Nom de la source d’événement** | Requiert une valeur unique, utilisée pour nommer la source d’événement.
   **Source** | Sélectionnez « IoT Hub ».
   **Option d’importation** | Utilisez l’option par défaut « Utiliser IoT Hub à partir des abonnements disponibles ». Cette option a pour effet de renseigner la prochaine liste déroulante avec les abonnements disponibles.
   **Abonnement** | Sélectionnez le même abonnement que celui dans lequel vous avez créé l’environnement TSI et les ressources de la simulation d’appareil.
   **Nom de l’IoT Hub** | Doit être défini par défaut sur le nom de l’IoT Hub que vous avez noté précédemment. Si ce n’est pas le cas, sélectionnez l’IoT Hub approprié.
   **Nom de la stratégie IoT Hub** | Conservez la valeur par défaut de « iothubowner ».
   **Groupe de consommateurs IoT Hub** | Doit être défini par défaut sur le nom du groupe de consommateurs IoT Hub que vous avez créé précédemment. Si ce n’est pas le cas, sélectionnez le nom de groupe de consommateurs approprié. 
   **Format de sérialisation de l’événement** | Conservez la valeur par défaut de « JSON ».
   **Nom de la propriété d’horodatage** | Spécifiez la valeur « timestamp ».

   Une fois terminé, cliquez sur **Créer** pour ajouter la source d’événement. Lorsque vous revenez sur la page **Vue d’ensemble** du groupe de ressources, avec la ressource de votre environnement TSI, vous pouvez maintenant voir une nouvelle ressource « Source de l’événement Time Series Insights ».

   ![Nouvelle source d’événement de l’environnement TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)

## <a name="run-a-device-simulation-to-stream-data-into-the-tsi-environment"></a>Exécuter une simulation d’appareil pour transmettre des données en continu à l’environnement TSI

Maintenant que la configuration est terminée, il est temps de renseigner l’environnement TSI avec les échantillons de données issues des appareils simulés.

Comme nous l’avons vu dans la [section Créer une simulation d’appareil](#create-a-device-simulation), plusieurs ressources Azure ont été créées par l’accélérateur afin de prendre en charge la solution. En même temps que IoT Hub, une application web Azure App Service a été générée pour créer et transmettre les données de télémétrie des appareils simulés.

1. Revenez à votre [tableau de bord Accélérateurs de solution](https://www.azureiotsolutions.com/Accelerators#dashboard). Connectez-vous à nouveau si nécessaire, à l’aide du même compte Azure que celui utilisé dans ce didacticiel. Vous pouvez maintenant cliquer sur le bouton **Lancer** sous votre solution « Simulation d’appareil » :

     ![Tableau de bord des accélérateurs de solution](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)

2. L’application web de simulation d’appareil va maintenant démarrer. Le chargement initial peut prendre quelques instants. Vous êtes également invité à accorder à l’application web l’autorisation « Vous connecter et lire votre profil ». Cette autorisation permet à l’application de récupérer les informations des profils utilisateur dont l’application a besoin pour fonctionner :

     ![Autorisation de l’application web de simulation d’appareil](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)

3. Une fois que la page **Configuration de la simulation** est chargée, entrez les paramètres requis : 

   Paramètre|Description
   ---|---
   **IoT Hub cible** | Sélectionnez « Utiliser IoT Hub préapprovisionné ».
   **Modèle de l’appareil** | Sélectionnez « Refroidissement ».
   **Nombre d’appareils**  | Entrez 1000 sous **Quantité**.
   **Fréquence de télémétrie** | Saisissez 10 secondes.
   **Durée de la simulation** | Sélectionnez **Se termine dans :** et entrez 5 minutes.

   Une fois terminé, cliquez sur **Commencer la simulation**. La simulation s’exécute pendant 5 minutes et génère des données à partir de 1000 appareils simulés, toutes les 10 secondes.  

   ![Configuration de la simulation d’appareil](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)

4. Pendant l’exécution de la simulation, vous remarquerez que les champs **Nombre total de messages** et **Messages par seconde** sont mis à jour, toutes les 10 secondes environ. La simulation se termine après environ 5 minutes. Vous revenez alors à **Configuration de la simulation**.

   ![Exécution de la simulation d’appareil](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)

## <a name="verify-the-telemetry-data"></a>Vérifier les données de télémétrie

Dans cette dernière section, vous allez vérifier que les données de télémétrie ont bien été générées et stockées dans l’environnement TSI. Pour vérifier les données, vous utilisez l’explorateur Time Series Insights, qui sert à interroger et analyser les données de télémétrie.

1. Revenez à la page **Vue d’ensemble** du groupe de ressources de l’environnement TSI, puis sélectionnez à nouveau l’environnement TSI :

   [![Environnement et groupe de ressources de l’environnement TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

2. Sur la page **Vue d’ensemble** de l’environnement TSI, cliquez sur l’**URL de l’explorateur Time Series Insights** pour ouvrir l’explorateur TSI :

   [![Explorateur TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

3. L’explorateur TSI va se charger et s’authentifier à l’aide de votre compte de portail Azure. Dans la vue initiale, vous pouvez voir dans la zone de graphique que l’environnement TSI a bien été renseigné avec les données de télémétrie simulées. Pour filtrer sur une période plus restreinte, sélectionnez la liste déroulante en haut à gauche. Saisissez ensuite une période suffisamment longue pour couvrir la durée de la simulation d’appareil. Cliquez ensuite sur l’icône de recherche en forme de loupe :

   [![Filtre d’intervalle de temps de l’explorateur TSI](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

4. Restreindre la période de recherche permet d’identifier les différentes rafales de transfert de données, l’IoT Hub et l’environnement TSI. Notez également le message **Diffusion en continu terminée** en haut à droite, qui indique le nombre total d’événements identifiés. Vous pouvez également faire glisser le curseur **Taille de l’intervalle** afin d’ajuster la granularité du tracé sur le graphique :

   [![Vue filtrée d’intervalle de temps de l’explorateur TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

5. Enfin, vous pouvez également cliquer sur une région pour filtrer une période, puis effectuer un clic droit et utiliser **Explorer les événements** pour afficher les détails des événements dans le tableau **Événements** :

   [![Vue filtrée d’intervalle de temps de l’explorateur TSI et événements](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Supprimer les ressources

Ce didacticiel crée plusieurs services Azure en cours d’exécution, pour prendre en charge l’environnement TSI et la solution de simulation d’appareil. Si vous souhaitez abandonner et/ou repousser la fin de cette série de didacticiels, nous vous recommandons de supprimer toutes les ressources pour éviter des coûts inutiles. 

Dans le menu gauche du portail Azure :

1. Cliquez sur l’icône **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé pour l’environnement TSI. En haut de la page, cliquez sur **Supprimer le groupe de ressources**, saisissez le nom du groupe de ressources, puis cliquez sur **Supprimer**. 
2. Cliquez sur l’icône **Groupes de ressources**, puis sélectionnez le groupe de ressources qui a été créé par l’accélérateur de la solution de simulation d’appareil. En haut de la page, cliquez sur **Supprimer le groupe de ressources**, saisissez le nom du groupe de ressources, puis cliquez sur **Supprimer**. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un environnement TSI 
> * Créer une solution de simulation d’appareil contenant un IoT Hub
> * Connecter l’environnement TSI à l’IoT Hub
> * Exécuter une simulation d’appareil pour transmettre des données en continu à l’environnement TSI
> * Vérifier les données de télémétrie simulées

Maintenant que vous savez comment créer votre propre environnement TSI, apprenez à créer une application web qui utilise des données à partir d’un environnement TSI :

> [!div class="nextstepaction"]
> [Créer une application web monopage Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)


