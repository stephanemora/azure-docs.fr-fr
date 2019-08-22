---
title: Qu’est-ce que Azure IoT Central | Microsoft Docs
description: Azure IoT Central est une solution SaaS de bout en bout que vous pouvez utiliser pour créer et gérer votre solution IoT personnalisée. Cet article donne une vue d’ensemble des fonctionnalités de Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 04/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: ff212e77b6fcaaa057c22c420cce82e7ea6c82e6
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877289"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Qu’est-ce que Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central est une solution IoT Software-as-a-Service (SaaS) complètement managée qui permet de créer facilement des produits qui connectent les mondes physiques et numériques. Vous pouvez donner vie à votre vision du produit connecté par :

- L’obtention de nouveaux insights depuis des périphériques connectés pour améliorer les produits et les expériences de vos clients.
- La création de nouvelles opportunités commerciales pour votre organisation.

Comparaison d’Azure IoT Central à un projet IoT classique :

- Réduit la charge de travail.
- Réduit les frais généraux et les coûts d’exploitation.
- Simplifie la personnalisation de votre application, tout en travaillant avec :
  - Des technologies avancées telles que [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) et [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).
  - Des fonctionnalités de classe affaires, comme le chiffrement de bout en bout.

La vidéo suivante donne une vue d’ensemble de Azure IoT Central :

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Cet article décrit, en ce qui concerne Azure IoT Central :

- Les personnages types associés à un projet.
- La procédure de création de votre application.
- La connexion de vos appareils à votre application
- Comment gérer votre application.

## <a name="personas"></a>Rôles

La documentation d’Azure IoT Central fait référence à quatre personnages qui interagissent avec une application Azure IoT Central :

- Un _générateur_ est chargé de définir les types d’appareils qui se connectent à l’application et de la personnalisation de l’application pour l’opérateur.
- Un _opérateur_ gère les périphériques connectés à l’application.
- Un _administrateur_ est responsable des tâches administratives telles que la gestion des utilisateurs et des rôles au sein de l’application.
- Un _développeur de l’appareil_ crée le code qui s’exécute sur un appareil connecté à votre application.

## <a name="create-your-azure-iot-central-application"></a>Créer votre application Azure IoT Central

En tant que générateur, vous utilisez Azure IoT Central pour créer une solution IoT personnalisée et hébergée dans le cloud pour votre organisation. Une solution IoT personnalisée se compose généralement de :

- Une application basée sur le cloud qui reçoit des données de télémétrie de vos appareils et qui vous permet de gérer ces appareils.
- Un code personnalisé exécuté sur plusieurs appareils, connecté à votre application basée sur le cloud.

Vous pouvez déployer rapidement une nouvelle application Azure IoT Central, puis la personnaliser selon vos propres exigences, depuis dans votre navigateur. En tant que générateur, vous utilisez les outils web pour créer un _modèle d’appareil_ pour les appareils qui se connectent à votre application. Un modèle d’appareil est le blueprint qui définit les caractéristiques et le comportement d’un type d’appareils tel que :

- Les données de télémétrie qu’il envoie.
- Les propriétés commerciales qu’un opérateur peut modifier.
- Les propriétés d’appareil qui sont définies par un appareil et en lecture seule dans l’application.
- Les seuils auxquels l’application répond.
- Les paramètres qui déterminent le comportement de l’appareil.

Vous pouvez tester immédiatement vos modèles d’appareil et votre application avec des données simulées et générées par Azure IoT Central.

En tant que générateur, vous pouvez également personnaliser l’interface utilisateur de l’application Azure IoT Central pour les opérateurs responsables de l’utilisation quotidienne de l’application. Les personnalisations possibles par un générateur incluent :

- La définition de la mise en page de propriétés et des paramètres au sein d’un modèle d’appareil.
- La configuration de tableaux de bord personnalisés permettant aux opérateurs de découvrir des insights et de résoudre les problèmes plus rapidement.
- La configuration d’analytiques personnalisés pour explorer des données de série chronologiques à partir de vos appareils connectés.

## <a name="connect-your-devices"></a>Connecter vos appareils

Une fois que le générateur définit les types d’appareils pouvant se connecter à l’application, un développeur d’appareil crée le code à exécuter sur les appareils. En tant que développeur d’appareil, vous utilisez les [kits de développement logiciel Azure IoT](https://github.com/Azure/azure-iot-sdks) open source de Microsoft pour créer votre code d’appareil. Ces kits de développement logiciel disposent d’une prise en charge étendue des langages, des plateformes et des protocoles afin de répondre à vos besoins pour connecter vos appareils à votre application Azure IoT Central. Les Kits de développement logiciel (SDK) vous aident à implémenter les fonctionnalités suivantes des appareils :

- Créer une connexion sécurisée.
- Envoyer des données de télémétrie.
- Signaler un état.
- Recevoir des mises à jour de configuration.

Pour plus d’informations, voir l’article de blog [Avantages de l’utilisation de kits de développement logiciel Azure IoT, les pièges à éviter dans le cas contraire](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Gérer votre application

Les applications Azure IoT Central sont entièrement hébergées par Microsoft, ce qui réduit la surcharge administrative liée à la gestion de vos applications.

En tant qu’opérateur, vous utilisez l’application Azure IoT Central pour gérer les appareils dans votre solution Azure IoT Central. Les opérateurs effectuent des tâches telles que :

- Surveiller les appareils connectés à l’application.
- La résolution et la correction des problèmes avec des appareils.
- La configuration de nouveaux appareils.

En tant que générateur, vous pouvez définir des règles et des actions personnalisées qui s’exécutent sur les flux de données d’appareils connectés. Un opérateur peut activer ou désactiver ces règles au niveau de l’appareil pour contrôler et automatiser des tâches au sein de l’application.

Les administrateurs gèrent l’accès à votre application avec des [rôles d’utilisateur et des autorisations](howto-administer.md).

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez une vue d’ensemble de Azure IoT Central, voici les prochaines étapes suggérées :

- Comprendre les différences entre [Azure IoT Central et les accélérateurs de solution Azure IoT](overview-iot-options.md).
- Vous familiariser avec [l’interface utilisateur de Azure IoT Central](overview-iot-central-tour.md).
- Mise en route avec la [création d’une application Azure IoT Central](quick-deploy-iot-central.md).
- Suivez une série de didacticiels qui vous indiquent :
  - [En tant que générateur, comment créer un modèle d’appareil](tutorial-define-device-type.md)
  - [En tant que générateur, ajouter des règles pour automatiser votre solution](tutorial-configure-rules.md)
  - [En tant que générateur, personnaliser l’application pour vos opérateurs](tutorial-customize-operator.md)
  - [En tant qu’opérateur, surveiller vos appareils](tutorial-monitor-devices.md)
  - [En tant qu’opérateur, ajouter un appareil réel à votre solution](tutorial-add-device.md)
  - [En tant que développeur d’appareil, créer du code pour vos appareils](tutorial-add-device.md#prepare-the-client-code)
