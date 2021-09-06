---
title: Architecture de référence pour une solution de suivi de la qualité de l’eau créée avec Azure IoT Central | Microsoft Docs
description: Découvrez les concepts qui se rapportent à une solution de suivi de la qualité de l’eau conçue avec Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 83906682a188a0d6abf859589a38311a0d726a05
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229809"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Architecture de référence d’une solution de suivi de la qualité de l’eau 

Vous pouvez créer des solutions de suivi de la qualité de l’eau avec le **modèle d’application Azure IoT Central** en tant qu’application IoT de démarrage. Cet article fournit des conseils d’ordre général sur l’architecture de référence d’une solution de bout en bout. 

![Architecture du suivi de la qualité de l’eau](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Concepts :

1. Appareils et connectivité  
1. IoT Central
1. Extensibilité et intégrations
1. Applications métier

Cet article décrit les composants clés qui ont généralement un rôle à jouer dans une solution de suivi de la qualité de l’eau.

## <a name="devices-and-connectivity"></a>Appareils et connectivité

Les solutions de gestion de l’eau utilisent des systèmes d’eau intelligents tels que des débitmètres, des moniteurs de qualité de l’eau, des vannes intelligentes, des détecteurs de fuite.

Les appareils des solutions d’eau intelligentes peuvent se connecter via des réseaux LPWAN ou via un opérateur réseau tiers. Pour ces types d’appareils, utilisez [Azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) pour envoyer les données de votre appareil à votre application IoT dans Azure IoT Central. Vous pouvez également utiliser des passerelles d’appareil qui sont compatibles IP et capables de se connecter directement à IoT Central.

## <a name="iot-central"></a>IoT Central

Azure IoT Central est une plateforme d’applications IoT qui vous permet de créer et de déployer rapidement une solution IoT. Vous pouvez personnaliser et intégrer votre solution à des services tiers.

Lorsque vous connectez vos systèmes d’eau intelligents à IoT Central, l’application permet de contrôler les appareils, d’effectuer le suivi et de déclencher des alertes, d’accéder à l’interface utilisateur à laquelle est intégré RBAC et d’accéder aux tableaux de bord configurables, ainsi qu’aux options d’extensibilité.

## <a name="extensibility-and-integrations"></a>Extensibilité et intégrations

Vous pouvez étendre votre application IoT dans IoT Central et si vous le souhaitez :

* Transformer et intégrer vos données IoT en vue d’une analytique avancée, par exemple en entraînant des modèles Machine Learning via l’exportation continue de données à partir d’une application IoT Central
* Automatiser des workflows dans d’autres systèmes en déclenchant des actions via Power Automate ou des webhooks à partir d’une application IoT Central
* Accéder programmatiquement à votre application IoT dans IoT Central via des API IoT Central

## <a name="business-applications"></a>Applications métier

Vous pouvez utiliser les données IoT pour alimenter diverses applications métier au sein d’une solution pour l’eau. Dans votre [application de surveillance de la consommation d’eau IoT Central](tutorial-water-consumption-monitoring.md), vous pouvez configurer des règles et des actions, et les définir pour créer des alertes dans le [Connected Field Service](/dynamics365/field-service/connected-field-service). Configurez Power Automate dans les règles d’IoT Central pour automatiser les flux de travail entre les applications et les services. Par ailleurs, selon les activités de service dans Connected Field Service, des informations peuvent être renvoyées à Azure IoT Central.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer une application IoT Central de suivi de la qualité de l’eau](./tutorial-water-quality-monitoring.md)
* Apprenez-en davantage sur les [modèles IoT Central Government](./overview-iot-central-government.md)
* Pour plus d’informations sur IoT Central, consultez la [vue d’ensemble d’IoT Central](../core/overview-iot-central.md)
