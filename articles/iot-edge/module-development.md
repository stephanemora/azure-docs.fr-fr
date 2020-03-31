---
title: Développer des modules pour Azure IoT Edge | Microsoft Docs
description: Développer des modules personnalisés pour Azure IoT Edge qui peuvent communiquer avec le runtime et IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79233285"
---
# <a name="develop-your-own-iot-edge-modules"></a>Développer vos propres modules IoT Edge

Les modules Azure IoT Edge peuvent se connecter à d’autres services Azure et contribuer à votre pipeline de données cloud plus volumineuses. Cet article explique comment vous pouvez développer des modules pour communiquer avec le runtime IoT Edge et IoT Hub, et par conséquent, avec le reste du cloud Azure.

## <a name="iot-edge-runtime-environment"></a>Environnement d’exécution IoT Edge

Le runtime IoT Edge fournit l’infrastructure nécessaire pour intégrer les fonctionnalités de plusieurs modules IoT Edge et pour les déployer sur des appareils IoT Edge. Tout programme peut être empaqueté en tant que module IoT Edge. Pour tirer pleinement parti des fonctionnalités de communication et de gestion d’IoT Edge, un programme qui s’exécute dans un module peut utiliser Azure IoT Device SDK pour se connecter au hub IoT Edge local.

## <a name="using-the-iot-edge-hub"></a>Utilisation du hub IoT Edge

Le hub IoT Edge fournit deux fonctionnalités principales : proxy d’IoT Hub et communications locales.

### <a name="iot-hub-primitives"></a>Primitives IoT Hub

IoT Hub considère une instance de module comme un appareil, dans le sens où :

* Elle a un jumeau de module, qui est distinct et isolé du [jumeau d’appareil](../iot-hub/iot-hub-devguide-device-twins.md) et des autres jumeaux de module de cet appareil.
* Elle peut envoyer des [messages appareil-à-cloud](../iot-hub/iot-hub-devguide-messaging.md).
* Elle peut recevoir des [méthodes directes](../iot-hub/iot-hub-devguide-direct-methods.md) ciblant spécifiquement son identité.

Actuellement, les modules ne peuvent pas recevoir de messages cloud-à-appareil, ni utiliser la fonctionnalité de chargement de fichier.

Lors de l’écriture d’un module, vous pouvez utiliser [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) pour vous connecter au hub IoT Edge et utiliser la fonctionnalité ci-dessus comme vous le feriez lors de l’utilisation d’IoT Hub avec une application d’appareil. La seule différence entre les modules IoT Edge et les applications d’appareil IoT est que vous devez vous référer à l’identité du module plutôt qu’à l’identité de l’appareil.

### <a name="device-to-cloud-messages"></a>Messages appareil-à-cloud

Pour permettre le traitement complexe des messages appareil-à-cloud, le hub IoT Edge fournit un routage déclaratif des messages entre les modules, et entre les modules et IoT Hub. Ce routage déclaratif permet aux modules d’intercepter et de traiter les messages envoyés par d’autres modules, et de les propager dans des pipelines complexes. Pour plus d’informations, consultez [Déployer des modules et établir des routes dans IoT Edge](module-composition.md).

Un module IoT Edge, contrairement à une application d’appareil IoT Hub normale, peut recevoir des messages appareil-à-cloud qui sont traités en proxy par son hub IoT Edge local afin de les traiter.

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

Créez une instance ModuleClient pour connecter votre module au hub IoT Edge en cours d’exécution sur l’appareil, de la même façon que les instances DeviceClient connectent les appareils IoT à IoT Hub. Pour plus d’informations sur la classe ModuleClient et ses méthodes de communication, reportez-vous aux informations de référence sur l’API du langage de votre SDK préféré : [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) ou [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Prise en charge du langage et de l’architecture

IoT Edge prend en charge plusieurs systèmes d’exploitation, architectures de périphérique et langages de développement afin que vous puissiez générer le scénario qui correspond à vos besoins. Utilisez cette section pour comprendre les options de développement de modules de IoT Edge personnalisés. Vous pouvez en savoir plus sur la prise en charge des outils et les exigences pour chaque langage dans [Préparer votre environnement de développement et de test pour IOT Edge](development-environment.md).

### <a name="linux"></a>Linux

Pour toutes les langues dans le tableau suivant, IoT Edge prend en charge le développement pour les appareils AMD64 et ARM32 Linux.

| Langage de développement | Outils de développement |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>La prise en charge du développement et du débogage des appareils Linux ARM64 est disponible en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour plus d’informations, consultez [Développer et déboguer des modules IoT Edge ARM64 dans Visual Studio Code (préversion)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Pour toutes les langues dans le tableau suivant, IoT Edge prend en charge le développement pour les appareils Windows AMD64.

| Langage de développement | Outils de développement |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (aucune fonctionnalité de débogage)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Étapes suivantes

[Préparer votre environnement de développement et de test pour IoT Edge](development-environment.md)

[Utiliser Visual Studio pour développer des modules C# pour IoT Edge](how-to-visual-studio-develop-module.md)

[Utiliser Visual Studio Code pour développer des modules pour IoT Edge](how-to-vs-code-develop-module.md)

[Comprendre et utiliser les kits Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md)
