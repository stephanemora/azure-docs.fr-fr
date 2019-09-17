---
title: Qu’est-ce que Azure IoT Central | Microsoft Docs
description: Azure IoT Central est une solution SaaS de bout en bout que vous pouvez utiliser pour créer et gérer votre solution IoT personnalisée. Cet article donne une vue d’ensemble des fonctionnalités de Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: bfdad0d81599035e7d8c270ec4e8ee8d6a45125e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858925"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Présentation d’IoT Central (fonctionnalités en préversion)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Les fonctionnalités [IoT Plug-and-Play](../iot-pnp/overview-iot-plug-and-play.md) dans Azure IoT Central sont actuellement en préversion publique. N’utilisez pas une application IoT Central sur laquelle IoT Plug-and-Play est activé pour les charges de travail de production. Pour les environnements de production, utilisez une application IoT Central créée à partir d’un modèle d’application actuel, généralement disponible.

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

## <a name="known-issues"></a>Problèmes connus

> [!Note]
> Ces problèmes connus s’appliquent uniquement à l’application IoT Central en préversion.

- Les règles ne prennent pas en charge toutes les actions (uniquement les e-mails).
- Pour les types complexes, les règles, les analyses et les groupes d’appareils ne sont pas pris en charge.
- L’exportation continue des données ne prend pas en charge le format Avro (incompatibilité).
- Les appareils simulés ne prennent pas en charge tous les types complexes.
- GeoJSON n’est pas actuellement pris en charge.
- La mosaïque n’est pas actuellement prise en charge.
- Les travaux ne prennent pas en charge les types complexes.
- Les types de schémas de tableaux ne sont pas pris en charge.
- L’exportation de modèle d’application et la copie d’application ne sont pas prises en charge.
- Seuls le SDK d’appareil C et les SDK de service et d’appareil Node.js sont pris en charge.
- Elle est disponible uniquement dans les régions Europe Nord et USA Centre.
- Les modèles de capacité d’appareil doivent avoir toutes les interfaces définies inline dans le même fichier.

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
- Propriétés définies par un opérateur qui déterminent le comportement de l’appareil.

Ce modèle d’appareil inclut les éléments suivants :

- Un _modèle de fonctionnalité d’appareil_ qui décrit les fonctionnalités qu’un appareil doit implémenter, telles que la télémétrie qu’il envoie et les propriétés qu’il signale.
- Propriétés cloud qui ne sont pas stockées sur l’appareil.
- Personnalisations, tableaux de bord et formulaires qui font partie de votre application IoT Central.

### <a name="create-device-templates"></a>Créer des modèles d’appareils

[IoT Plug-and-Play](../iot-pnp/overview-iot-plug-and-play.md) permet à IoT Central d’intégrer des appareils sans que vous n’ayez à écrire de code d’appareil incorporé. Au cœur d’IoT Plug-and-Play est un schéma de modèle de fonctionnalité d’appareil qui décrit les fonctionnalités de l’appareil. Dans une application IoT Central en préversion, les modèles d’appareil utilisent ces modèles de fonctionnalité d’appareil IoT Plug-and-Play.

Plusieurs choix s’offrent à vous pour créer des modèles d’appareil :

- Concevez le modèle d’appareil dans IoT Central, puis implémentez son modèle de fonctionnalité d’appareil dans le code de votre appareil.
- Importez un modèle de fonctionnalité d’appareil à partir du [catalogue d’appareils Azure Certified pour IoT](https://aka.ms/iotdevcat), puis ajoutez les propriétés cloud, les personnalisations et les tableaux de bord nécessaires à votre application IoT Central.
- Créez un modèle de fonctionnalité d’appareil mobile avec Visual Studio Code. Implémentez votre code d’appareil à partir du modèle et connectez votre appareil à votre application IoT Central. IoT Central recherche le modèle de fonctionnalité d’appareil à partir d’un référentiel et crée un modèle d’appareil simple pour vous.
- Créez un modèle de fonctionnalité d’appareil mobile avec Visual Studio Code. Implémentez votre code d’appareil à partir du modèle. Importez manuellement le modèle de fonctionnalité d’appareil dans votre application IoT Central, puis ajoutez les propriétés cloud, les personnalisations et les tableaux de bord nécessaires à votre application IoT Central.

En tant que créateur, vous pouvez utiliser IoT Central pour générer du code pour les appareils de test afin de valider vos modèles d’appareil.

### <a name="customize-the-ui"></a>Personnaliser l’interface utilisateur

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

Les administrateurs gèrent l’accès à votre application avec des [rôles d’utilisateur et des autorisations](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez une vue d’ensemble de Azure IoT Central, voici les prochaines étapes suggérées :

- Comprendre les différences entre [Azure IoT Central et les accélérateurs de solution Azure IoT](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Vous familiariser avec [l’interface utilisateur de Azure IoT Central](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Mise en route avec la [création d’une application Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Suivez une série de didacticiels qui vous indiquent :
  - [En tant que générateur, comment créer un modèle d’appareil](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [En tant que générateur, ajouter des règles pour automatiser votre solution](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [En tant qu’opérateur, surveiller vos appareils](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [En tant qu’opérateur, ajouter un appareil à votre solution](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- En savoir plus sur [IoT Plug-and-Play](../iot-pnp/overview-iot-plug-and-play.md)
