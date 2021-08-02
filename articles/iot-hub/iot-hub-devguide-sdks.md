---
title: Kits SDK Azure IoT Hub | Microsoft Docs
description: Liens vers les kits SDK Azure IoT Hub que vous pouvez utiliser pour générer des applications d’appareil et des applications back-end.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 67c9bd6c4f8779340a847815da8692e12e3ee1ac
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537056"
---
# <a name="azure-iot-hub-sdks"></a>Kits de développement logiciel (SDK) IoT Hub

Il existe deux catégories de kits de développement logiciel (SDK) permettant de travailler avec IoT Hub :

* [**Les kits SDK Service IoT Hub**](#azure-iot-hub-service-sdks) vous permettent de créer des applications back-end pour gérer votre hub IoT et, si vous le souhaitez, d’envoyer des messages, de planifier des travaux, d’appeler des méthodes directes ou d’envoyer les mises à jour de propriétés souhaitées à vos modules ou appareils IoT.

* [**Les kits SDK Appareil IoT Hub**](../iot-develop/about-iot-sdks.md) vous permettent de générer des applications qui s’exécutent sur vos appareils IoT à l’aide d’un client d’appareil ou d’un client de module. Ces applications envoient des données de télémétrie à votre hub IoT et reçoivent le cas échéant des messages ou des mises à jour de tâche, de méthode ou de jumeau de ce hub IoT. Vous pouvez utiliser ces kits SDK afin de créer des applications d’appareil qui utilisent des conventions et des modèles [Azure IoT Plug-and-Play](../iot-pnp/overview-iot-plug-and-play.md) pour publier leurs fonctionnalités sur des applications compatibles avec IoT Plug-and-Play. Vous pouvez également utiliser un client de module afin de créer des [modules](../iot-edge/iot-edge-modules.md) pour le [runtime Azure IoT Edge](../iot-edge/about-iot-edge.md).

De plus, nous fournissons également un ensemble de SDK pour travailler avec le [Service Device Provisioning](../iot-dps/about-iot-dps.md).

* Les **SDK Device Provisioning** vous permettent de créer des applications qui s’exécutent sur vos appareils IoT pour communiquer avec le Service Device Provisioning.

* Les **SDK Service Provisioning** vous permettent de créer des applications back-end pour gérer vos inscriptions dans le Service Device Provisioning.

Découvrez les [avantages du développement avec les Kits de développement logiciel (SDK) Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="azure-iot-hub-service-sdks"></a>Kits Azure IoT Hub Service SDK

Les kits Azure IoT service SDK contiennent du code pour faciliter la création d’applications qui interagissent directement avec IoT Hub pour gérer les appareils et la sécurité.

| Plateforme  | Package | Dépôt de code | Exemples |  Informations de référence |
|---|---|---|---|---|
| .NET | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Exemples](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [Référence](/dotnet/api/microsoft.azure.devices) |
| Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | [Référence](/java/api/com.microsoft.azure.sdk.iot.service) |
| Nœud | [npm](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [Référence](/javascript/api/azure-iothub/) |
| Python | [pip](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Exemples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Référence](/python/api/azure-iot-hub) |
| Node.js | [npm](https://www.npmjs.com/package/azure-iot-common) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Référence](/javascript/api/azure-iothub/) |

Kit Azure IoT Hub service SDK pour iOS :

* Installation à partir de [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Exemples](https://github.com/Azure-Samples/azure-iot-samples-ios)

## <a name="microsoft-azure-provisioning-sdks"></a>Kits SDK de provisionnement Microsoft Azure

Les **Kits de développement logiciel (SDK) de provisionnement Microsoft Azure** vous permettent d’ajouter des appareils à votre hub IoT à l’aide du [Service Device Provisioning](../iot-dps/about-iot-dps.md).

| Plateforme | Package | Code source | Informations de référence |
| -----|-----|-----|-----|
| .NET|[Kit SDK Appareil](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/), [kit SDK Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) |[GitHub](https://github.com/Azure/azure-iot-sdk-csharp/)|[Informations de référence](/dotnet/api/microsoft.azure.devices.provisioning.client) |
| C|[apt-get, MBED, Arduino IDE ou iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)|[GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning\_client)|[Référence](/azure/iot-hub/iot-c-sdk-ref/) |
| Java|[Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)|[GitHub](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)|[Référence](/java/api/com.microsoft.azure.sdk.iot.provisioning.device) |
| Node.js|[Kit SDK Appareil](https://badge.fury.io/js/azure-iot-provisioning-device), [kit SDK Service](https://badge.fury.io/js/azure-iot-provisioning-service) |[GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)|[Référence](/javascript/api/overview/azure/iothubdeviceprovisioning) |
| Python|[Kit SDK Appareil](https://pypi.org/project/azure-iot-device/), [kit SDK Service](https://pypi.org/project/azure-iothub-provisioningserviceclient/)|[GitHub](https://github.com/Azure/azure-iot-sdk-python)|[Référence de l’appareil](/python/api/azure-iot-device/azure.iot.device.provisioningdeviceclient), [référence du service](/python/api/azure-mgmt-iothubprovisioningservices) |

## <a name="azure-iot-hub-device-sdks"></a>Kits Azure IoT Hub Device SDK

Les kits Microsoft Azure IoT device SDK contiennent du code qui facilite la création d’applications qui se connectent aux services Azure IoT Hub et sont gérés par eux.

En savoir plus sur les kits SDK d’appareil IoT Hub dans la [documentation sur le développement d’appareil IoT](../iot-develop/about-iot-sdks.md).

## <a name="os-platform-and-hardware-compatibility"></a>Compatibilité des plateformes de système d’exploitation et du matériel

Les plateformes prises en charge pour les Kits de développement logiciel (SDK) se trouvent dans le document [Prise en charge des plateformes par les Kits de développement logiciel (SDK) Azure IoT](iot-hub-device-sdk-platform-support.md).

Pour plus d’informations sur la compatibilité des Kits de développement logiciel (SDK) avec un matériel particulier, consultez le [catalogue d’appareils Azure Certified pour IoT](https://devicecatalog.azure.com/) ou un référentiel spécifique.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="next-steps"></a>Étapes suivantes

Documentation pertinente relative au développement à l’aide des kit Azure IoT SDK :

* Découvrez comment [gérer la connectivité et la messagerie fiable](iot-hub-reliability-features-in-sdks.md) à l’aide des kits IoT Hub SDK.
* Découvrez comment [développer pour des plateformes mobiles](iot-hub-how-to-develop-for-mobile-devices.md) telles qu’iOS et Android.
* [Prise en charge des plateformes par le kit de développement logiciel (SDK) Azure IoT](iot-hub-device-sdk-platform-support.md)

Les autres rubriques de référence de ce Guide du développeur IoT Hub comprennent :

* [Points de terminaison IoT Hub](iot-hub-devguide-endpoints.md)
* [Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages](iot-hub-devguide-query-language.md)
* [Quotas et limitation](iot-hub-devguide-quotas-throttling.md)
* [Prise en charge de MQTT dans IoT Hub](iot-hub-mqtt-support.md)
* [Informations de référence sur l’API REST d’IoT Hub](/rest/api/iothub/)
