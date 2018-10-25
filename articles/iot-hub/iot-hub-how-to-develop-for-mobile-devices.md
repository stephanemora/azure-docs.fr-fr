---
title: Développer pour les appareils mobiles à l’aide des kits SDK Azure IoT | Microsoft Docs
description: 'Guide du développeur : découvrez comment développer pour les appareils mobiles à l’aide des kits SDK Azure IoT Hub.'
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 4a94abe69b525dc1b03fe2c1ae9593f3c6399f56
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339751"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Développer pour les appareils mobiles à l’aide des kits SDK Azure IoT

Les « objets » de l’Internet des objets peuvent désigner un large éventail d’appareils dotés de capacités diverses, tels que les capteurs, les microcontrôleurs, les smart devices, les passerelles industrielles et même les appareils mobiles.  Un appareil mobile peut être un appareil IoT qui envoie des données de télémétrie appareil-à-cloud et est géré par le cloud.  Il peut également faire office d’appareil exécutant une application de service principal qui gère les autres appareils IoT.  Dans les deux cas, les [kits SDK Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) peuvent être utilisés pour développer des applications fonctionnant sur appareil mobile.  

## <a name="develop-for-native-ios-platform"></a>Développer pour la plateforme iOS native

Les kits SDK Azure IoT Hub assurent la prise en charge de la plateforme iOS native via le SDK Azure IoT Hub pour C.  Celui-ci peut être considéré comme un kit SDK iOS incorporable dans un projet XCode Swift ou Objective-C.  Le kit SDK pour C peut être utilisé de deux façons sur iOS :

* Utiliser les bibliothèques CocoaPod dans le projet XCode directement.  

* Télécharger le code source pour le kit SDK C et générer pour la plateforme iOS en suivant les [instructions de génération](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) pour MacOS.  

Le kit SDK Azure IoT Hub pour C est écrit en C99 pour une portabilité maximale sur les différentes plateformes.  Le processus de portage implique l’écriture d’une fine couche d’adoption pour les composants propres à la plateforme en question, disponible ici pour [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Les fonctionnalités du SDK C peuvent être utilisées sur la plateforme iOS, notamment les primitives Azure IoT Hub prises en charge et les fonctionnalités spécifiques du SDK, telles que la stratégie de nouvelles tentatives pour la fiabilité du réseau.  L’interface du SDK iOS est également similaire à l’interface du SDK Azure IoT Hub C.  

Les documents qui suivent vous guident dans le développement d’une application d’appareil ou d’une application de service sur un appareil iOS :

* [Démarrage rapide : envoyer des données de télémétrie depuis un appareil à un hub IoT](quickstart-send-telemetry-ios.md)  

* [Envoyer des messages du cloud à votre appareil avec un hub IoT](iot-hub-ios-swift-c2d.md) 

## <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Développer avec les bibliothèques CocoaPod Azure IoT Hub

Les SDK Azure IoT Hub s’accompagnent d’un ensemble de bibliothèques CocoaPod Objective-C pour le développement iOS.  Pour obtenir la dernière liste en date des bibliothèques CocoaPod, consultez [CocoaPods for Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Une fois les bibliothèques appropriées incorporées dans votre projet XCode, deux options s’offrent à vous pour écrire le code IoT Hub associé :

* Fonction Objective-C : si votre projet est écrit en Objective-C, vous pouvez appeler des API directement à partir du SDK Azure IoT Hub C.  Si votre projet est écrit en Swift, vous pouvez appeler `@objc func` avant de créer votre fonction et procéder à l’écriture de toute la logique liée à Azure IoT Hub à l’aide de code C ou Objective-C.  Vous trouverez un ensemble d’exemples illustrant les deux cas de figure dans l’[exemple de dépôt](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Incorporation d’exemples en C : si vous avez écrit une application d’appareil en C, vous pouvez la référencer directement dans votre projet XCode :

    * Ajoutez le fichier sample.c à votre projet XCode dans XCode.  
    
    * Ajoutez le fichier d’en-tête à votre dépendance.  Un fichier d’en-tête est inclus dans l’[exemple de dépôt](https://github.com/Azure-Samples/azure-iot-samples-ios) à titre d’illustration. Pour plus d’informations, consultez la page de documentation d’Apple relative à [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="next-steps"></a>Étapes suivantes

* [Informations de référence sur l’API REST d’IoT Hub](https://docs.microsoft.com/rest/api/iothub/)
* [Code source du SDK C Azure IoT](https://github.com/Azure/azure-iot-sdk-c)
