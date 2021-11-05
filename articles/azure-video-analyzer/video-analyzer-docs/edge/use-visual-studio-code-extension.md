---
title: Utiliser l’extension Visual Studio Code pour Azure Video Analyzer
description: Cet article vous guide dans les étapes de prise en main de l’extension Visual Studio Code pour Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0784f69e40597bef3a08512e3bcbd01cf1343390
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552560"
---
# <a name="use-the-visual-studio-code-extension-for-azure-video-analyzer"></a>Utiliser l’extension Visual Studio Code pour Azure Video Analyzer

[!INCLUDE [header](includes/edge-env.md)]

Cet article vous guide dans les étapes de prise en main de l’extension Studio Code pour Azure Video Analyzer. Vous allez connecter l’extension Visual Studio Code à votre module de périphérie Video Analyzer via l’IoT Hub et déployer un [exemple de topologie de pipeline](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink). Vous allez exécuter un flux vidéo en direct simulé via un pipeline en direct qui enregistre en continu une vidéo dans une ressource vidéo. Le diagramme suivant représente le pipeline.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Enregistrement vidéo en continu":::
 
 ## <a name="prerequisites"></a>Prérequis
 
* [Visual Studio Code](https://code.visualstudio.com/) avec l’[extension Azure Video Analyzer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.azure-video-analyzer)
* Le [Démarrage rapide : Commencer avec Azure Video Analyzer](./get-started-detect-motion-emit-events.md) ou le didacticiel [Enregistrement et lecture vidéo en continu](./use-continuous-video-recording.md)

 > [!NOTE]
 > Les images de cet article sont basées sur le didacticiel [Enregistrement et lecture vidéo en continu](./use-continuous-video-recording.md).    

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

### <a name="obtain-your-iot-hub-connection-string"></a>Obtenir votre chaîne de connexion IoT Hub

Pour effectuer des appels au module de périphérie Video Analyzer, une chaîne de connexion est d’abord nécessaire pour connecter l’extension Visual Studio Code à l’IoT Hub.

1. Dans le Portail Azure, accédez à votre compte IoT Hub.
1. Recherchez **Stratégies d’accès partagé** dans le volet gauche et sélectionnez-le.
1. Cliquez sur la stratégie nommée **iothubowner**.
1. Copiez la valeur **Chaîne de connexion primaire**. Cela ressemble à ceci `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`.

### <a name="connect-the-visual-studio-code-extension-to-the-iot-hub"></a>Connecter l’extension Visual Studio Code à l’IoT Hub

À l’aide de votre chaîne de connexion IoT Hub, connectez l’extension Visual Studio Code au module Video Analyzer. 

1.  Dans Visual Studio Code, sélectionnez l’icône **Azure Video Analyzer** dans la barre d’activité sur le côté gauche.
1.  Dans le volet de l’extension Video Analyzer, cliquez sur le bouton **Entrer la chaîne de connexion**.
1.  En haut, collez la chaîne de connexion IoT Hub.
1.  Sélectionnez l’appareil sur lequel AVA est déployé. La valeur par défaut est nommée `avasample-iot-edge-device`.
1.  Sélectionnez le module Video Analyzer. La valeur par défaut est nommée `avaedge`.

![Gif montrant comment entrer la chaîne de connexion](./media/use-visual-studio-code-extension/enter-connection-string.gif)

Le volet de l’extension Video Analyzer doit maintenant afficher l’appareil connecté avec tous ses modules. Sous les modules, les topologies de pipeline sont répertoriées. Par défaut, aucune topologie de pipeline n’est déployée.

## <a name="create-a-pipeline-topology"></a>Créer une topologie de pipeline 

Une [topologie de pipeline](../pipeline.md) vous permet de décrire la façon dont une vidéo en direct ou des vidéos enregistrées doivent être traitées et analysées pour vos besoins personnalisés via un ensemble de nœuds interconnectés. 

1.  Sur la gauche, sous **Modules**, cliquez avec le bouton droit sur **Topologies de pipeline**, puis sélectionnez **Créer une topologie de pipeline**.
1.  En haut, sous **Essayer des exemples de topologie**, sous **Enregistrement vidéo en continu**, sélectionnez **Enregistrer dans une vidéo Azure Video Analyzer**. Lorsque vous y êtes invité, cliquez sur **Continuer**.
1.  Cliquez sur **Enregistrer** en haut à droite.

![Gif montrant comment ajouter une topologie](./media/use-visual-studio-code-extension/add-topology.gif)

Notez qu’il existe désormais une entrée dans la liste **Topologies de pipeline** sur la gauche intitulée **CVRToVideoSink**. Il s’agit d’une topologie de pipeline dans laquelle certains des paramètres sont définis en tant que variables.

## <a name="create-a-live-pipeline"></a>Créer un pipeline en direct

Un pipeline en direct est une instance de topologie de pipeline. Les variables d’une topologie de pipeline sont remplies lors de la création d’un pipeline en direct.

1.  Sur la gauche, sous **Topologies de pipeline**, cliquez avec le bouton droit sur **CVRToVideoSink** et sélectionnez **Créer un pipeline en direct**.
1.  Pour **Nom de l’instance**, entrez `livePipeline1`.
1. Dans la section **Paramètres**, sous le paramètre **rtspUrl**, entrez `rtsp://rtspsim:554/media/camera-300s.mkv`.
1.  En haut à droite, cliquez sur **Enregistrer et activer**.

![Gif montrant comment créer et activer un pipeline en direct](./media/use-visual-studio-code-extension/create-and-activate.gif)

Maintenant qu’un pipeline en direct a été activé, vous pouvez afficher des événements opérationnels en cliquant sur le bouton **Démarrer la supervision du point de terminaison d’événement intégré** sur l’extension IOT Hub, comme indiqué dans le didacticiel [Enregistrement et lecture vidéo en continu](./use-continuous-video-recording.md#prepare-to-monitor-the-modules).

## <a name="next-steps"></a>Étapes suivantes

Chargez un autre exemple de topologie de pipeline via l’extension Visual Studio Code et affichez les propriétés de chaque nœud. Découvrez comment paramétrer des variables et comment les modules sont connectés en suivant la référence [Extension Visual Studio Code pour Azure Video Analyzer](../visual-studio-code-extension.md).
