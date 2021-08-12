---
title: Extension Visual Studio Code pour Azure Video Analyzer
description: Ce guide de démarrage rapide vous guide tout au long des étapes de prise en main de l’extension Visual Studio Code pour Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: dd342d1aaf8cf93bfcf518342315ad022e52f4f9
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604022"
---
# <a name="quickstart-azure-video-analyzer-visual-studio-code-extension"></a>Démarrage rapide : Extension Visual Studio Code pour Azure Video Analyzer

Ce guide de démarrage rapide est conçu pour vous montrer comment configurer et connecter l’extension Visual Studio Code pour Video Analyzer à votre module périphérique Video Analyzer, et comment déployer un exemple de topologie de pipeline.  Il va utiliser le même pipeline que le guide de démarrage rapide Bien démarrer.  

Une fois les étapes de configuration terminées, vous pourrez exécuter un flux vidéo en direct simulé via un pipeline qui détecte et signale les mouvements dans ce flux. Le diagramme suivant représente graphiquement ce pipeline.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="Détecter le mouvement":::
 
 ## <a name="prerequisites"></a>Prérequis
 
* Un compte Azure incluant un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) gratuitement.

* [Visual Studio Code](https://code.visualstudio.com/), avec les extensions suivantes :
    * [Video Analyzer](https://go.microsoft.com/fwlink/?linkid=2163332)

* Si vous n’avez pas suivi le guide de démarrage rapide [Bien démarrer - Azure Video Analyzer](./get-started-detect-motion-emit-events.md), veillez à [configurer les ressources Azure](#set-up-azure-resources).    

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="connect-the-azure-video-analyzer-visual-studio-code-extension-to-your-iot-hub"></a>Connectez l’extension Visual Studio Code pour Azure Video Analyzer à votre hub IoT

Pour connecter l’extension au module périphérique, vous devez d’abord récupérer votre chaîne de connexion. Pour cela, effectuez les étapes suivantes.

1.  Accédez au [portail Azure](https://portal.azure.com) et sélectionnez votre hub IoT.
1.  Sur la gauche, sous `Settings`, sélectionnez `Shared access policies`.
1.  Sélectionnez le nom de stratégie `iothubowner`.
1.  Dans la fenêtre de droite, copiez la `Primary connection string`.

Maintenant que vous avez la chaîne de connexion, les étapes ci-dessous vont connecter l’extension au module périphérique.

1.  Dans Visual Studio Code, sélectionnez l’icône `Azure Video Analyzer` sur la gauche.
1.  Cliquez sur le bouton `Enter Connection String`.
1.  En haut, collez la chaîne de connexion depuis le portail.
1.  Sélectionnez l’appareil : il s’agit par défaut de `avasample-iot-edge-device`.
1.  Sélectionnez le module Video Analyzer : il s’agit par défaut de `avaedge`.

Sur la gauche, vous voyez maintenant votre appareil connecté avec le module sous-jacent.  Par défaut, aucune topologie de pipeline n’est déployée.

## <a name="deploy-a-topology-and-live-pipeline"></a>Déployer une topologie et un pipeline en direct

Les topologies de pipeline sont les composants de base utilisés par Video Analyzer pour définir comment le travail est effectué.  Vous trouverez plus d’informations sur les [topologies de pipeline ici](./pipeline.md).  Dans cette section, vous allez déployer une topologie de pipeline qui est un modèle, puis créer une instance de la topologie ou « pipeline en direct ». Le pipeline en direct est connecté au flux vidéo réel.

1.  Sur la gauche, sous `Modules`, cliquez avec le bouton droit sur `Pipeline topologies`, puis sélectionnez « Créer une topologie de pipeline ».
1.  En haut, sous `Try sample topologies`, sous `Motion Detection`, sélectionnez `Publish motion events to IoT Hub`.  Quand vous y êtes invité, cliquez sur `Proceed`.
1.  Cliquez sur `Save` en haut à droite.

Vous devez maintenant voir une entrée dans la liste `Pipeline topologies` à gauche intitulée `MotionDetection`.  C’est une topologie de pipeline dans laquelle certains des paramètres sont définis en tant que variables que vous pouvez renseigner quand vous créez un pipeline en direct.  Ensuite, nous allons créer un pipeline en direct.

1.  Sur la gauche, sous `Pipeline topologies`, cliquez avec le bouton droit sur `MotionDetection`, puis sélectionnez `Create live pipeline`.
1.  Pour `Live pipeline name`, entrez `mdpipeline1`.
1.  Dans la section `Parameters` :
    - Pour « rtspPassword », entrez « testuser ».
    - Pour « rtspUrl », entrez « rtsp://rtspsim:554/media/camera-300s.mkv ».
    - Pour « rtspUserName », entrez « testpassword ».
1.  En haut à droite, cliquez sur « Enregistrer et activer ».

Vous obtenez une topologie de démarrage déployée et un pipeline en direct opérationnel sur votre appareil périphérique.  Si vous avez installé l’extension Azure IoT Hub comme indiqué dans le guide de démarrage rapide Bien démarrer, vous pouvez surveiller le point de terminaison d’événements intégré dans l’extension Visual Studio Code pour Azure IoT Hub pour voir cela, comme indiqué dans la section [Observer les résultats](./get-started-detect-motion-emit-events.md#observe-results).

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les différentes fonctions de l’[extension Visual Studio Code pour Azure Video Analytics](./visual-studio-code-extension.md).
