---
title: Découvrir les options de connexion pour les développeurs d’appareils Azure IoT
description: Découvrez les outils et options de connexion des appareils couramment utilisés pour les développeurs d’appareils Azure IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100656316"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Vue d’ensemble : options de connexion pour les développeurs d’appareils Azure IoT
En tant que développeur d’appareils, vous disposez de plusieurs options pour la connexion et la gestion des appareils Azure IoT. Cet article présente les options et les outils les plus couramment utilisés qui vous aident à connecter et à gérer les appareils.

Au moment d’évaluer les options de connexion Azure IoT pour vos appareils, il est utile de comparer les éléments suivants :
- Plateformes d’application d’appareil Azure IoT
- Outils de connexion et de gestion des appareils

## <a name="application-platforms-iot-central-and-iot-hub"></a>Plateformes d’application : IoT Central et IoT Hub
Azure IoT contient deux services qui sont des plateformes pour les applications cloud compatibles avec les appareils : Azure IoT Central et Azure IoT Hub. Vous pouvez utiliser l’un ou l’autre pour héberger une application IoT et connecter des appareils.
- [IOT Central](../iot-central/core/overview-iot-central.md) est conçu pour réduire la complexité et le coût de l’utilisation des solutions IoT. Il s’agit d’une application SaaS (Software-as-a-Service) qui fournit une plateforme complète pour l’hébergement d’applications IoT. Vous pouvez utiliser l’interface utilisateur web d’IoT Central pour simplifier la totalité du cycle de vie de la création et de la gestion des applications IoT. L’interface utilisateur web simplifie les tâches de création d’applications ainsi que de connexion et de gestion d’une poignée comme de millions d’appareils. IoT Central utilise IoT Hub pour créer et gérer des applications, tout en gardant les détails transparents pour l’utilisateur. En général, IoT Central permet de réduire la complexité et l’effort de développement ainsi que de simplifier la gestion des appareils par le biais de l’interface utilisateur web.
- [IoT Hub](../iot-hub/about-iot-hub.md) fait office de hub de messages central pour la communication bidirectionnelle entre les applications IoT et les appareils connectés. Il s’agit d’une application PaaS (Platform as a Service) qui fournit également une plateforme pour l’hébergement d’applications IoT. Comme IoT Central, elle peut être mise à l’échelle pour prendre en charge des millions d’appareils. En général, IoT Hub offre davantage de contrôle et de personnalisation pour la conception des applications ainsi que plus d’options d’outils de développement pour l’utilisation du service, au détriment d’une plus grande complexité de développement et de gestion.

## <a name="tools-to-connect-and-manage-devices"></a>Outils pour la connexion et la gestion des appareils
Une fois que vous avez sélectionné IoT Hub ou IoT Central pour héberger votre application IoT, vous disposez de plusieurs options pour les outils de développement. Vous pouvez utiliser ces outils pour vous connecter à la plateforme d’applications IoT que vous avez sélectionnée et pour créer et gérer des applications et des appareils. Les options d’outil courantes sont décrites dans le tableau suivant. 

> [!NOTE]
> Outre les outils suivants, vous pouvez créer et gérer par programmation des applications IoT avec des API REST, des SDK Azure ou des modèles Azure Resource Manager. Pour plus d’informations, consultez la documentation sur les services [IOT Hub](../iot-hub/about-iot-hub.md) et [IOT Central](../iot-central/core/overview-iot-central.md).

|Outil  |Plateforme IoT prise en charge &nbsp; &nbsp; &nbsp; &nbsp; |Documentation  |Description  |
|---------|---------|---------|---------|
|Interface utilisateur web de Central     | Central | [Guide de démarrage rapide sur IoT Central](../iot-central/core/quick-deploy-iot-central.md) | Portail basé sur un navigateur pour IoT Central. |
|Portail Azure     | Hub, Central      | [Créer un hub IoT avec le portail Azure](../iot-hub/iot-hub-create-through-portal.md), [Gérer IoT Central à l’aide du portail Azure](../iot-central/core/howto-manage-iot-central-from-portal.md)| Portail basé sur un navigateur pour IoT Hub et les appareils. Fonctionne également avec d’autres ressources Azure, y compris IoT Central. |
|Azure CLI     | Hub, Central          | [Créer un hub IoT avec l’interface CLI](../iot-hub/iot-hub-create-using-cli.md), [Gérer IoT Central à partir d’Azure CLI](../iot-central/core/howto-manage-iot-central-from-cli.md) | Interface de ligne de commande pour la création et la gestion d’applications IoT. |
|Azure PowerShell     | Hub, Central   | [Créer un hub IoT avec PowerShell](../iot-hub/iot-hub-create-using-powershell.md), [Gérer IoT Central à partir d’Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | Interface PowerShell pour la création et la gestion d’applications IoT |
|Azure IoT Tools pour VS Code  | Hub | [Créer un hub IoT avec Tools pour VS Code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | Extension VS Code pour les applications IoT Hub. |
|Explorateur Azure IoT     | Hub | [Explorateur Azure IoT](https://github.com/Azure/azure-iot-explorer) | Ne peut pas créer de hubs IoT. Établit une connexion à un hub IoT existant pour gérer des appareils. Souvent utilisé avec l’interface CLI ou le portail.|

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les options de connexion des appareils à Azure IoT, explorez les guides de démarrage rapide suivants :
- IoT Central : [Créer une application Azure IoT Central](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub : [Envoyer des données de télémétrie d’un appareil à un hub IoT et les surveiller avec Azure CLI](../iot-hub/quickstart-send-telemetry-cli.md)