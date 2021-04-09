---
title: Comprendre le fichier de configuration Device Update pour Azure IoT Hub | Microsoft Docs
description: Prenez connaissance du fichier de configuration Device Update pour Azure IoT Hub.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101660247"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>Fichier de configuration Device Update pour Azure IoT Hub

Le fichier « adu-conf.txt » est un fichier facultatif qui peut être créé pour gérer les configurations suivantes.

* AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]
* AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]
* DeviceInformation.manufacturer
* DeviceInformation.model
* Chaîne de connexion de l’appareil (si elle n’est pas connue par l’agent Device Update).

## <a name="purpose"></a>Objectif
L’agent Device Update tente d’abord d’obtenir les valeurs `manufacturer` et `model` de l’appareil à utiliser pour la [couche d’interface](device-update-agent-overview.md#the-interface-layer). En cas d’échec, l’agent Device Update recherchera ensuite le fichier « adu-conf.txt » et utilisera les valeurs à partir de là. Si ces deux tentatives échouent, l’agent Device Update utilisera les valeurs [par défaut](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt).

En savoir plus sur l’[interface ADU Core](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) et l’[interface d’informations sur l’appareil](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

## <a name="file-location"></a>Emplacement du fichier

Dans le système Linux, dans la partition ou sur le disque appelé `adu`, créez un fichier texte appelé « adu-conf.txt » avec les champs suivants.

## <a name="list-of-fields"></a>Liste de champs

|Nom|Description|
|-----------|--------------------|
|connection_string|Chaîne de connexion préprovisionnée que l’appareil peut utiliser pour se connecter au hub IoT. Remarque : Non requis si vous provisionnez l’agent Device Update via le [service d’identité Azure IoT](https://azure.github.io/iot-identity-service/)|
|aduc_manufacturer|Signalé par l’interface `AzureDeviceUpdateCore:4.ClientMetadata:4` pour classer l’appareil afin de cibler le déploiement de mise à jour.|
|aduc_model|Signalé par l’interface `AzureDeviceUpdateCore:4.ClientMetadata:4` pour classer l’appareil afin de cibler le déploiement de mise à jour.|
|manufacturer|Signalé par l’agent Device Update dans le cadre de l’interface `DeviceInformation`.|
|model|Signalé par l’agent Device Update dans le cadre de l’interface `DeviceInformation`.|

## <a name="example-adu-conftxt-file-contents"></a>Exemple de contenu du fichier « adu-conf.txt »

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
