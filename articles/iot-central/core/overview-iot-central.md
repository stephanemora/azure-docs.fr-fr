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
ms.openlocfilehash: a16ed6ac942dd4a9fa521cc813a92e6767a98328
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024124"
---
# <a name="what-is-azure-iot-central"></a>Qu’est-ce que Azure IoT Central

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

Vous pouvez déployer rapidement une nouvelle application IoT Central, puis la personnaliser selon vos propres exigences, depuis dans votre navigateur. Vous pouvez commencer avec un _modèle d’application_ générique ou avec l’un des modèles d’application axés sur la [vente au détail](../retail/overview-iot-central-retail-pnp.md), l’[énergie](../energy/overview-iot-central-energy.md), le [secteur public](../government/overview-iot-central-government.md) ou la [santé](../healthcare/overview-iot-central-healthcare.md).

En tant que créateur de solution, vous utilisez les outils web pour créer un _modèle d’appareil_ pour les appareils qui se connectent à votre application. Un modèle d’appareil est le blueprint qui définit les caractéristiques et le comportement d’un type d’appareils tel que :

- Les données de télémétrie qu’il envoie.
- Les propriétés commerciales qu’un opérateur peut modifier.
- Les propriétés d’appareil qui sont définies par un appareil et en lecture seule dans l’application.
- Les propriétés définies par un opérateur, qui déterminent le comportement de l’appareil.

Ce modèle d’appareil inclut les éléments suivants :

- Un _modèle de fonctionnalité d’appareil_ qui décrit les fonctionnalités qu’un appareil doit implémenter, telles que la télémétrie qu’il envoie et les propriétés qu’il signale.
- Propriétés cloud qui ne sont pas stockées sur l’appareil.
- Personnalisations, tableaux de bord et formulaires qui font partie de votre application IoT Central.


### <a name="pricing"></a>Tarifs

Vous pouvez créer une application IoT Central dans le cadre d’un essai gratuit de 7 jours ou utiliser un plan tarifaire standard.

