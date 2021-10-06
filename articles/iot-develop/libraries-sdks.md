---
title: Bibliothèques et Kits de développement logiciel (SDK) IoT Plug-and-Play
description: Informations sur les bibliothèques d’appareils et de services disponibles pour développer des solutions IoT Plug-and-Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-develop
services: iot-develop
ms.custom: mvc
ms.openlocfilehash: 7993dce658f6146df183a95808926ec22ddef931
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457731"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Kits SDK Microsoft pour IoT Plug-and-Play

Les bibliothèques et Kits de développement logiciel (SDK) IoT Plug-and-Play permettent aux développeurs de créer des solutions IoT à l'aide de différents langages de programmation sur plusieurs plateformes. Le tableau suivant contient des liens vers des exemples et des guides de démarrage rapide pour aider votre prise en main :

## <a name="device-sdks"></a>Kits de développement logiciel (SDK) d’appareil

| Langage | Package | Dépôt de code | Exemples | Démarrage rapide | Informations de référence |
|---|---|---|---|---|---|
| C - Appareil | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Exemples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Connexion à IoT Hub](tutorial-connect-device.md) | [Référence](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET - Appareil | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/) | [Exemples](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples) | [Connexion à IoT Hub](tutorial-connect-device.md) | [Référence](/dotnet/api/microsoft.azure.devices.client) |
| Java - Appareil | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/main/) | [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample) | [Connexion à IoT Hub](tutorial-connect-device.md) | [Référence](/java/api/com.microsoft.azure.sdk.iot.device) |
| Python - Appareil | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Exemples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Connexion à IoT Hub](tutorial-connect-device.md) | [Référence](/python/api/azure-iot-device/azure.iot.device) |
| Node - Appareil | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Connexion à IoT Hub](tutorial-connect-device.md) | [Référence](/javascript/api/azure-iot-device/) |
| Embedded C - Appareil | N/A | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Exemples](tutorial-connect-device.md?pivots=programming-language-embedded-c#samples) | [Utilisation d’Embedded C](tutorial-connect-device.md?pivots=programming-language-embedded-c) | N/A

## <a name="service-sdks"></a>Kits de développement logiciel (SDK) IoT Service

| Plateforme  | Package | Dépôt de code | Exemples | Démarrage rapide | Informations de référence |
|---|---|---|---|---|---|
| .NET - service IoT Hub | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Exemples](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/service/PnpServiceSamples) | NON APPLICABLE | [Référence](/dotnet/api/microsoft.azure.devices) |
| Java - service IoT Hub | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples/pnp-service-sample) | NON APPLICABLE | [Référence](/java/api/com.microsoft.azure.sdk.iot.service) |
| Node - service IoT Hub | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | NON APPLICABLE | [Référence](/javascript/api/azure-iothub/) |
| Python - service Digital Twins | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Exemples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interagir avec l’API IoT Hub Digital Twins](tutorial-service.md) | N/A |
| Node - service Digital Twins | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interagir avec l’API IoT Hub Digital Twins](tutorial-service.md) | N/A |

## <a name="next-steps"></a>Étapes suivantes

Pour tester les kits SDK et les bibliothèques, consultez le [Guide du développeur](concepts-developer-guide-device.md), ainsi que les [didacticiels d’appareil](tutorial-connect-device.md) et les [didacticiels de service](tutorial-service.md).