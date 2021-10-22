---
title: Vue d’ensemble des options de kits Azure IoT device SDK
description: Découvrez quel kit Azure IoT device SDK utiliser en fonction de votre rôle et de vos tâches de développement.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: e4f5d99caadaedf6389215fccb753aedd6620917
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129983966"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Vue d’ensemble des kits Azure IoT device SDK

Les kits Azure IoT device SDK contiennent un ensemble de bibliothèques de client d’appareils, d’exemples et de documentations. Les kits device SDK simplifient le processus de connexion par programmation des appareils à Azure IoT. Les kits SDK sont disponibles dans différents langages de programmation pour et avec la prise en charge de plusieurs RTOS pour appareils embarqués.

## <a name="why-use-an-azure-iot-device-sdk"></a>Pourquoi utiliser un kit Azure IoT device SDK ?

Les avantages offerts par l’utilisation d’un kit Azure IoT Device SDK par rapport à la création d’une couche de connexion personnalisée sont décrits ci-dessous :

| | Couche de connexion personnalisée | Kits Azure IoT device SDK |
| :-- | :-- | :-- |
| **Support** | Besoin de prendre en charge et de documenter votre solution | Accès au support technique de Microsoft (GitHub, Microsoft Q&R, Microsoft Docs, Équipes de support client) |
| **Nouvelles fonctionnalités** | Besoin d’ajouter manuellement de nouvelles fonctionnalités Azure | Peut immédiatement tirer parti des nouvelles fonctionnalités ajoutées |
| **Investissement** | Investir des centaines d’heures de développement incorporé pour concevoir, créer, tester et maintenir une version personnalisée | Possibilité de tirer parti d’outils open source gratuits. Le seul coût associé aux SDK est la courbe d’apprentissage. |

## <a name="which-sdk-should-i-use"></a>Quel kit SDK utiliser ?

Le principal facteur dans le choix d’un kit SDK est le matériel de l’appareil. Les appareils informatiques généraux, tels que les PC et les téléphones mobiles, contiennent des unités de microprocesseur (MPU) et disposent de ressources de calcul et de mémoire relativement élevées. Une classe spécialisée d’appareils, qui sont utilisés comme capteurs ou pour d’autres rôles à usage spécifique, contiennent des unités de microcontrôleur (MCU) et disposent de ressources de calcul et de mémoire relativement limitées. Ces appareils à ressources restreintes nécessitent des outils de développement et des kits SDK spécialisés. Le tableau suivant récapitule les différentes classes d’appareils et les kits SDK à utiliser pour le développement d’appareils.

|Classe d’appareil|Description|Exemples|Kits SDK|
|-|-|-|-|
|Appareils embarqués|Appareils basés sur MCU à usage spécial avec limitations en termes de calcul et de mémoire|Capteurs|[Kits SDK pour appareil embarqué](#embedded-device-sdks)|
|Autres|Comprend des appareils basés sur MPU à usage général avec des ressources de calcul et de mémoire plus importantes|PC, smartphone, Raspberry Pi|[Kits de développement logiciel (SDK) d’appareil](#device-sdks)|

> [!Note] 
> Pour plus d’informations sur les différentes catégories d’appareils, afin de vous permettre de choisir le meilleur kit SDK pour votre appareil, consultez [Types d’appareils Azure IoT](concepts-iot-device-types.md).

## <a name="device-sdks"></a>Kits de développement logiciel (SDK) d’appareil

Ces kits SDK peuvent s’exécuter sur un appareil basé sur MPU, tel qu’un PC, une tablette, un smartphone ou Raspberry Pi. Les kits SDK prennent en charge le développement en C et dans les langages managés modernes, notamment C#, Node.JS, Python et Java.

Les kits SDK sont disponibles dans **plusieurs langages**, ce qui vous permet de choisir celui qui convient le mieux à votre équipe et à votre scénario.

| Langage | Package | Source | Démarrages rapides | Exemples | Informations de référence |
| :-- | :-- | :-- | :-- | :-- | :-- |
| **.NET** | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-csharp) | [Exemples](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [Référence](/dotnet/api/microsoft.azure.devices.client) |
| **Python** | [pip](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-python) | [Exemples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | [Référence](/python/api/azure-iot-device) |
| **Node.js** | [npm](https://www.npmjs.com/package/azure-iot-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-nodejs) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) | [Référence](/javascript/api/azure-iot-device/) |
| **Java** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-java) | [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples) | [Référence](/java/api/com.microsoft.azure.sdk.iot.device) |
| **C** | [packages](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#getting-the-sdk) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-ansi-c) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-ansi-c) | [Exemples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples) | [Référence](/azure/iot-hub/iot-c-sdk-ref/) |

> [!WARNING]
> Le **kit SDK C** listé ci-dessus ne convient **pas** aux applications embarquées en raison de son modèle de gestion de thread et de mémoire. Pour les appareils embarqués, reportez-vous aux [kits SDK pour appareils embarqués](#embedded-device-sdks).

## <a name="embedded-device-sdks"></a>Kits SDK pour appareil embarqué

Ces kits SDK ont été conçus et créés pour s’exécuter sur des appareils avec des ressources de calcul et de mémoire limitées, et sont implémentés à l’aide du langage C.

Les kits SDK pour appareil embarqué sont disponibles pour **plusieurs systèmes d’exploitation**, ce qui vous permet de choisir celui qui convient le mieux à votre équipe et à votre scénario.

| RTOS | Kit SDK | Source | Exemples | Informations de référence |
| :-- | :-- | :-- | :-- | :-- | 
| **Azure RTOS** | Middleware Azure RTOS | [GitHub](https://github.com/azure-rtos/netxduo) | [Démarrages rapides](quickstart-devkit-mxchip-az3166.md) | [Référence](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot) | 
| **FreeRTOS** | Middleware FreeRTOS | [GitHub](https://github.com/Azure/azure-iot-middleware-freertos) | [Exemples](https://github.com/Azure-Samples/iot-middleware-freertos-samples) | [Référence](https://azure.github.io/azure-iot-middleware-freertos) |
| **Bare Metal** | Kit SDK Azure pour embarqué C | [GitHub](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot) | [Exemples](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md) | [Référence](https://azure.github.io/azure-sdk-for-c) |

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation des kits Azure IoT SDK pour appareils pour connecter des appareils basés sur MPU, consultez les articles suivants :

* [Démarrage rapide : Envoyer des données de télémétrie à IoT Central](quickstart-send-telemetry-central.md)
* [Démarrage rapide : Envoyer des données de télémétrie à IoT Hub](quickstart-send-telemetry-iot-hub.md)

Pour en savoir plus sur l’utilisation des kits Azure IoT SDK pour appareils embarqués pour connecter des appareils basés sur MCU à ressources limitées, consultez l’article suivant :
* [Démarrage rapide : Connecter un devkit MXCHIP AZ3166 à IoT Central](quickstart-devkit-mxchip-az3166.md)