- Les applications que vous créez à partir d’un plan *gratuit* sont gratuites pendant sept jours et prennent en charge jusqu’à cinq appareils. Vous pouvez les convertir de sorte qu’elles utilisent un plan tarifaire standard à tout moment avant leur expiration.
- Les applications que vous créez dans le cadre d’un plan *standard* sont facturées par appareil ; vous pouvez choisir un plan tarifaire **Standard 1** ou **Standard 2**, les deux premiers appareils étant gratuits. Découvrez plus en détail les plans tarifaires gratuit et standard dans la [page des prix Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

### <a name="create-device-templates"></a>Créer des modèles d’appareils

[IoT Plug-and-Play (préversion)](../../iot-pnp/overview-iot-plug-and-play.md) permet à IoT Central d’intégrer des appareils en vous évitant d’écrire du code d’appareil incorporé. Au cœur d’IoT Plug-and-Play (préversion) se trouve un schéma de capacité d’appareil qui décrit les capacités d’appareil. Dans une application IoT Central, les modèles d’appareil utilisent ces modèles de capacité d’appareil IoT Plug-and-Play (préversion).

Plusieurs choix s’offrent à vous pour créer des modèles d’appareil :

- Importez un modèle de fonctionnalité d’appareil à partir du [catalogue d’appareils Azure Certified pour IoT](https://aka.ms/iotdevcat), puis ajoutez les propriétés cloud, les personnalisations et les tableaux de bord nécessaires à votre application IoT Central.
- Concevez le modèle d’appareil dans IoT Central, puis implémentez son modèle de fonctionnalité d’appareil dans le code de votre appareil.
- Créez un modèle de capacité d’appareil avec du code Visual Studio et publiez le modèle dans un référentiel. Implémentez votre code d’appareil à partir du modèle et connectez votre appareil à votre application IoT Central. IoT Central recherche le modèle de capacité d’appareil dans le référentiel et crée automatiquement un modèle d’appareil simple.
- Créez un modèle de fonctionnalité d’appareil mobile avec Visual Studio Code. Implémentez votre code d’appareil à partir du modèle. Importez manuellement le modèle de fonctionnalité d’appareil dans votre application IoT Central, puis ajoutez les propriétés cloud, les personnalisations et les tableaux de bord nécessaires à votre application IoT Central.

En tant que créateur de solution, vous pouvez utiliser IoT Central pour générer du code pour les appareils de test afin de valider vos modèles d’appareil.

### <a name="customize-the-ui"></a>Personnaliser l’interface utilisateur

En tant que créateur de solution, vous pouvez également personnaliser l’interface utilisateur de l’application IoT Central pour les opérateurs responsables de l’utilisation quotidienne de l’application. Les personnalisations possibles par un créateur de solution incluent :

- La définition de la mise en page de propriétés et des paramètres au sein d’un modèle d’appareil.
- La configuration de tableaux de bord personnalisés permettant aux opérateurs de découvrir des insights et de résoudre les problèmes plus rapidement.
- La configuration d’analytiques personnalisés pour explorer des données de série chronologiques à partir de vos appareils connectés.

## <a name="pricing"></a>Tarifs

Vous pouvez créer une application IoT Central dans le cadre d’un essai gratuit de 7 jours ou utiliser un plan tarifaire standard.

- Les applications que vous créez à partir d’un plan *gratuit* sont gratuites pendant sept jours et prennent en charge jusqu’à cinq appareils. Vous pouvez les convertir de sorte qu’elles utilisent un plan tarifaire standard à tout moment avant leur expiration.
- Les applications que vous créez vous dans le cadre d’un plan *standard* sont facturées par appareil ; vous pouvez choisir un plan tarifaire **Standard 1** ou **Standard 2**, les deux premiers appareils étant gratuits. Apprenez-en davantage sur les [tarifs d’IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Connecter vos appareils

Azure IoT Central utilise le [service Azure IoT Device Provisioning (DPS)](../../iot-dps/about-iot-dps.md) pour gérer toutes les inscriptions et connexions d’appareils.

L’utilisation de DPS permet :

- à IoT Central de prendre en charge l’intégration et la connexion des appareils à l’échelle ;
- aux clients de générer des informations d’identification d’appareils et de les configurer hors ligne sans avoir à les inscrire via l’interface utilisateur d’IoT Central ;
- aux appareils de se connecter avec des signatures d’accès partagé ;
- aux appareils de se connecter avec des certificats X.509 standard du secteur ;
- aux clients d’utiliser leurs propres ID d’appareils pour inscrire des appareils dans IoT Central. L’utilisation de vos propres ID d’appareils simplifie l’intégration dans des systèmes back-office existants.
- C’est une manière cohérente de connecter des appareils à IoT Central.

Pour plus d’informations, consultez [Se connecter à Azure IoT Central](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Appareil Azure IoT Edge

En plus des appareils créés à l’aide des [kits de développement logiciel (SDK) Azure IoT](https://github.com/Azure/azure-iot-sdks), vous pouvez également connecter des [appareils Azure IoT Edge](../../iot-edge/about-iot-edge.md) à une application IoT Central. Azure IoT Edge vous permet d’exécuter l’intelligence Cloud et une logique personnalisée directement sur les appareils IoT gérés par IoT Central. Le runtime IoT Edge vous permet d’effectuer les opérations suivantes :

- Installer et mettre à jour des charges de travail sur l’appareil.
- Tenir à jour les normes de sécurité Azure IoT Edge sur l’appareil.
- Garantir que les modules IoT Edge sont toujours en cours d’exécution.
- Envoyer des rapports d’intégrité du module dans le cloud pour la supervision à distance.
- Gérer la communication entre les appareils de nœud terminal en aval et un appareil IoT Edge, entre les modules sur un appareil IoT Edge, ainsi qu’entre un appareil IoT Edge et le cloud.

Pour plus d’informations, voir [Appareils Azure IoT Edge et IoT Central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>Rester connecté

Les applications IoT Central sont entièrement hébergées par Microsoft, ce qui réduit la surcharge administrative liée à la gestion de vos applications.

En tant qu’opérateur, vous utilisez l’application IoT Central pour gérer les appareils dans votre solution IoT Central. Les opérateurs effectuent des tâches telles que :

- Surveiller les appareils connectés à l’application.
- La résolution et la correction des problèmes avec des appareils.
- La configuration de nouveaux appareils.

En tant que créateur de solution, vous pouvez définir des règles et des actions personnalisées qui s’exécutent sur les flux de données d’appareils connectés. Un opérateur peut activer ou désactiver ces règles au niveau de l’appareil pour contrôler et automatiser des tâches au sein de l’application.

Les administrateurs gèrent l’accès à votre application avec des [rôles d’utilisateur et des autorisations](howto-administer.md).

Pour toute solution IoT conçue pour fonctionner à grande échelle, il est important d’avoir une approche structurée de la gestion des appareils. Il ne vous suffit pas de connecter vos appareils au cloud ; ceux-ci doivent rester connectés et sains. Un opérateur peut utiliser les capacités IoT Central suivantes pour gérer vos appareils tout au long du cycle de vie de l’application :

### <a name="dashboards"></a>Tableaux de bord 

Les [tableaux de bord](./howto-set-up-template.md#generate-default-views) intégrés proposent une interface utilisateur personnalisable qui permet de superviser la télémétrie et l’intégrité des appareils. Commencez avec un tableau de bord prédéfini dans un [modèle d’application](howto-use-app-templates.md) ou créez vos propres tableaux de bord en fonction des besoins de vos opérateurs. Vous pouvez partager les tableaux de bord avec tous les utilisateurs de votre application ou les maintenir privés.

### <a name="rules-and-actions"></a>Règles et actions 

Générez des [règles personnalisées](tutorial-create-telemetry-rules.md) en fonction de l’état et des données de télémétrie des appareils pour identifier ceux qui ont besoin d’attention. Configurez des actions pour notifier les personnes concernées et faire en sorte que des mesures correctives soient prises en temps voulu.

### <a name="jobs"></a>travaux 

Les [travaux](howto-run-a-job.md) vous permettent d’appliquer des mises à jour uniques ou en bloc aux appareils en définissant des propriétés ou en appelant des commandes. 

### <a name="user-roles-and-permissions"></a>Rôles et autorisations d’utilisateur

Les [rôles et autorisations](howto-manage-users-roles.md) permettent à un administrateur d’adapter l’expérience de chaque utilisateur. Un administrateur se sert de l’interface utilisateur web pour créer des rôles et attribuer des autorisations.

## <a name="transform-your-iot-data"></a>Transformer vos données IoT

En tant que plateforme d’application, IoT Central vous permet de transformer vos données IoT en insights métier qui permettent d’obtenir des résultats exploitables. Les [règles](./tutorial-create-telemetry-rules.md), l’[exportation de données](./howto-export-data.md)et l’[API REST publique](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) sont des exemples de la façon dont vous pouvez intégrer IoT Central à des applications métier :

![Comment IoT Central peut transformer vos données IoT](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Superviser l’intégrité et le fonctionnement des appareils à l’aide de règles

Quand vos appareils sont connectés et qu’ils envoient des données, les règles permettent d’identifier ceux qui rencontrent des problèmes ou qui envoient des messages d’erreur, ce qui vous permet de les corriger avec un temps d’arrêt minime. Créez des règles dans votre application IoT Central pour superviser les données de télémétrie de vos appareils et alerter un opérateur dès qu’une métrique franchit un seuil ou qu’un appareil envoie un message spécifique. Les actions de messagerie et les webhooks de vos règles notifient les personnes et les systèmes en aval appropriés.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Exécuter des analyses et des traitements personnalisés sur vos données exportées

Vous pouvez générer des insights métier, comme déterminer l’évolution de l’efficacité des machines ou prédire la consommation future d’énergie dans un atelier de fabrication en créant des pipelines analytiques personnalisés pour traiter les données de télémétrie de vos appareils et stocker les résultats. Configurez des exportations de données dans votre application IoT Central de telle sorte que les données de télémétrie et les modifications apportées aux propriétés et aux modèles d’appareil soient exportées vers d’autres services pour y analyser, stocker et visualiser les données à l’aide de vos outils préférés.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Générer des solutions et des intégrations IoT personnalisées avec les API REST

Générez les types de solutions IoT suivants :

- Applications compagnes mobiles capables de configurer et contrôler à distance des appareils.
- Intégrations personnalisées qui permettent aux applications métier existantes d’interagir avec vos appareils et données IoT.
- Applications de gestion d’appareils pour la modélisation, l’intégration, la gestion et de l’accès aux données des appareils.

## <a name="quotas"></a>Quotas

Chaque abonnement Azure a des quotas par défaut qui peuvent impacter l’étendue de votre solution IoT. IoT Central limite actuellement à 10 le nombre d’applications que vous pouvez déployer dans un abonnement. Pour augmenter cette limite, contactez le [support Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez une vue d’ensemble de IoT Central, voici les prochaines étapes suggérées :

- Découvrez les [technologies et services Azure disponibles pour créer des solutions IoT](../../iot-fundamentals/iot-services-and-technologies.md).
- Vous familiariser avec [l’interface utilisateur de Azure IoT Central](overview-iot-central-tour.md).
- Mise en route avec la [création d’une application Azure IoT Central](quick-deploy-iot-central.md).
- Découvrez plus en détail [IoT Plug-and-Play (préversion)](../../iot-pnp/overview-iot-plug-and-play.md).
- Découvrez comment [connecter un appareil Azure IoT Edge](./tutorial-add-edge-as-leaf-device.md).
- Découvrez plus en détail les [technologies et services Azure IoT](../../iot-fundamentals/iot-services-and-technologies.md).
