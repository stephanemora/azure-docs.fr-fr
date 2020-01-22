---
title: Bibliothèques et Kits de développement logiciel (SDK) IoT Plug-and-Play
description: Informations sur les bibliothèques d’appareils et de services disponibles pour développer des solutions IoT Plug-and-Play.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b328d7fe4cf3a3487614ed93dcf130aa7a233efd
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830531"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Bibliothèques et Kits de développement logiciel (SDK) IoT Plug-and-Play

Les bibliothèques et Kits de développement logiciel (SDK) IoT Plug-and-Play permettent aux développeurs de créer des solutions IoT à l’aide d’un large éventail de langages de programmation sur plusieurs plateformes. Le tableau suivant contient des liens vers des exemples et des guides de démarrage rapide pour aider votre prise en main :

## <a name="microsoft-supported-libraries-and-sdks"></a>Bibliothèques et Kits de développement logiciel (SDK) pris en charge par Microsoft

| Plateforme | Bibliothèque/package | Code source | Exemple | Démarrage rapide | Informations de référence |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Kit de développement logiciel (SDK) d’appareil sur apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Exemples de clients de jumeaux numériques](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Connexion à IoT Hub](./quickstart-connect-pnp-device-c-linux.md) | [Référence](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Kit de développement logiciel (SDK) d’appareil sur Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Exemples de clients de jumeaux numériques](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Connexion à IoT Hub](./quickstart-connect-pnp-device-c-windows.md) | [Référence](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Kit de développement logiciel (SDK) d’appareil sur EMBED](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Exemples de clients de jumeaux numériques](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Référence](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Kit de développement logiciel (SDK) d’appareil dans l’IDE Arduino](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Exemples de clients de jumeaux numériques](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Référence](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Kit de développement logiciel (SDK) d’appareil sur CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Exemples de clients de jumeaux numériques](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Référence](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Exemples de jumeaux numériques](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Connexion à IoT Hub](./quickstart-connect-pnp-device-csharp.md) | [Référence](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Exemples de jumeaux numériques](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Connexion à IoT Hub](./quickstart-connect-pnp-device-java.md) | [Référence](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Exemples de jumeaux numériques](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Connexion à IoT Hub](./quickstart-connect-pnp-device-node.md) | [Référence](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |

## <a name="iot-hub-support"></a>Prise en charge d’IoT Hub

Les fonctionnalités d’appareils IoT Plug-and-Play sont prises en charge uniquement par les [hubs IoT gratuits et standard](../iot-hub/iot-hub-scaling.md).

## <a name="next-steps"></a>Étapes suivantes

En plus des bibliothèques et des Kits de développement logiciel (SDK) d’appareils, vous pouvez utiliser les API REST pour interagir avec les référentiels de modèles.