---
ms.openlocfilehash: 3196d377c09ca0cce24093710bc4be13fb21d2e3
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531469"
---
Vérifiez que vous avez effectué les étapes permettant de [préparer la supervision d’événements](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events).

![Démarrer la supervision du point de terminaison d'événement intégré](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Vous pouvez être invité à fournir des informations sur le point de terminaison intégré pour le hub IoT. Pour obtenir ces informations, dans le portail Azure, accédez à votre hub IoT et recherchez l’option **Points de terminaison intégrés** dans le volet de navigation gauche. Cliquez et recherchez l’option **Point de terminaison compatible Event Hub** sous la section **Point de terminaison compatible Event Hub**. Copiez et utilisez le texte dans la zone. Le point de terminaison doit ressembler à ceci :  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
