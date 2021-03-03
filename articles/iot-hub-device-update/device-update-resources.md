---
title: Comprendre les ressources Device Update pour Azure IoT Hub | Microsoft Docs
description: Découvrez les ressources Device Update pour Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678332"
---
# <a name="device-update-resources"></a>Ressources Device Update

Pour utiliser Device Update pour IoT Hub, vous devez créer un compte et une ressource d’instance Device Update. 

## <a name="device-update-account"></a>Compte Device Update

Un compte Device Update est une ressource qui est créée dans votre abonnement Azure. Au niveau du compte Device Update, vous pouvez sélectionner la région où votre compte Device Update sera créé. Vous pouvez également définir des autorisations pour autoriser les utilisateurs qui auront accès à Device Update.


## <a name="device-update-instance"></a>Instance Device Update
Une fois qu’un compte a été créé, vous devez créer une instance Device Update. Une instance est un conteneur logique qui contient les mises à jour et les déploiements associés à un hub IoT spécifique. Device Update utilise un hub IoT comme répertoire des appareils et un canal de communication avec les appareils. 

Pendant la préversion publique du service, vous pouvez créer deux comptes Device Update par abonnement. En outre, deux instances Device Update peuvent être créées par compte.

## <a name="configuring-device-update-linked-iot-hub"></a>Configuration Device Update liée à IoT Hub 

Pour que Device Update reçoive des notifications de modification d’IoT Hub, Device Update s’intègre au hub d’événements « Intégré ». Le fait de cliquer sur le bouton « Configurer IoT Hub » dans votre instance configure les routes de messages et la stratégie d’accès nécessaires pour communiquer avec les appareils IoT. 

Les routes de messages suivantes sont configurées pour Device Update :

|   Nom des routes    | Requête de routage  | Description  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |Est à l’écoute les événements de modification du jumeau numérique  |
|  DeviceUpdate.DeviceLifeCycle | opType = 'deleteDeviceIdentity'  | Est à l’écoute des appareils qui ont été supprimés |
|  DeviceUpdate.TelemetryModelInformation | iothub-interface-id = "urn:azureiot:ModelDiscovery:ModelInformation:1 | Est à l’écoute des nouveaux types d’appareils |
|  DeviceUpdate.DeviceTwinEvents| (opType = 'updateTwin' OR opType = 'replaceTwin') AND IS_DEFINED($body.tags.ADUGroup) | Est à l’écoute des nouveaux groupes Device Update |

## <a name="next-steps"></a>Étapes suivantes

[Créer des ressources Device Update](./create-device-update-account.md)
