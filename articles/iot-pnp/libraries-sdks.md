---
title: Bibliothèques et Kits de développement logiciel (SDK) IoT Plug-and-Play
description: Informations sur les bibliothèques d’appareils et de services disponibles pour développer des solutions IoT Plug-and-Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 87e701bfd742c48cf7d99b2fa09fa408e900a77d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714837"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Kits SDK Microsoft pour IoT Plug-and-Play

Les bibliothèques et Kits de développement logiciel (SDK) IoT Plug-and-Play permettent aux développeurs de créer des solutions IoT à l’aide d’un large éventail de langages de programmation sur plusieurs plateformes. Le tableau suivant contient des liens vers des exemples et des guides de démarrage rapide pour aider votre prise en main :

## <a name="device-sdks"></a>Kits de développement logiciel (SDK) d’appareil

| Langage | Package | Dépôt de code | Exemples | Démarrage rapide | Informations de référence |
|---|---|---|---|---|---|
| C - Appareil | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Exemples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Connexion à IoT Hub](quickstart-connect-device-c.md) | [Référence](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET - Appareil | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Exemples](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Connexion à IoT Hub](quickstart-connect-device-csharp.md) | [Référence](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| Java - Appareil | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Connexion à IoT Hub](quickstart-connect-device-java.md) | [Référence](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python - Appareil | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Exemples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Connexion à IoT Hub](quickstart-connect-device-python.md) | [Référence](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| Node - Appareil | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Connexion à IoT Hub](quickstart-connect-device-node.md) | [Référence](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |
| Embedded C - Appareil | N/A | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Exemples](howto-use-embedded-c.md#samples) | [Utilisation d’Embedded C](howto-use-embedded-c.md) | N/A

## <a name="service-sdks"></a>Kits de développement logiciel (SDK) IoT Service

| Plateforme  | Package | Dépôt de code | Exemples | Démarrage rapide | Informations de référence |
|---|---|---|---|---|---|
| .NET - service IoT Hub | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Exemples](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | NON APPLICABLE | [Référence](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| Java - service IoT Hub | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | NON APPLICABLE | [Référence](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| Node - service IoT Hub | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | NON APPLICABLE | [Référence](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Python - service Digital Twins | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Exemples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interagir avec l’API IoT Hub Digital Twins](quickstart-service-python.md) | N/A |
| Node - service Digital Twins | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interagir avec l’API IoT Hub Digital Twins](quickstart-service-node.md) | N/A |

## <a name="next-steps"></a>Étapes suivantes

Pour tester les kits SDK et les bibliothèques, consultez le [Guide du développeur](concepts-developer-guide-device-csharp.md), ainsi que les [guides de démarrage rapide d’appareil](quickstart-connect-device-c.md) et les [guides de démarrage rapide de service](quickstart-service-node.md).
