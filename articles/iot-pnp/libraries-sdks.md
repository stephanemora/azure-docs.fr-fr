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
ms.openlocfilehash: 5638cd9973c6a4df809e0b200efe85b067aae026
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407793"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Kits SDK Microsoft pour IoT Plug-and-Play

Les bibliothèques et Kits de développement logiciel (SDK) IoT Plug-and-Play permettent aux développeurs de créer des solutions IoT à l’aide d’un large éventail de langages de programmation sur plusieurs plateformes. Le tableau suivant contient des liens vers des exemples et des guides de démarrage rapide pour aider votre prise en main :

## <a name="device-sdks-ga"></a>Kits SDK d’appareil (GA)

| Langage | Package | Dépôt de code | Exemples | Démarrage rapide | Informations de référence |
|---|---|---|---|---|---|
| C - Appareil | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [Exemples](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Connexion à IoT Hub](quickstart-connect-device-c.md) | [Référence](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET - Appareil | [NuGet 1.27.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Exemples](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Connexion à IoT Hub](quickstart-connect-device-csharp.md) | [Référence](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java - Appareil | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Connexion à IoT Hub](quickstart-connect-device-java.md) | [Référence](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python - Appareil | [pip 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Exemples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Connexion à IoT Hub](quickstart-connect-device-python.md) | [Référence](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Node - Appareil | [npm 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Connexion à IoT Hub](quickstart-connect-device-node.md) | [Référence](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

## <a name="device-sdks-preview"></a>Kits SDK d’appareil (préversion)

| Langage | Référentiel/exemples de code |
|---|---|
|Kit SDK Azure pour Embedded| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Intergiciel (middleware) Azure RTOS IoT| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Guides de démarrage Azure RTOS | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks-preview"></a>Kits SDK de service (préversion)

| Langage | Package | Dépôt de code | Exemples | Démarrage rapide | Informations de référence |
|---|---|---|---|---|---|
| .NET - préversion du service IoT Hub | [NuGet 1.27.1-preview-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [Exemples](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | NON APPLICABLE | N/A |
| Java - préversion du service IoT Hub | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | NON APPLICABLE | N/A |
| Node - préversion du service IoT Hub | [npm 1.12.4-pnp-refresh.4](https://www.npmjs.com/package/azure-iothub/v/1.12.4-pnp-refresh.4) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/service/samples) | NON APPLICABLE | N/A |
| Python - préversion du service IoT Hub/Digital Twins | [pip 2.2.1rc1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [Exemples](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Interagir avec l’API IoT Hub Digital Twins](quickstart-service-python.md) | N/A |
| Node - préversion du service Digital Twins | [npm 1.0.0-pnp-refresh.3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Interagir avec l’API IoT Hub Digital Twins](quickstart-service-node.md) | N/A |

## <a name="next-steps"></a>Étapes suivantes

Pour tester les kits SDK et les bibliothèques, consultez le [Guide du développeur](concepts-developer-guide.md), ainsi que les [guides de démarrage rapide d’appareil](quickstart-connect-device-c.md) et les [guides de démarrage rapide de service](quickstart-service-node.md).
