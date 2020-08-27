---
ms.openlocfilehash: 768e79c6a2471715b336f90748ad97ecfcc4bbc2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682163"
---

![vue d'ensemble](../../../media/quickstarts/overview-qs4.png)

Le diagramme précédent montre comment les signaux circulent dans ce guide de démarrage rapide. [Un module de périphérie](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simule une caméra IP qui héberge un serveur RTSP (Real-Time Streaming Protocol). Un nœud [source RTSP](../../../media-graph-concept.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud [processeur de détection de mouvement](../../../media-graph-concept.md#motion-detection-processor). La source RTSP envoie les mêmes images vidéo à un nœud [processeur de porte de signal](../../../media-graph-concept.md#signal-gate-processor), qui reste fermé jusqu’à ce qu’il soit déclenché par un événement.

Quand le processeur de détection de mouvement détecte des mouvements dans la vidéo, il envoie un événement au nœud processeur de porte de signal, ce qui le déclenche. La porte s’ouvre pendant la durée configurée, et les images vidéo sont envoyées au nœud [récepteur de fichiers](../../../media-graph-concept.md#file-sink). Ce nœud récepteur enregistre la vidéo au format de fichier MP4 sur le système de fichiers local de votre appareil de périphérie. Le fichier est enregistré à l’emplacement configuré.

Dans ce guide de démarrage rapide, vous allez :

1. créer et déployer le graphe multimédia ;
1. interpréter les résultats ;
1. Supprimer des ressources.
