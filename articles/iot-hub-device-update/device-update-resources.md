---
title: Comprendre les ressources Device Update pour Azure IoT Hub | Microsoft Docs
description: Découvrez les ressources Device Update pour Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e8194a269aec09f087632d2f6069d0624d7a835b
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111410654"
---
# <a name="device-update-resources"></a>Ressources Device Update

Pour utiliser Device Update pour IoT Hub, vous devez créer un compte et une ressource d’instance Device Update. 

## <a name="device-update-account"></a>Compte Device Update

Un compte Device Update est une ressource qui est créée dans votre abonnement Azure. Au niveau du compte Device Update, vous pouvez sélectionner la région où votre compte Device Update sera créé. Vous pouvez également définir des autorisations pour autoriser les utilisateurs qui auront accès à Device Update.


## <a name="device-update-instance"></a>Instance Device Update
Une fois qu’un compte a été créé, vous devez créer une instance Device Update. Une instance est un conteneur logique qui contient les mises à jour et les déploiements associés à un hub IoT spécifique. Device Update utilise un hub IoT comme répertoire des appareils et un canal de communication avec les appareils. 

Pendant la préversion publique du service, vous pouvez créer deux comptes Device Update par abonnement. En outre, deux instances Device Update peuvent être créées par compte.

## <a name="configuring-device-update-linked-iot-hub"></a>Configuration Device Update liée à IoT Hub 

Pour que Device Update reçoive des notifications de modification d’IoT Hub, Device Update s’intègre au hub d’événements « Intégré ». Le fait de cliquer sur le bouton « Configurer IoT Hub » dans votre instance configure les routes de messages, les groupes de consommateurs et la stratégie d’accès nécessaires pour communiquer avec les appareils IoT. 

### <a name="message-routing"></a>Routage des messages

Les routes de messages suivantes sont configurées pour Device Update :

|   Nom des routes    | source de données | Requête de routage  | Point de terminaison | Description  |
| :--------- | :---- |:---- |:---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | DigitalTwinChangeEvents | true | événements | Est à l’écoute les événements de modification du jumeau numérique  |
|  DeviceUpdate.DeviceLifecycle | DeviceLifecycleEvents | opType = 'deleteDeviceIdentity' OR opType = 'deleteModuleIdentity'  | événements | Est à l’écoute des appareils qui ont été supprimés |
|  DeviceUpdate.DeviceTwinEvents| TwinChangeEvents | (opType = 'updateTwin' OR opType = 'replaceTwin') AND IS_DEFINED($body.tags.ADUGroup) | événements | Est à l’écoute des nouveaux groupes Device Update |

> [!NOTE]
> Les noms des itinéraires n’ont pas guère d’importance lors de leur configuration. Nous incluons DeviceUpdate en tant que préfixe pour assurer la cohérence des noms et déterminer aisément qu’ils sont utilisés pour Device Update. Les autres propriétés d’itinéraire doivent être configurées comme indiqué dans le tableau ci-dessous pour permettre le bon fonctionnement de Device Update. 

### <a name="consumer-group"></a>Groupe de consommateurs

La configuration d’IoT Hub crée également le groupe de consommateurs Event Hub requis par les services Device Update Management. 

:::image type="content" source="media/device-update-resources/consumer-group.png" alt-text="Capture d’écran des groupes de consommateurs." lightbox="media/device-update-resources/consumer-group.png":::

### <a name="access-policy"></a>Stratégie d’accès

Une stratégie d’accès partagé nommée « deviceupdateservice » est requise par les services Device Update Management pour demander des appareils avec mise à jour. La stratégie « deviceupdateservice » est créée et se voit attribuer les autorisations suivantes dans le cadre de la configuration d’IoT Hub :
- Lecture du registre
- Connexion du service
- Connexion de l’appareil

:::image type="content" source="media/device-update-resources/access-policy.png" alt-text="Capture d’écran de la stratégie d’accès." lightbox="media/device-update-resources/access-policy.png":::

## <a name="next-steps"></a>Étapes suivantes

[Créer des ressources Device Update](./create-device-update-account.md)
