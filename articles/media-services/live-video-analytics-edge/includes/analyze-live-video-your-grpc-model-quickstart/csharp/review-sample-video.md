---
ms.openlocfilehash: eaa44ae9d8bf4d723944da695d9a063937b7e020
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98061205"
---
Quand vous configurez les ressources Azure, une courte vidéo du trafic d’une autoroute est copiée dans Azure sur la machine virtuelle Linux que vous utilisez comme appareil IoT Edge. Ce guide de démarrage rapide utilise le fichier vidéo pour simuler un flux en direct.

Ouvrez une application comme le [lecteur multimédia VLC](https://www.videolan.org/vlc/). Sélectionnez Ctrl+N, puis collez un lien vers [l’exemple de vidéo d’intersection d’autoroute](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) pour démarrer la lecture. Vous voyez la séquence vidéo de nombreux véhicules se déplaçant dans le trafic d’une autoroute.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

Dans ce guide de démarrage rapide, vous allez utiliser Live Video Analytics sur IoT Edge pour détecter des objets tels que des véhicules et des personnes. Vous allez publier les événements d’inférence associés sur IoT Edge Hub.
