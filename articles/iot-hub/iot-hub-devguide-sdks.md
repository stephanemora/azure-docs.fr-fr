---
title: Comprendre les kits IoT Azure SDK | Microsoft Docs
description: Guide du développeur - informations et liens vers divers kits Azure IoT device et service SDK que vous pouvez utiliser pour créer des applications d’appareil et des applications principales.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: 1eeb0afdd5ffcbe00357914d6a98c8d0b3d452ec
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017957"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Comprendre et utiliser les kits Azure IoT Hub SDK

Il existe trois catégories de kits de développement logiciel (SDK) permettant de travailler avec IoT Hub :

* Les **Kits de développement logiciel (SDK) d’appareil** vous permettent de générer des applications qui s’exécutent sur vos appareils IoT à l’aide d’un client d’appareil ou d’un client de module. Ces applications envoient des données de télémétrie à votre hub IoT et reçoivent le cas échéant des messages ou des mises à jour de tâche, de méthode ou de jumeau de ce hub IoT.  Vous pouvez également utiliser un client de module afin de créer des [modules](../iot-edge/iot-edge-modules.md) pour le [runtime Azure IoT Edge](../iot-edge/about-iot-edge.md).

* Les **Kits de développement logiciel (SDK) de service** vous permettent de gérer votre hub IoT et, si vous le souhaitez, d’envoyer des messages, de planifier des tâches, d’appeler des méthodes directes ou d’envoyer les mises à jour de propriétés souhaitées à vos modules ou appareils IoT.

* Les **Kits de développement logiciel (SDK) de provisionnement des appareils** vous permettent d’ajouter des appareils à votre hub IoT à l’aide du [Service Device Provisioning](../iot-dps/about-iot-dps.md).

