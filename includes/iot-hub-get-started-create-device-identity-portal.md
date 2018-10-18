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
ms.openlocfilehash: ffd5da239f8e271a8c9b2aaf3f6d5fd9f885c79c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400164"
---
## <a name="create-a-device-identity"></a>Création d’une identité d’appareil

Dans cette section, vous allez utiliser le [portail Azure](https://portal.azure.com) pour créer une identité d’appareil dans le registre des identités de votre hub IoT. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre des identités. Pour plus d’informations, consultez le « Registre des identités » du [Guide du développeur IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Utilisez le volet **Appareils IoT** du portail pour générer un ID d’appareil unique et la clé de votre appareil lui permettant de s’identifier auprès d’IoT Hub. Les ID d’appareil respectent la casse.

1. Connectez-vous au [portail Azure](https://portal.azure.com)

2. Sélectionnez **Toutes les ressources**, puis recherchez votre ressource IoT Hub.

3. Lorsque votre ressource IoT Hub est ouverte, cliquez sur l’outil **Appareil IoT**, puis cliquez en haut sur **Ajouter**. 

    ![Créer une identité d’appareil sur le portail](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

4. Indiquez un nom pour votre nouvel appareil, par exemple **myDeviceId**, puis cliquez sur **Enregistrer**. Cette action permet de créer une identité d’appareil pour votre IoT Hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Ajouter un nouvel appareil](./media/iot-hub-get-started-create-device-identity-portal/create-a-device.png)

5. Dans la liste des appareils, cliquez sur l’appareil nouvellement créé, puis copiez la **chaîne de connexion ainsi que la clé primaire** pour une utilisation ultérieure.

    ![Chaîne de connexion de l’appareil](./media/iot-hub-get-started-create-device-identity-portal/device-details.png)

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
