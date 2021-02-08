---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956268"
---
À l’étape [Générer et déployer le manifeste de déploiement IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest), dans Visual Studio Code, développez le nœud **lva-sample-device** sous **AZURE IOT HUB** (dans la section en bas à gauche). Vous devez voir les modules suivants déployés :

* Le module Live Video Analytics, nommé `lvaEdge`
* Le module `rtspsim`, qui simule un serveur RTSP faisant office de source d’un flux vidéo en direct

  ![Modules](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Les étapes ci-dessus supposent que vous utilisez la machine virtuelle créée par le script d’installation. Si vous utilisez votre propre appareil de périphérie, accédez à cet appareil, puis exécutez les commandes suivantes avec des **droits d'administrateur** pour extraire et stocker l'exemple de fichier vidéo utilisé dans ce guide de démarrage rapide :  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
