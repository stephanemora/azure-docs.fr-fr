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
ms.openlocfilehash: 935e2b9e861a889bef48c1d7ba119ab694cddfb3
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094208"
---
## <a name="create-a-device-identity"></a>Créer une identité d’appareil

Dans cette section, vous allez utiliser le [portail Azure](https://portal.azure.com) pour créer une identité d’appareil dans le registre des identités de votre hub IoT. Un appareil ne peut pas se connecter à un hub IoT, à moins d’avoir une entrée dans le registre des identités. Pour plus d’informations, consultez le « Registre des identités » du [Guide du développeur IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Utilisez le volet **Appareils IoT** du portail pour générer un ID d’appareil unique et la clé de votre appareil lui permettant de s’identifier auprès d’IoT Hub. Les ID d’appareil respectent la casse.

1. Connectez-vous au [portail Azure](https://portal.azure.com)

1. Sélectionnez **Toutes les ressources**, puis recherchez votre ressource IoT Hub.

1. Lorsque votre ressource IoT Hub est ouverte, cliquez sur l’outil **Appareil IoT**, puis cliquez en haut sur **Ajouter**. 

    ![Créer une identité d’appareil sur le portail](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

1. Indiquez un nom pour votre nouvel appareil, par exemple **myDeviceId**, puis cliquez sur **Enregistrer**. Cette action permet de créer une identité d’appareil pour votre hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Ajouter un nouvel appareil](./media/iot-hub-get-started-create-device-identity-portal/add-device.png)

1. Dans la liste des appareils, cliquez sur l’appareil nouvellement créé, puis copiez la **chaîne de connexion ainsi que la clé primaire** pour les utiliser plus tard.

    ![Chaîne de connexion de l’appareil](./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png)

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès d’un appareil en particulier. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
