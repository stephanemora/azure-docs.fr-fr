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
ms.openlocfilehash: 1df3e188b71b8fa2d5223bad8bc5914513e26286
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "34371199"
---
## <a name="create-a-device-identity"></a>Création d’une identité d’appareil

Dans cette section, vous allez utiliser le [Portail Azure][lnk-azure-portal] afin de créer une identité d’appareil dans le registre d’identité présent dans votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre des identités. Reportez-vous à la section Registre d’identité du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Utilisez le volet **Appareils IoT** dans le portail pour générer un ID d’appareil unique et la clé de votre appareil permettant l’identification à IoT Hub. Les ID d’appareil respectent la casse.

1. Connectez-vous au [portail Azure][lnk-azure-portal].

1. Sélectionnez **Toutes les ressources**, puis recherchez votre ressource IoT Hub.

1. Lorsque votre ressource IoT Hub est ouverte, cliquez sur l’outil **Appareil IoT**, puis cliquez en haut sur **Ajouter**. 

    ![Créer une identité d’appareil sur le portail][img-add-device]

1. Indiquez un nom pour votre nouvel appareil, par exemple **myDeviceId**, puis cliquez sur **Enregistrer**. Cette action permet de créer une identité d’appareil pour votre IoT Hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Ajouter un nouvel appareil][img-create-device]

1. Dans la liste des appareils, cliquez sur l’appareil nouvellement créé, puis copiez la **chaîne de connexion ainsi que la clé primaire** pour une utilisation ultérieure.

    ![Chaîne de connexion de l’appareil][img-connection-string]

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-add-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png
[img-create-device]:./media/iot-hub-get-started-create-device-identity-portal/add-device.png

<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

