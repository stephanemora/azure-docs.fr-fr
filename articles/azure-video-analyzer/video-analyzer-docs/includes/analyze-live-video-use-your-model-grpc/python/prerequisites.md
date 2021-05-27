---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/07/2021
ms.author: juliako
ms.openlocfilehash: c9b37af5b3a30f23cdefcb703adc5e5c41415b3d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371832"
---
* Un compte Azure incluant un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    > [!NOTE]
    > Vous aurez besoin d’un abonnement Azure avec au moins un rôle Contributeur. Si vous ne disposez pas des autorisations appropriées, contactez l’administrateur de votre compte pour qu’il vous les accorde.
    * [Visual Studio Code](https://code.visualstudio.com/), avec les extensions suivantes :
        * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

        > [!TIP]
        > Lors de l’installation d’Azure IoT Tools, vous pouvez être invité à installer Docker. N’hésitez pas à ignorer cette invite.
        * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
    * [Python 3](https://www.python.org/downloads/) (3.6.9 ou version ultérieure), [Pip 3](https://pip.pypa.io/en/stable/installing/) et éventuellement [venv](https://docs.python.org/3/library/venv.html).
 
## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]


## <a name="overview"></a>Vue d'ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/analyze-live-video-use-your-model-grpc/overview.png" alt-text="Vue d’ensemble de gRPC":::
 
Ce diagramme montre comment les signaux circulent dans ce guide de démarrage rapide. Un [module de périphérie](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un [nœud source RTSP](./../../../pipeline.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud du [processeur de détection de mouvements](./../../../pipeline.md#motion-detection-processor). Ce processeur détecte le mouvement et, après détection, envoie des images vidéo au nœud du [processeur d’extension gRPC](./../../../pipeline.md#grpc-extension-processor).

Le nœud d’extension gRPC joue le rôle d’un proxy. Il convertit les images vidéo dans le type d’image spécifié. Ensuite, il relaie l’image via gRPC vers un autre module de périphérie qui exécute un modèle IA derrière un point de terminaison gRPC via une [mémoire partagée](https://en.wikipedia.org/wiki/Shared_memory). Dans cet exemple, le module de périphérie est généré à l’aide du modèle [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3), qui peut détecter de nombreux types d’objets. Le nœud du processeur d’extension gRPC collecte les résultats de la détection et publie les événements sur le nœud [récepteur IoT Hub](./../../../pipeline.md#iot-hub-message-sink). Le nœud envoie ensuite ces événements à [IoT Edge Hub](../../../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub).

Dans ce guide de démarrage rapide, vous allez :

1. Créer et déployer le pipeline.
1. interpréter les résultats ;
1. Supprimer des ressources.

## <a name="set-up-your-development-environment"></a>Configurer l’environnement de développement
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/python/python-set-up-dev-env.md)]