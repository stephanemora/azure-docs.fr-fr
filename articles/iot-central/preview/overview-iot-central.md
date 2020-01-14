---
title: Qu’est-ce que Azure IoT Central | Microsoft Docs
description: Azure IoT Central est une plateforme IoT qui simplifie la création de vos solutions IoT et contribue à réduire la charge et le coût liés à la gestion, aux opérations et au développement IoT. Cet article donne une vue d’ensemble des fonctionnalités de Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 52504fb8333b286407b3f2df8f962da59b80ac53
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434845"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Présentation d’IoT Central (fonctionnalités en préversion)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Les fonctionnalités [IoT Plug-and-Play](../../iot-pnp/overview-iot-plug-and-play.md) dans Azure IoT Central sont actuellement en préversion publique. N’utilisez pas un [modèle d’application](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) IoT Central sur laquelle IoT Plug-and-Play est activé pour les charges de travail de production. Pour les environnements de production, utilisez une application IoT Central créée à partir d’un [modèle d’application](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) actuel, généralement disponible.

IoT Central est une plateforme d’application IoT qui réduit les charges et les coûts associés au développement, à la gestion et à la maintenance de solutions IoT de qualité professionnelle. Choisir de créer des applications avec IoT Central vous offre la possibilité de consacrer votre temps, votre argent et votre énergie à transformer votre activité avec des données IoT, au lieu de simplement gérer et mettre à jour une infrastructure IoT complexe et en constante évolution.

L’interface utilisateur web vous permet de superviser les conditions des appareils, de créer des règles et de gérer des millions d’appareils et leurs données tout au long de leur cycle de vie. Elle vous permet également d’agir sur les insights des appareils en élargissant l’intelligence IoT aux applications métier.

Cet article décrit, en ce qui concerne IoT Central :

- Les personnages types associés à un projet.
- La procédure de création de votre application.
- La connexion de vos appareils à votre application
- Comment gérer votre application.
- Fonctionnalités Azure IoT Edge dans IoT Central
- Comment connecter vos appareils fonctionnant avec Azure IoT Edge à votre application.

## <a name="known-issues"></a>Problèmes connus

> [!Note]
> Ces problèmes connus s’appliquent uniquement à l’application IoT Central en préversion.

- L’exportation continue des données ne prend pas en charge le format Avro (incompatibilité).
- GeoJSON n’est pas actuellement pris en charge.
- La mosaïque n’est pas actuellement prise en charge.
- Les travaux ne prennent pas en charge les types complexes.
- Les types de schémas de tableaux ne sont pas pris en charge.
- Seuls le SDK d’appareil C et les SDK de service et d’appareil Node.js sont pris en charge.
- Elle est uniquement disponible aux États-Unis et en Europe.
- Les modèles de capacité d’appareil doivent avoir toutes les interfaces définies inline dans le même fichier.

## <a name="personas"></a>Rôles

La documentation de IoT Central fait référence à quatre personnages qui interagissent avec une application IoT Central :

- Un _créateur de solution_ est chargé de définir les types d’appareils qui se connectent à l’application et de la personnalisation de l’application pour l’opérateur.
- Un _opérateur_ gère les périphériques connectés à l’application.
- Un _administrateur_ est responsable des tâches d’administration comme la gestion des [rôles et des autorisations des utilisateurs](howto-administer.md) au sein de l’application.
- Un _développeur de l’appareil_ crée le code qui s’exécute sur un appareil ou un module IoT Edge connecté à votre application.

## <a name="create-your-iot-central-application"></a>Créer votre application IoT Central

En tant que créateur de solution, vous utilisez IoT Central pour créer une solution IoT personnalisée et hébergée dans le cloud pour votre organisation. Une solution IoT personnalisée se compose généralement de :

- Une application basée sur le cloud qui reçoit des données de télémétrie de vos appareils et qui vous permet de gérer ces appareils.
- Un code personnalisé exécuté sur plusieurs appareils, connecté à votre application basée sur le cloud.

Vous pouvez déployer rapidement une nouvelle application IoT Central, puis la personnaliser selon vos propres exigences, depuis dans votre navigateur. En tant que créateur de solution, vous utilisez les outils web pour créer un _modèle d’appareil_ pour les appareils qui se connectent à votre application. Un modèle d’appareil est le blueprint qui définit les caractéristiques et le comportement d’un type d’appareils tel que :

- Les données de télémétrie qu’il envoie.
- Les propriétés commerciales qu’un opérateur peut modifier.
- Les propriétés d’appareil qui sont définies par un appareil et en lecture seule dans l’application.
- Les propriétés définies par un opérateur, qui déterminent le comportement de l’appareil.

Ce modèle d’appareil inclut les éléments suivants :

- Un _modèle de fonctionnalité d’appareil_ qui décrit les fonctionnalités qu’un appareil doit implémenter, telles que la télémétrie qu’il envoie et les propriétés qu’il signale.
- Propriétés cloud qui ne sont pas stockées sur l’appareil.
- Personnalisations, tableaux de bord et formulaires qui font partie de votre application IoT Central.

### <a name="create-device-templates"></a>Créer des modèles d’appareils

[IoT Plug-and-Play](../../iot-pnp/overview-iot-plug-and-play.md) permet à IoT Central d’intégrer des appareils sans que vous n’ayez à écrire de code d’appareil incorporé. Un schéma de modèle de capacité d’appareil est au cœur d’IoT Plug-and-Play, qui décrit les capacités des appareils. Dans une application IoT Central en préversion, les modèles d’appareil utilisent ces modèles de fonctionnalité d’appareil IoT Plug-and-Play.

Plusieurs choix s’offrent à vous pour créer des modèles d’appareil :

