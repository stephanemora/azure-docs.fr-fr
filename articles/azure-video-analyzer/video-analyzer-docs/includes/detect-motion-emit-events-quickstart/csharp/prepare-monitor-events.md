---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 05/05/2021
ms.author: faneerde
ms.openlocfilehash: 232c0fea75c6e6b9ce03e90f093d22f6b68cefe6
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385871"
---
Vous allez utiliser le module périphérique Video Analyzer pour détecter les mouvements dans le flux vidéo en direct entrant et envoyer des événements à IoT Hub. Pour voir ces événements, effectuez les étapes suivantes :

1. Ouvrez le volet Explorateur dans Visual Studio Code, puis recherchez Azure IoT Hub dans l’angle inférieur gauche.
1. Développez le nœud **Appareils**.
1. Cliquez avec le bouton droit sur **avasample-iot-edge-device**, puis sélectionnez **Démarrer la supervision du point de terminaison d’événements intégré**.

   ![Démarrer la supervision d’un point de terminaison d’événement intégré](../../../media/vscode-common-screenshots/start-monitoring.png)

> [!NOTE]
> Vous pouvez être invité à fournir des informations sur le point de terminaison intégré pour le hub IoT. Pour obtenir ces informations, dans le portail Azure, accédez à votre hub IoT et recherchez l’option **Points de terminaison intégrés** dans le volet de navigation gauche. Cliquez et recherchez l’option **Point de terminaison compatible Event Hub** sous la section **Point de terminaison compatible Event Hub**. Copiez et utilisez le texte dans la zone. Le point de terminaison doit ressembler à ceci : <br/>`Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>`
