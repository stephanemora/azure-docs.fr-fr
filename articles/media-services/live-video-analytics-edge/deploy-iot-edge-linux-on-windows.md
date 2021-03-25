---
title: Déployer sur IoT Edge pour Linux sur Windows - Azure
description: Cet article fournit des conseils sur la façon de déployer sur un appareil IoT Edge pour Linux sur Windows.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 9ec28c62ca804137ede3cd60d1980e55fbaa2807
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618132"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Déployer sur un appareil IoT Edge pour Linux sur Windows (EFLOW)

Dans cet article, vous allez découvrir comment déployer Live Video Analytics sur un appareil de périphérie qui dispose d’[IoT Edge pour Linux sur Windows (EFLOW)](https://docs.microsoft.com/azure/iot-edge/iot-edge-for-linux-on-windows). Une fois que vous aurez terminé les étapes décrites dans ce document, vous pourrez exécuter un [graphe multimédia](media-graph-concept.md) qui détecte le mouvement dans une vidéo et émet de tels événements vers le hub IoT dans le cloud. Vous pourrez ensuite extraire le graphe multimédia pour les scénarios avancés, et bénéficier de toute la puissance de Live Video Analytics dans votre appareil IoT Edge Windows.

## <a name="prerequisites"></a>Prérequis 

* Un compte Azure disposant d’un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    > [!NOTE]
    > Vous aurez besoin d’un abonnement Azure avec des autorisations pour créer des principaux de service (le **rôle propriétaire** permet d’effectuer cette opération). Si vous ne disposez pas des autorisations appropriées, contactez l’administrateur de votre compte pour qu’il vous les accorde.
* [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement. Vérifiez que vous disposez de l’[extension Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Lisez [Qu’est-ce qu’Azure IoT Edge pour Linux sur Windows](https://aka.ms/AzEFLOW-docs).

## <a name="deployment-steps"></a>Étapes du déploiement

Le diagramme suivant illustre le flux d’ensemble du document ; en cinq étapes simples, vous devrez être prêt à exécuter Live Video Analytics sur un appareil Windows qui a EFLOW :

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="Diagramme d’IoT Edge pour Linux sur Windows (EFLOW)":::

1. [Installez EFLOW](https://aka.ms/AzEFLOW-install) sur votre appareil Windows. 

    1. Si vous utilisez votre PC Windows, dans la page d’accueil de [Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview), dans la liste des connexions figure une connexion d’hôte local représentant le PC sur lequel vous exécutez Windows Admin Center. 
    1. Les autres serveurs, ordinateurs ou clusters que vous gérez s’affichent également ici.
    1. Vous pouvez utiliser Windows Admin Center pour installer et gérer Azure EFLOW sur votre appareil local ou des appareils gérés distants. Dans ce guide, la connexion d’hôte local sert d’appareil cible pour le déploiement d’Azure IoT Edge pour Linux sur Windows. localhost est donc également listé en tant qu’appareil IoT Edge.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Étapes de déploiement - Windows Admin Center":::
1. Cliquez sur l’appareil IoT Edge pour vous y connecter. Une vue d’ensemble et un onglet Interface de commande s’affichent. C’est par le biais de l’onglet Interface de commande que vous pouvez envoyer des commandes à votre appareil de périphérie.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Étapes de déploiement - Gestionnaire Azure IoT Edge":::
1. Accédez à l’interface de commande et tapez la commande suivante :
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    Le module Live Video Analytics s’exécute sur l’appareil de périphérie avec des [comptes d’utilisateur locaux](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment) non privilégiés. Il a aussi [besoin de certains dossiers locaux](deploy-iot-edge-device.md#granting-permissions-to-device-storage) pour le stockage des données de configuration d’application. Enfin, pour ce guide pratique, nous utilisons un [simulateur RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) qui relaie un flux vidéo en temps réel vers le module LVA à des fins d’analyse. Ce simulateur prend comme entrée des fichiers vidéo préenregistrés à partir d’un répertoire d’entrée. 
    
    Le script de préparation d’appareil utilisé ci-dessus permet d’automatiser ces tâches, ce qui vous permet de créer en une seule commande tous les dossiers d’entrée et de configuration pertinents, les fichiers d’entrée vidéo et les comptes d’utilisateur avec privilèges. Une fois la commande terminée, les dossiers suivants auront normalement été créés sur votre appareil Edge. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    Notez la présence des fichiers vidéo (*.mkv) dans le dossier /home/lvaedgeuser/samples/input, qui servent de fichiers d’entrée à analyser. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Analyse":::
1. Maintenant que l’appareil de périphérie est configuré, inscrit auprès du hub et en cours d’exécution avec les structures de dossiers correctes créées, l’étape suivante consiste à configurer les ressources Azure supplémentaires ci-dessous et à déployer le module LVA. 

    * Compte de stockage
    * Compte Azure Media Services

    Pour cela, nous vous recommandons d’utiliser le [script d’installation des ressources Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) pour déployer les ressources nécessaires dans votre abonnement Azure. Pour ce faire, procédez comme suit :

    1. Ouvrez [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud Shell":::
    1. Si vous utilisez Cloud Shell pour la première fois, vous serez invité à sélectionner un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files. Sélectionnez **Créer un stockage** pour créer un compte de stockage pour vos informations de session Cloud Shell. Ce compte de stockage est distinct du compte que le script créera pour être utilisé avec votre compte Azure Media Services.
    1. Dans le menu déroulant situé sur le côté gauche de la fenêtre Cloud Shell, sélectionnez Bash comme environnement.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Environnement Bash":::
    1. Exécutez la commande suivante :

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Veillez à répondre **Y** quand vous êtes invité à choisir votre propre appareil de périphérie en tant qu’appareil IoT Edge, puisque vous avez créé l’appareil et le hub IoT plus tôt. Vous serez également invité à entrer le nom de votre hub IoT et l’ID d’appareil IoT Edge. Vous pouvez les récupérer en vous connectant au portail Azure et en cliquant sur votre hub IoT, puis en accédant à l’option IoT Edge dans le panneau du portail IoT Hub.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="Voir les appareils IoT Edge":::

    Une fois terminé, vous pouvez vous reconnecter à l’interface de commande de l’appareil IoT Edge et exécuter la commande suivante.
    
    `sudo iotedge list`
    
    Les quatre modules suivants doivent être déployés et en cours d’exécution sur votre appareil Edge. Notez que le script de création de ressources déploie le module LVA en même temps que les modules IoT Edge (edgeAgent et edgeHub) et un module de simulateur RTSP pour fournir le flux vidéo RTSP simulé.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Voir l’état":::
1. Une fois les modules déployés et configurés, vous êtes prêt à exécuter votre premier graphe multimédia LVA sur EFLOW. Vous pouvez exécuter un graphe de détection de mouvement simple comme celui ci-dessous, et visualiser les résultats en exécutant les étapes suivantes :

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Graphe de détection de mouvement":::

    1. [Configurez](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) l’extension Azure IoT Tools.
    
        > [!Note]
        > Utilisez le chemin suivant : `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json`.
    1. Définissez la topologie, instanciez un graphe et activez-le à l’aide de ces [appels de méthode directe](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls).
    1. [Observez les résultats](get-started-detect-motion-emit-events-quickstart.md#observe-results) sur le hub.
    1. Appelez des [méthodes de nettoyage](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate).
    1. Supprimez vos ressources si vous n’en avez plus besoin.

        > [!IMPORTANT]
        > Les ressources qui ne sont pas supprimées peuvent rester actives et occasionner des coûts Azure.
    
## <a name="next-steps"></a>Étapes suivantes

* Essayez la détection de mouvement avec l’enregistrement de vidéos pertinentes dans le cloud. Suivez les étapes du guide de démarrage rapide [Détecter les mouvements et enregistrer des vidéos sur Media Services](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video).
* Exécutez l’[IA sur la vidéo en direct](use-your-model-quickstart.md#overview) (vous pouvez ignorer la configuration des prérequis, car elle a déjà été effectuée ci-dessus).
* Utilisez notre [extension VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) pour afficher des graphes multimédias supplémentaires.
* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) qui prend en charge RTSP au lieu d’utiliser le simulateur RTSP. Vous trouverez les caméras IP qui prennent RTSP en charge dans la page des [produits conformes ONVIF](https://www.onvif.org/conformant-products/). Recherchez les appareils conformes aux profils G, S ou T.

