---
title: Qu’est-ce que Azure IoT Central | Microsoft Docs
description: Azure IoT Central est une solution SaaS de bout en bout que vous pouvez utiliser pour créer et gérer votre solution IoT personnalisée. Cet article donne une vue d’ensemble des fonctionnalités de Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 8c369ab05059e57f2e2a98339052c27292ac7c0d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628774"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Qu’est-ce que Azure IoT Central

Microsoft Azure IoT Central est une solution IoT Software-as-a-Service (SaaS) entièrement gérée qui permet de créer facilement des produits qui se connectent à aux mondes physique et numérique. Vous pouvez donner vie à votre vision du produit connecté par :

- L’obtention de nouveaux insights depuis des périphériques connectés pour améliorer les produits et les expériences de vos clients.
- La création de nouvelles opportunités commerciales pour votre organisation.

Azure IoT Central, par rapport à un projet IoT standard, simplifie la gestion d’une solution IoT par :

- La réduction de la charge de travail.
- La réduction des frais généraux et des coûts d’exploitation.
- La simplification de la personnalisation de votre application, tout en tirant parti :
  - Des technologies avancées telles que [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) et [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).
  - Des fonctionnalités de classe affaires, comme le chiffrement de bout en bout.

La vidéo suivante donne une vue d’ensemble de Azure IoT Central :

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Le reste de cet article décrit pour Azure IoT Central :

- Les personnages types associés à un projet.
- La procédure de création de votre application.
- La connexion de vos appareils à votre application
- Comment gérer votre application.

## <a name="personas"></a>Rôles

La documentation de Azure IoT Central fait référence à quatre personnages classiques qui interagissent avec une application Azure IoT Central :

- Un _générateur_ est chargé de définir les types d’appareils qui se connectent à l’application et de la personnalisation de l’application pour l’opérateur.
- Un _opérateur_ gère les périphériques connectés à l’application.
- Un _administrateur_ est responsable des tâches administratives telles que la gestion des utilisateurs et des rôles au sein de l’application.
- Un _développeur de l’appareil_ crée le code qui s’exécute sur un appareil connecté à votre application.

## <a name="create-your-azure-iot-central-application"></a>Créer votre application Azure IoT Central

En tant que générateur, vous utilisez Azure IoT Central pour créer une solution IoT personnalisée et hébergée dans le cloud pour votre organisation. Une solution IoT personnalisée se compose généralement de :

- Une application basée sur le cloud qui reçoit des données de télémétrie de vos appareils et qui vous permet de gérer ces appareils.
- Un code personnalisé exécuté sur plusieurs appareils, connecté à votre application basée sur le cloud.

Vous pouvez déployer rapidement une nouvelle application Azure IoT Central, puis la personnaliser selon vos propres exigences, directement depuis dans votre navigateur. En tant que générateur d’Azure IoT Central, vous pouvez utiliser les outils web pour créer un _modèle d’appareil_ pour les appareils qui se connectent à votre application. Un modèle d’appareil est la blueprint d’un modèle d’appareil partagée par tous les appareils créés à partir du modèle d’appareil. Un modèle d’appareil définit les caractéristiques et le comportement d’un type d’appareils tel que :

- Les données de télémétrie qu’il envoie.
- Les propriétés commerciales qu’un opérateur peut modifier.
- Les propriétés d’appareil définies par un appareil et qui sont en lecture seule dans l’application.
- Les seuils auxquels l’application répond.
- Les paramètres qui déterminent le comportement de l’appareil.

Vous pouvez tester immédiatement vos modèles d’appareil et votre application avec des données simulées et générées par Azure IoT Central.

En tant que générateur, vous pouvez également personnaliser l’interface utilisateur de l’application Azure IoT Central pour les opérateurs responsables de l’utilisation quotidienne de l’application. Les personnalisations possibles par un générateur incluent :

- La définition de la mise en page de propriétés et des paramètres au sein d’un modèle d’appareil.
- La configuration de tableaux de bord personnalisés permettant aux opérateurs de découvrir des insights et de résoudre les problèmes plus rapidement.
- La configuration d’analytiques personnalisés pour explorer des données de série chronologiques à partir de vos appareils connectés.

## <a name="connect-your-devices"></a>Connecter vos appareils

Une fois que le générateur définit les types d’appareils pouvant se connecter à l’application, un développeur d’appareil crée le code à exécuter sur les appareils. En tant que développeur d’appareil, vous utilisez les [kits de développement logiciel Azure IoT](https://github.com/Azure/azure-iot-sdks) open source de Microsoft pour créer votre code d’appareil. Ces kits de développement logiciel disposent d’une prise en charge étendue des langages, des plateformes et des protocoles afin de répondre à vos besoins pour connecter vos appareils à votre application Azure IoT Central. Les kits de développement vous aident à effectuer les tâches suivantes sur l’appareil connecté à Azure IoT Central :

- Créer une connexion sécurisée.
- Envoyer des données de télémétrie.
- Signaler un état.
- Recevoir des mises à jour de configuration.

Pour plus d’informations, voir l’article de blog [Avantages de l’utilisation de kits de développement logiciel Azure IoT, les pièges à éviter dans le cas contraire](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Gérer votre application

Les applications Azure IoT Central sont entièrement hébergées par Microsoft, ce qui réduit la surcharge administrative liée à la gestion de vos applications.

En tant qu’opérateur, vous utilisez l’application Azure IoT Central pour gérer les appareils dans votre solution Azure IoT Central. Les opérateurs peuvent effectuer des tâches telles que :

- Surveiller les appareils connectés à l’application.
- La résolution et la correction des problèmes avec des appareils.
- La configuration de nouveaux appareils.

Un générateur peut définir des règles et des actions personnalisées qui s’exécutent sur des données diffusées en continu au niveau du modèle d’appareils. Un opérateur peut activer ou désactiver ces règles au niveau de l’appareil pour contrôler et automatiser des tâches au sein de l’application.

Les administrateurs peuvent gérer l’accès à votre application avec des [rôles d’utilisateur et des autorisations](howto-administer.md).

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
