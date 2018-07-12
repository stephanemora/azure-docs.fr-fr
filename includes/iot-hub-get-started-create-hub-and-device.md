---
title: Fichier Include
description: Fichier Include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f8cd78e63099f864c5fc54b6268f6e558d738626
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724901"
---
## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Maintenant que vous avez créé un IoT Hub, recherchez les informations importantes que vous utilisez pour connecter des appareils et des applications à votre IoT Hub. 

Dans le menu de navigation de votre IoT Hub, ouvrez **Stratégies d’accès partagé**.
Sélectionnez la stratégie **iothubowner**, puis copiez la **Chaîne de connexion – clé primaire** de votre IoT Hub. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

   > [!NOTE] 
   > Vous n’avez pas besoin de cette chaîne de connexion iothubowner pour ce didacticiel d’installation. Toutefois, cette chaîne vous sera peut-être nécessaire pour certains didacticiels ou pour différents scénarios IoT une fois cette installation terminée.

   ![Obtention de la chaîne de connexion de votre IoT Hub](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Inscrire un appareil dans l’IoT Hub pour votre appareil

1. Dans le menu de navigation de votre IoT Hub, ouvrez **Appareils IoT**, puis cliquez sur **Ajouter** pour inscrire un appareil dans votre IoT Hub.

   ![Ajouter un appareil aux appareils IoT de votre hub IoT](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Entrez un **ID d’appareil** pour le nouvel appareil. Les ID d’appareil respectent la casse.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Cliquez sur **Enregistrer**.
5. Une fois l’appareil créé, ouvrez ce dernier à partir de la liste figurant dans le volet **Appareils IoT**.
6. Copiez la **Chaîne de connexion – clé primaire** pour une utilisation ultérieure.

   ![Obtention de la chaîne de connexion de l’appareil](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
