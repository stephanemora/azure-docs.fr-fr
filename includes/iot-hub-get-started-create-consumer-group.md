---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248895"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Ajouter un groupe de consommateurs à votre instance IoT Hub

[Groupes de consommateurs](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) fournissent des vues indépendantes dans le flux d’événements qui permettent aux applications et services Azure pour consommer indépendamment des données à partir du même point de terminaison concentrateur d’événements. Dans cette section, vous ajoutez un groupe de consommateurs à intégrés point de terminaison votre IoT hub est utilisé plus loin dans ce didacticiel pour extraire les données du point de terminaison.

Pour ajouter un groupe de consommateurs à votre instance IoT Hub, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez votre instance IoT Hub.

2. Dans le volet gauche, sélectionnez **points de terminaison intégrés**, sélectionnez **événements** dans le volet droit, puis entrez un nom sous **groupes de consommateurs**. Sélectionnez **Enregistrer**.

   ![Créer un groupe de consommateurs dans votre IoT Hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
