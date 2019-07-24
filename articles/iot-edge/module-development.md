---
title: Développer des modules pour Azure IoT Edge | Microsoft Docs
description: Développer des modules personnalisés pour Azure IoT Edge qui peuvent communiquer avec le runtime et IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5285490ca1a27494cbcd3ea3d6527b78c7d38c8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833436"
---
# <a name="develop-your-own-iot-edge-modules"></a>Développer vos propres modules IoT Edge

Les modules Azure IoT Edge peuvent se connecter à d’autres services Azure et contribuer à votre pipeline de données cloud plus volumineuses. Cet article explique comment vous pouvez développer des modules pour communiquer avec le runtime IoT Edge et IoT Hub, et par conséquent, avec le reste du cloud Azure. 

## <a name="iot-edge-runtime-environment"></a>Environnement d’exécution IoT Edge
Le runtime IoT Edge fournit l’infrastructure nécessaire pour intégrer les fonctionnalités de plusieurs modules IoT Edge et pour les déployer sur des appareils IoT Edge. À un niveau global, tout programme peut être empaqueté en tant que module IoT Edge. Toutefois, pour tirer pleinement parti des fonctionnalités de communication et de gestion d’IoT Edge, un programme qui s’exécute dans un module peut se connecter au hub IoT Edge local, intégré dans le runtime IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Utilisation du hub IoT Edge
Le hub IoT Edge fournit deux fonctionnalités principales : proxy d’IoT Hub et communications locales.

### <a name="iot-hub-primitives"></a>Primitives IoT Hub
IoT Hub considère une instance de module comme un appareil, dans le sens où :

* Elle a un jumeau de module, qui est distinct et isolé du [jumeau d’appareil](../iot-hub/iot-hub-devguide-device-twins.md) et des autres jumeaux de module de cet appareil.
* Elle peut envoyer des [messages appareil-à-cloud](../iot-hub/iot-hub-devguide-messaging.md).
* Elle peut recevoir des [méthodes directes](../iot-hub/iot-hub-devguide-direct-methods.md) ciblant spécifiquement son identité.

Actuellement, un module ne peut pas recevoir de messages cloud-à-appareil, ni utiliser la fonctionnalité de chargement de fichier.

Quand vous écrivez un module, vous pouvez utiliser [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md) pour vous connecter au hub IoT Edge et utiliser la fonctionnalité ci-dessus comme vous le feriez lors de l’utilisation d’IoT Hub avec une application d’appareil (la seule différence étant que, à partir du back-end de votre application, vous devez faire référence à l’identité du module plutôt qu’à l’identité de l’appareil).

### <a name="device-to-cloud-messages"></a>Messages appareil-à-cloud
Pour permettre le traitement complexe des messages appareil-à-cloud, le hub IoT Edge fournit un routage déclaratif des messages entre les modules, et entre les modules et IoT Hub. Ce routage déclaratif permet aux modules d’intercepter et de traiter les messages envoyés par d’autres modules, et de les propager dans des pipelines complexes. Pour plus d’informations, consultez [Déployer des modules et établir des routes dans IoT Edge](module-composition.md).

Un module IoT Edge, contrairement à une application d’appareil IoT Hub normale, peut recevoir des messages appareil-à-cloud qui sont traités en proxy par son hub IoT Edge local, afin de les traiter.

Le hub IoT Edge propage les messages vers votre module en fonction des routes déclaratives décrites dans le [manifeste de déploiement](module-composition.md). Quand vous développez un module IoT Edge, vous pouvez recevoir ces messages en définissant des gestionnaires de messages.

Pour simplifier la création de routes, IoT Edge ajoute le concept de points de terminaison *d’entrée* et *de sortie* de module. Un module peut recevoir tous les messages appareil-à-cloud qui lui sont envoyés sans spécifier d’entrée, et peut envoyer des messages appareil-à-cloud sans spécifier de sortie. L’utilisation d’entrées et de sorties explicites rend toutefois les règles de routage plus simples à comprendre. 

Pour finir, les messages appareil-à-cloud gérés par le hub Edge sont marqués avec les propriétés système suivantes :

| Propriété | Description |
| -------- | ----------- |
| $connectionDeviceId | ID d’appareil du client ayant envoyé le message. |
| $connectionModuleId | ID de module du module ayant envoyé le message. |
| $inputName | Entrée ayant reçu ce message. Peut être vide. |
| $outputName | Sortie utilisée pour envoyer le message. Peut être vide. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Connexion au hub IoT Edge à partir d’un module
La connexion au hub IoT Edge local à partir d’un module passe par deux étapes : 
1. Créez une instance ModuleClient dans votre application.
2. Assurez-vous que votre application accepte le certificat présenté par le hub IoT Edge sur l’appareil.

Créez une instance ModuleClient pour connecter votre module au hub IoT Edge en cours d’exécution sur l’appareil, de la même façon que les instances DeviceClient connectent les appareils IoT à IoT Hub. Pour plus d’informations sur la classe ModuleClient et ses méthodes de communication, reportez-vous aux informations de référence sur l’API du langage de votre SDK préféré : [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C et Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) ou [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).


## <a name="next-steps"></a>Étapes suivantes

[Préparer votre environnement de développement et de test pour IoT Edge](development-environment.md)

[Utiliser Visual Studio pour développer des modules C# pour IoT Edge](how-to-visual-studio-develop-module.md)

[Utiliser Visual Studio Code pour développer des modules pour IoT Edge](how-to-vs-code-develop-module.md)

