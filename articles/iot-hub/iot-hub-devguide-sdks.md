---
title: Comprendre les kits IoT Azure SDK | Microsoft Docs
description: Guide du développeur - informations et liens vers divers kits Azure IoT device et service SDK que vous pouvez utiliser pour créer des applications d’appareil et des applications principales.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71d02e3f8bf0757d7ea0622e76b95acda1fefc95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
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

> [!NOTE]
> Consultez les fichiers lisez-moi dans les dépôts GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilité des plateformes de système d’exploitation et du matériel

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

> [!NOTE]
> Consultez les fichiers lisez-moi dans les dépôts GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.


## <a name="next-steps"></a>Étapes suivantes

Les autres rubriques de référence de ce Guide du développeur IoT Hub comprennent :

* [Points de terminaison IoT Hub][lnk-devguide-endpoints]
* [Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages][lnk-devguide-query]
* [Quotas et limitation][lnk-devguide-quotas]
* [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt]
* [Informations de référence sur l’API REST d’IoT Hub][lnk-rest-ref]

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
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
