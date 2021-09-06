---
title: Architecture de référence pour une solution de gestion des déchets créée avec Azure IoT Central | Microsoft Docs
description: Découvrez les concepts qui se rapportent à une solution de gestion des déchets conçue avec Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 60fe5b51ffa04de79598f3f4744a8bf2bb57966a
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229896"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Architecture de référence d’une solution connectée de suivi des déchets 

Vous pouvez créer des solutions de gestion des déchets connectées à l’aide du **modèle d’application Azure IoT Central** en tant qu’application IoT de démarrage. Cet article donne des conseils d’ordre général sur l’architecture de référence d’une solution de bout en bout.

![Architecture de gestion des déchets connectée](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)

Concepts :

1. Appareils et connectivité  
1. IoT Central
1. Extensibilité et intégrations
1. Applications métier

Cet article décrit les composants clés qui ont généralement un rôle à jouer dans une solution de gestion des déchets.

## <a name="devices-and-connectivity"></a>Appareils et connectivité

Les dispositifs, tels que les conteneurs de déchets, qui sont utilisés dans les environnements ouverts peuvent généralement être connectés via des réseaux LPWAN ou via un opérateur réseau tiers. Pour ces types d’appareils, utilisez [Azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) pour envoyer les données de votre appareil à votre application IoT dans Azure IoT Central. Vous pouvez également utiliser des passerelles d’appareil qui sont compatibles IP et capables de se connecter directement à IoT Central.

## <a name="iot-central"></a>IoT Central

Azure IoT Central est une plateforme d’applications IoT qui vous permet de créer et de déployer rapidement une solution IoT. Vous pouvez personnaliser et intégrer votre solution à des services tiers.

Lorsque vous connectez vos systèmes de déchets intelligents à IoT Central, l’application permet de contrôler les appareils, d’effectuer le suivi et de déclencher des alertes, d’accéder à l’interface utilisateur à laquelle est intégré RBAC et d’accéder aux tableaux de bord configurables, ainsi qu’aux options d’extensibilité.

## <a name="extensibility-and-integrations"></a>Extensibilité et intégrations

Vous pouvez étendre votre application IoT dans IoT Central et si vous le souhaitez :

* Transformer et intégrer vos données IoT en vue d’une analytique avancée, par exemple en entraînant des modèles Machine Learning via l’exportation continue de données à partir d’une application IoT Central
* Automatiser des workflows dans d’autres systèmes en déclenchant des actions via Power Automate ou des webhooks à partir d’une application IoT Central
* Accéder programmatiquement à votre application IoT dans IoT Central via des API IoT Central

## <a name="business-applications"></a>Applications métier

Vous pouvez utiliser les données IoT pour alimenter diverses applications métier au sein d’une solution pour les déchets. Par exemple, dans une solution de gestion des déchets connectée, vous pouvez optimiser l’envoi de camions de ramassage d’ordures. L’optimisation peut être effectuée en fonction des données de capteurs IoT à partir de conteneurs à déchets connectés. Dans votre [application de gestion des déchets IoT Central connectée](./tutorial-connected-waste-management.md), vous pouvez configurer des règles et des actions, et les paramétrer pour créer des alertes dans le [Service de terrain connecté](/dynamics365/field-service/connected-field-service). Configurez Power Automate dans les règles d’IoT Central pour automatiser les flux de travail entre les applications et les services. Par ailleurs, selon les activités de service dans Connected Field Service, des informations peuvent être renvoyées à Azure IoT Central.

Vous pouvez facilement configurer les processus d’intégration suivants avec IoT Central et le service de terrain connecté :

* Azure IoT Central peut envoyer des informations relatives à des anomalies de périphériques à un service de terrain connecté à des fins de diagnostic.
* Ce service de terrain connecté peut créer des cas de support ou des ordres de travail déclenchés à partir d’anomalies de périphériques.
* Il peut également planifier des techniciens pour l’inspection afin d’éviter les temps d’arrêt.
* Le tableau de bord d’appareil Azure IoT Central peut être mis à jour avec les informations de planification et de service appropriées.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer une application de gestion des déchets connectée](./tutorial-connected-waste-management.md) IoT Central
* Apprenez-en davantage sur les [modèles IoT Central Government](./overview-iot-central-government.md)
* Pour plus d’informations sur IoT Central, consultez la [vue d’ensemble d’IoT Central](../core/overview-iot-central.md)