Découvrez les [avantages du développement avec les Kits de développement logiciel (SDK) Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Kits Azure IoT device SDK

Les kits Microsoft Azure IoT device SDK contiennent du code qui facilite la création d’appareils et d’applications qui se connectent aux services Azure IoT Hub et sont gérés par eux.

Kit Azure IoT device SDK pour .NET : 

* Installation à partir de [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Code source](https://github.com/Azure/azure-iot-sdk-csharp)
* [Informations de référence sur l'API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Informations de référence sur les modules](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Kit Azure IoT Hub device SDK pour C : écrit en ANSI C (C99) pour la portabilité et la compatibilité de nombreuses plateformes :

* Installation à partir de [apt-get, MBED, Arduino IDE ou Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Code source](https://github.com/Azure/azure-iot-sdk-c)
* [Informations de référence sur l'API](https://azure.github.io/azure-iot-sdk-c/index.html)
* [Informations de référence sur les modules](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

Kit Azure IoT device SDK pour Java : 

* Ajout au projet [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Code source](https://github.com/Azure/azure-iot-sdk-java)
* [Informations de référence sur l'API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Informations de référence sur les modules](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

Kit Azure IoT device SDK pour Node.js : 

* Installation à partir de [npm](https://www.npmjs.com/package/azure-iot-device)
* [Code source](https://github.com/Azure/azure-iot-sdk-node)
* [Informations de référence sur l'API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Informations de référence sur les modules](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Kit Azure IoT device SDK pour Python : 

* Installation à partir de [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Code source](https://github.com/Azure/azure-iot-sdk-python)
* Informations de référence sur l’API : voir [Informations de référence sur l’API C](https://azure.github.io/azure-iot-sdk-c/index.html)

Kit Azure IoT Hub device SDK pour iOS : 

* Installation à partir de [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Exemples](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Informations de référence sur l’API : voir [Informations de référence sur l’API C](https://azure.github.io/azure-iot-sdk-c/index.html)

> [!NOTE]
> Consultez les fichiers lisez-moi dans les dépôts GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilité des plateformes de système d’exploitation et du matériel

Les plateformes prises en charge pour les Kits de développement logiciel (SDK) se trouvent dans le document [Prise en charge des plateformes par les Kits de développement logiciel (SDK) Azure IoT](iot-hub-device-sdk-platform-support.md).

Pour plus d’informations sur la compatibilité des Kits de développement logiciel (SDK) avec un matériel particulier, consultez le [catalogue d’appareils Azure Certified pour IoT](https://catalog.azureiotsuite.com/) ou un référentiel spécifique.

## <a name="azure-iot-service-sdks"></a>Kits Azure IoT service SDK

Les kits Azure IoT service SDK contiennent du code pour faciliter la création d’applications qui interagissent directement avec IoT Hub pour gérer les appareils et la sécurité.

Kit Azure IoT Hub service SDK pour .NET :

* Téléchargement à partir de [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
* [Code source](https://github.com/Azure/azure-iot-sdk-csharp)
* [Informations de référence sur l'API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Kit Azure IoT Hub service SDK pour Java : 

* Ajout au projet [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
)
* [Code source](https://github.com/Azure/azure-iot-sdk-java)
* [Informations de référence sur l'API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Kit Azure IoT Hub service SDK pour Node.js : 

* Téléchargement à partir de [npm](https://www.npmjs.com/package/azure-iothub)
* [Code source](https://github.com/Azure/azure-iot-sdk-node)
* [Informations de référence sur l'API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Kit Azure IoT Hub service SDK pour Python : 

* Téléchargement à partir de [pip](https://pypi.python.org/pypi/azure-iothub-service-client/)
* [Code source](https://github.com/Azure/azure-iot-sdk-python)

Kit Azure IoT Hub service SDK pourC : 

* Téléchargement à partir de [apt-get, MBED, Arduino IDE ou Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Code source](https://github.com/Azure/azure-iot-sdk-c)

Kit Azure IoT Hub service SDK pour iOS : 

* Installation à partir de [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Exemples](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Consultez les fichiers lisez-moi dans les dépôts GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.

## <a name="device-provisioning-sdks"></a>Kits de développement logiciel (SDK) d’approvisionnement d’appareil

Les **Kits de développement logiciel (SDK) de provisionnement Microsoft Azure** vous permettent d’ajouter des appareils à votre hub IoT à l’aide du [Service Device Provisioning](../iot-dps/about-iot-dps.md).

Kits de développement logiciel (SDK) de services et d’appareils d’approvisionnement Azure pour C# :

* [Kit de développement logiciel (SDK) client d’appareil d’approvisionnement](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [Kit de développement logiciel (SDK) client de service d’approvisionnement](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

Kits de développement logiciel (SDK) de services et d’appareils d’approvisionnement Azure pour Java :

* [Kit de développement logiciel (SDK) client d’appareil d’approvisionnement](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client)
* [Kit de développement logiciel (SDK) client de service d’approvisionnement](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

Kits de développement logiciel (SDK) de services et d’appareils d’approvisionnement Azure pour Node.js :

* [Kit de développement logiciel (SDK) client d’appareil d’approvisionnement](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [Kit de développement logiciel (SDK) client de service d’approvisionnement](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

Kits de développement logiciel (SDK) de services et d’appareils d’approvisionnement Azure pour Python :

* [Kit de développement logiciel (SDK) client d’appareil d’approvisionnement](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [Kit de développement logiciel (SDK) client de service d’approvisionnement](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

Kits de développement logiciel (SDK) de services et d’appareils d’approvisionnement Azure pour C :

* [Kit de développement logiciel (SDK) client d’appareil d’approvisionnement](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Kit de développement logiciel (SDK) client de service d’approvisionnement](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client)

## <a name="next-steps"></a>Étapes suivantes

Les kits de développement logiciel IoT d’Azure fournissent également un ensemble d’outils pour faciliter le développement :
* [iothub-diagnostic](https://github.com/Azure/iothub-diagnostics) : outil de ligne de commande multiplateforme permettant de diagnostiquer les problèmes liés à la connexion avec IoT Hub.
* [device-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) : application de bureau Windows permettant de se connecter à son IoT Hub.

Les autres rubriques de référence de ce Guide du développeur IoT Hub comprennent :

* [Points de terminaison IoT Hub](iot-hub-devguide-endpoints.md)
* [Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages](iot-hub-devguide-query-language.md)
* [Quotas et limitation](iot-hub-devguide-quotas-throttling.md)
* [Prise en charge de MQTT dans IoT Hub](iot-hub-mqtt-support.md)
* [Informations de référence sur l’API REST d’IoT Hub](/rest/api/iothub/)
* [Prise en charge des plateformes par le kit de développement logiciel Azure IoT](iot-hub-device-sdk-platform-support.md)