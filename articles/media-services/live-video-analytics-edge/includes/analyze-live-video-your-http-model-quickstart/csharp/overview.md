---
ms.openlocfilehash: db0e3bf102feeab6272ac96198d486b51e144d05
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89570051"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="Flux des signaux":::

Ce diagramme montre comment les signaux circulent dans ce guide de démarrage rapide. Un [module de périphérie](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud de [source RTSP](../../../media-graph-concept.md#rtsp-source) récupère le flux vidéo à partir de ce serveur et envoie des images vidéo au nœud [processeur de filtre de fréquence d’images](../../../media-graph-concept.md#frame-rate-filter-processor). Ce processeur limite la fréquence d’images du flux vidéo qui atteint le nœud [processeur d’extension HTTP](../../../media-graph-concept.md#http-extension-processor). 

Le nœud d’extension HTTP joue le rôle d’un proxy. Il convertit les images vidéo dans le type d’image spécifié. Ensuite, il relaie l’image sur REST vers un autre module de périphérie qui exécute un modèle IA derrière un point de terminaison HTTP. Dans cet exemple, le module de périphérie est généré à l’aide du modèle [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), qui peut détecter de nombreux types d’objets. Le nœud processeur d’extension HTTP collecte les résultats de la détection et publie les événements sur le nœud [récepteur IoT Hub](../../../media-graph-concept.md#iot-hub-message-sink). Le nœud envoie ensuite ces événements à [IoT Edge Hub](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Dans ce guide de démarrage rapide, vous allez :

1. créer et déployer le graphe multimédia ;
1. interpréter les résultats ;
1. Supprimer des ressources.