- Concevez le modèle d’appareil dans IoT Central, puis implémentez son modèle de fonctionnalité d’appareil dans le code de votre appareil.
- Importez un modèle de fonctionnalité d’appareil à partir du [catalogue d’appareils Azure Certified pour IoT](https://aka.ms/iotdevcat), puis ajoutez les propriétés cloud, les personnalisations et les tableaux de bord nécessaires à votre application IoT Central.
- Créez un modèle de fonctionnalité d’appareil mobile avec Visual Studio Code. Implémentez votre code d’appareil à partir du modèle et connectez votre appareil à votre application IoT Central. IoT Central recherche le modèle de fonctionnalité d’appareil à partir d’un référentiel et crée un modèle d’appareil simple pour vous.
- Créez un modèle de fonctionnalité d’appareil mobile avec Visual Studio Code. Implémentez votre code d’appareil à partir du modèle. Importez manuellement le modèle de fonctionnalité d’appareil dans votre application IoT Central, puis ajoutez les propriétés cloud, les personnalisations et les tableaux de bord nécessaires à votre application IoT Central.

En tant que créateur de solution, vous pouvez utiliser IoT Central pour générer du code pour les appareils de test afin de valider vos modèles d’appareil.

### <a name="customize-the-ui"></a>Personnaliser l’interface utilisateur

En tant que créateur de solution, vous pouvez également personnaliser l’interface utilisateur de l’application IoT Central pour les opérateurs responsables de l’utilisation quotidienne de l’application. Les personnalisations possibles par un créateur de solution incluent :

- La définition de la mise en page de propriétés et des paramètres au sein d’un modèle d’appareil.
- La configuration de tableaux de bord personnalisés permettant aux opérateurs de découvrir des insights et de résoudre les problèmes plus rapidement.
- La configuration d’analytiques personnalisés pour explorer des données de série chronologiques à partir de vos appareils connectés.

## <a name="connect-your-devices"></a>Connecter vos appareils

Une fois que le générateur définit les types d’appareils pouvant se connecter à l’application, un développeur d’appareil crée le code à exécuter sur les appareils. En tant que développeur d’appareil, vous utilisez les [kits de développement logiciel Azure IoT](https://github.com/Azure/azure-iot-sdks) open source de Microsoft pour créer votre code d’appareil. Ces kits de développement logiciel disposent d’une prise en charge étendue des langages, des plateformes et des protocoles afin de répondre à vos besoins pour connecter vos appareils à votre application IoT Central. Les Kits de développement logiciel (SDK) vous aident à implémenter les fonctionnalités suivantes des appareils :

- Créer une connexion sécurisée.
- Envoyer des données de télémétrie.
- Signaler un état.
- Recevoir des mises à jour de configuration.

Pour plus d’informations, voir l’article de blog [Avantages de l’utilisation de kits de développement logiciel Azure IoT, les pièges à éviter dans le cas contraire](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

### <a name="azure-iot-edge-devices"></a>Appareil Azure IoT Edge

En plus des appareils créés à l’aide des [kits de développement logiciel (SDK) Azure IoT](https://github.com/Azure/azure-iot-sdks), vous pouvez également connecter des [appareils Azure IoT Edge](../../iot-edge/about-iot-edge.md) à une application IoT Central. Azure IoT Edge vous permet d’exécuter l’intelligence Cloud et une logique personnalisée directement sur les appareils IoT gérés par IoT Central. Le runtime IoT Edge vous permet d’effectuer les opérations suivantes :

- Installer et mettre à jour des charges de travail sur l’appareil.
- Tenir à jour les normes de sécurité Azure IoT Edge sur l’appareil.
- Garantir que les modules IoT Edge sont toujours en cours d’exécution.
- Envoyer des rapports d’intégrité du module dans le cloud pour la supervision à distance.
- Gérer la communication entre les appareils de nœud terminal en aval et un appareil IoT Edge, entre les modules sur un appareil IoT Edge, ainsi qu’entre un appareil IoT Edge et le cloud.

Pour plus d’informations, voir [Appareils Azure IoT Edge et IoT Central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Gérer votre application

Les applications IoT Central sont entièrement hébergées par Microsoft, ce qui réduit la surcharge administrative liée à la gestion de vos applications.

En tant qu’opérateur, vous utilisez l’application IoT Central pour gérer les appareils dans votre solution IoT Central. Les opérateurs effectuent des tâches telles que :

- Surveiller les appareils connectés à l’application.
- La résolution et la correction des problèmes avec des appareils.
- La configuration de nouveaux appareils.

En tant que créateur de solution, vous pouvez définir des règles et des actions personnalisées qui s’exécutent sur les flux de données d’appareils connectés. Un opérateur peut activer ou désactiver ces règles au niveau de l’appareil pour contrôler et automatiser des tâches au sein de l’application.

Les administrateurs gèrent l’accès à votre application avec des [rôles d’utilisateur et des autorisations](howto-administer.md).

## <a name="quotas"></a>Quotas

Chaque abonnement Azure a des quotas par défaut qui peuvent impacter l’étendue de votre solution IoT. IoT Central limite actuellement à 10 le nombre d’applications que vous pouvez déployer dans un abonnement. Pour augmenter cette limite, contactez le [support Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez une vue d’ensemble de IoT Central, voici les prochaines étapes suggérées :

- Découvrez les [technologies et services Azure disponibles pour créer des solutions IoT](../../iot-fundamentals/iot-services-and-technologies.md).
- Vous familiariser avec [l’interface utilisateur de Azure IoT Central](overview-iot-central-tour.md).
- Mise en route avec la [création d’une application Azure IoT Central](quick-deploy-iot-central.md).
- En savoir plus sur [IoT Plug-and-Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Découvrez comment [créer un modèle d’appareil Azure IoT Edge](./tutorial-define-edge-device-type.md)
