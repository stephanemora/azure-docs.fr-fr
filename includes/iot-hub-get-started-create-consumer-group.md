---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 57a5de4c590ff98429aa1d4a0b96cebed6084a0f
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59055646"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Ajouter un groupe de consommateurs à votre instance IoT Hub

Les groupes de consommateurs sont utilisés par les applications pour extraire des données de l’instance Azure IoT Hub. Dans ce didacticiel, vous créez un groupe de consommateurs qu’un prochain service Azure utilisera pour lire les données à partir de votre instance IoT Hub.

Pour ajouter un groupe de consommateurs à votre instance IoT Hub, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez votre instance IoT Hub.

2. Dans le volet gauche, cliquez sur **Point de terminaison prédéfinis**, sélectionnez **Événements** dans le volet supérieur, puis entrez un nom sous **Groupes de consommateurs** dans le volet droit. Cliquez sur **Enregistrer** après avoir changé la valeur de **Durée de vie par défaut** et rétabli sa valeur d’origine.

   ![Créer un groupe de consommateurs dans votre IoT Hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
