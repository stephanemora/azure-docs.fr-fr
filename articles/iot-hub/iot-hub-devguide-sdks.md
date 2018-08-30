---
title: Comprendre les kits IoT Azure SDK | Microsoft Docs
description: Guide du développeur - informations et liens vers divers kits Azure IoT device et service SDK que vous pouvez utiliser pour créer des applications d’appareil et des applications principales.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: dobett
ms.openlocfilehash: 710de8021abfa5b1fc17491af6b8b9f2bdd3a19f
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42919035"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Comprendre et utiliser les kits Azure IoT Hub SDK

Il existe deux catégories de kits de développement logiciel (SDK) permettant de travailler avec IoT Hub :

* Les kits **device SDK** vous permettent de créer des applications qui s’exécuteront sur vos appareils IoT. Ces applications envoient des données de télémétrie à votre hub IoT et reçoivent le cas échéant des messages ou des mises à jour de tâche, de méthode ou de jumeau de ce hub IoT.

* Les kits **Service SDK** vous permettent de gérer votre hub IoT et, si vous le souhaitez, d’envoyer des messages, de planifier des tâches, d’appeler des méthodes directes ou d’envoyer des mises à jour de propriétés souhaitées à vos appareils IoT.

Découvrez les avantages du développement avec les kits Azure IoT SDK [ici][lnk-benefits-blog].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Kits Azure IoT device SDK

Les kits Microsoft Azure IoT device SDK contiennent du code qui facilite la création d’appareils et d’applications qui se connectent aux services Azure IoT Hub et sont gérés par eux.

Kit Azure IoT device SDK pour .NET : 
* Installation à partir de [Nuget][lnk-nuget-csharp-device]
* [Code source][lnk-dotnet-sdk]
* [Informations de référence sur l’API][lnk-dotnet-ref]

Kit Azure IoT Hub device SDK pour C : écrit en ANSI C (C99) pour la portabilité et la compatibilité de nombreuses plateformes
* Installation à partir de [apt-get, MBED, Arduino IDE ou Nuget][lnk-c-package]
* [Code source][lnk-c-sdk]
* [Informations de référence sur l’API][lnk-c-ref]

Kit Azure IoT device SDK pour Java : 
* Ajout au projet [Maven][lnk-maven-device]
* [Code source][lnk-java-sdk]
* [Informations de référence sur l’API][lnk-java-ref]

Kit Azure IoT device SDK pour Node.js : 
* Installation à partir de [npm][lnk-npm-device]
* [Code source][lnk-node-sdk]
* [Informations de référence sur l’API][lnk-node-ref]

Kit Azure IoT device SDK pour Python : 
* Installation à partir de [pip][lnk-pip-device]
* [Code source][lnk-python-sdk]

Kit Azure IoT Hub device SDK pour iOS : 
* Installation à partir de [CocoaPod][lnk-cocoa-device]
* [Exemples][lnk-ios-sample]

> [!NOTE]
> Consultez les fichiers lisez-moi dans les dépôts GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilité des plateformes de système d’exploitation et du matériel

Les plateformes supportées pour les kits de développement logiciel se trouvent dans ce [document](iot-hub-device-sdk-platform-support.md).
Pour plus d’informations sur la compatibilité des kits SDK avec un matériel particulier, consultez le [catalogue d’appareils Azure Certified pour IoT][lnk-certified] ou un référentiel spécifique.

## <a name="azure-iot-service-sdks"></a>Kits Azure IoT service SDK

Les kits Azure IoT service SDK contiennent du code pour faciliter la création d’applications qui interagissent directement avec IoT Hub pour gérer les appareils et la sécurité.

Kit Azure IoT Hub service SDK pour .NET :
* Téléchargement à partir de [Nuget][lnk-nuget-csharp-service]
* [Code source][lnk-dotnet-sdk]
* [Informations de référence sur l’API][lnk-dotnet-service-ref]

Kit Azure IoT Hub service SDK pour Java : 
* Ajout au projet [Maven][lnk-maven-service]
* [Code source][lnk-java-sdk]
* [Informations de référence sur l’API][lnk-java-service-ref]

Kit Azure IoT Hub service SDK pour Node.js : 
* Téléchargement à partir de [npm][lnk-npm-service]
* [Code source][lnk-node-sdk]
* [Informations de référence sur l’API][lnk-node-service-ref]

Kit Azure IoT Hub service SDK pour Python : 
* Téléchargement à partir de [pip][lnk-pip-service]
* [Code source][lnk-python-sdk]

Kit Azure IoT Hub service SDK pourC : 
* Téléchargement à partir de [apt-get, MBED, Arduino IDE ou Nuget][lnk-c-package]
* [Code source][lnk-c-sdk]

Kit Azure IoT Hub service SDK pour iOS : 
* Installation à partir de [CocoaPod][lnk-cocoa-service]
* [Exemples][lnk-ios-sample]

> [!NOTE]
> Consultez les fichiers lisez-moi dans les dépôts GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.



## <a name="next-steps"></a>Étapes suivantes

Les kits de développement logiciel IoT d’Azure fournissent également un ensemble d’outils pour faciliter le développement :
* [iothub-diagnostic](https://github.com/Azure/iothub-diagnostics) : outil de ligne de commande multiplateforme permettant de diagnostiquer les problèmes liés à la connexion avec IoT Hub.
* [device-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) : application de bureau Windows permettant de se connecter à son IoT Hub.

Les autres rubriques de référence de ce Guide du développeur IoT Hub comprennent :

* [Points de terminaison IoT Hub][lnk-devguide-endpoints]
* [Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages][lnk-devguide-query]
* [Quotas et limitation][lnk-devguide-quotas]
* [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt]
* [Informations de référence sur l’API REST d’IoT Hub][lnk-rest-ref]
* [Prise en charge des plateformes par le kit de développement logiciel Azure IoT](iot-hub-device-sdk-platform-support.md)

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
[lnk-cocoa-device]: https://cocoapods.org/pods/AzureIoTHubClient
[lnk-ios-sample]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-cocoa-service]: https://cocoapods.org/pods/AzureIoTHubServiceClient
