---
title: Glossaire Azure IoT | Microsoft Docs
description: 'Guide du développeur : Glossaire expliquant certains des termes courants utilisés dans les articles sur Azure IoT.'
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 3e8a2ac93e9fea6ad045030759be894617557658
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021935"
---
# <a name="glossary-of-iot-terms"></a>Glossaire IoT

Cet article répertorie certains termes courants utilisés dans les articles relatifs à l'IoT.

## <a name="a"></a>Un

### <a name="advanced-message-queueing-protocol"></a>Advanced Message Queuing Protocol

[Advanced Message Queuing Protocol (AMQP)](https://www.amqp.org/) est l’un des protocoles de messagerie que [IoT Hub](#iot-hub) prend en charge pour communiquer avec des appareils. Pour plus d’informations sur les protocoles de messagerie pris en charge par IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="automatic-device-management"></a>Gestion automatique des appareils

La gestion automatique des appareils dans Azure IoT Hub automatise une grande partie des tâches répétitives et complexes liées à la gestion de grandes flottes d’appareils pendant tout leur cycle de vie. Avec la gestion automatique des appareils, vous pouvez cibler un ensemble d’appareils en fonction de leurs propriétés, définir la configuration souhaitée et permettre à IoT Hub de mettre à jour les appareils chaque fois qu’ils se trouvent dans l’étendue.  La gestion automatique des appareils comprend les [configurations automatiques des appareils](../iot-hub/iot-hub-automatic-device-management.md) et les [déploiements automatiques IoT Edge](../iot-edge/how-to-deploy-at-scale.md).

### <a name="automatic-device-configuration"></a>Configuration automatique des appareils

Le backend de la solution peut utiliser des [configurations automatiques des appareils](../iot-hub/iot-hub-automatic-device-management.md) pour affecter des propriétés souhaitées à un ensemble de [jumeaux d’appareil](#device-twin) et signaler l’état à l’aide de métriques système et de métriques personnalisées. 

### <a name="azure-iot-device-sdks"></a>Kits Azure IoT device SDK

Des kits _device SDK_ sont disponibles pour plusieurs langages et permettent de créer des [applications pour appareils](#device-app) qui interagissent avec un hub IoT. Les didacticiels sur IoT Hub expliquent comment utiliser ces kits device SDK. Le code source et des informations supplémentaires sur les kits device SDK sont disponibles dans ce [dépôt](https://github.com/Azure/azure-iot-sdks) GitHub.

### <a name="azure-iot-explorer"></a>Explorateur Azure IoT

L'[Explorateur Azure IoT](https://github.com/Azure/azure-iot-explorer) permet d'afficher les données de télémétrie que l’appareil envoie, utiliser les propriétés de l’appareil et appeler des commandes. Vous pouvez également utiliser l'explorateur pour interagir avec vos appareils et les tester, et pour gérer les [appareils IoT Plug-and-Play](#iot-plug-and-play-device).

### <a name="azure-iot-service-sdks"></a>Kits Azure IoT service SDK

Des kits _service SDK_ sont disponibles pour plusieurs langages et permettent de créer des [applications principales](#back-end-app) qui interagissent avec un hub IoT. Les didacticiels sur IoT Hub expliquent comment utiliser ces kits service SDK. Le code source et des informations supplémentaires sur les Kits de développement logiciel de services sont disponibles dans ce [dépôt](https://github.com/Azure/azure-iot-sdks) GitHub.

### <a name="azure-iot-tools"></a>Azure IoT Tools

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) est une extension multiplateforme, open-source Visual Studio Code, qui vous permet de gérer Azure IoT Hub et des périphériques dans VS Code. Avec Azure IoT Tools, les développeurs IoT peuvent développer des projets IoT dans VS Code en toute simplicité.

## <a name="b"></a>B

### <a name="back-end-app"></a>Application principale

Dans le contexte de [IoT Hub](#iot-hub), une application principale est une application qui se connecte à l’un des points de terminaison visible par le service sur un hub IoT. Par exemple, une application back-end peut récupérer des messages [appareil-à-cloud](#device-to-cloud) ou gérer le [registre des identités](#identity-registry). En règle générale, une application principale s’exécute dans le cloud mais, dans de nombreux didacticiels, les applications principales sont des applications console qui s’exécutent sur votre ordinateur de développement local.

### <a name="built-in-endpoints"></a>Points de terminaison intégrés

Chaque hub IoT inclut un [point de terminaison](../iot-hub/iot-hub-devguide-endpoints.md) intégré compatible avec Event Hub. Vous pouvez utiliser n’importe quel mécanisme compatible avec Event Hubs pour lire les messages appareil-à-cloud à partir de ce point de terminaison.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Passerelle cloud

Une passerelle cloud assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub). Une passerelle cloud est hébergée dans le cloud, contrairement à une [passerelle de champ](#field-gateway) qui s’exécute localement sur vos appareils. Un cas d’utilisation classique d’une passerelle cloud est l’implémentation d’une traduction de protocole pour vos appareils.

### <a name="cloud-to-device"></a>Cloud-à-appareil

Fait référence aux messages envoyés à partir d’un hub IoT à un appareil connecté. Souvent, ces messages sont des commandes qui donnent pour instruction à l’appareil d’effectuer une action. Pour plus d’informations, consultez [Envoyer et recevoir des messages avec IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Commandes

Dans IoT Plug-and-Play, les commandes définies dans une [interface](#interface) représentent les méthodes qui peuvent être exécutées sur le [jumeau numérique](#digital-twin). Par exemple, une commande pour redémarrer un appareil.

### <a name="component"></a>Composant

Dans IoT Plug-and-Play, les composants vous permettent de créer une [interface](#interface) de modèle qui représente un assemblage d'autres interfaces. Un [modèle d’appareil](#device-model) peut combiner plusieurs interfaces sous forme de composants (par exemple, un modèle comportant un composant de commutateur et un composant de thermostat). Il peut également arriver que plusieurs composants d’un modèle utilisent le même type d’interface (par exemple, un modèle comportant deux composants de thermostat).

### <a name="configuration"></a>Configuration

Dans le contexte d’une [configuration automatique des appareils](../iot-hub/iot-hub-automatic-device-management.md), une configuration dans IoT Hub définit la configuration souhaitée pour un ensemble de jumeaux d’appareils et fournit un ensemble de métriques pour signaler l’état et la progression.

### <a name="connection-string"></a>Chaîne de connexion

Vous utilisez des chaînes de connexion dans votre code d’application afin d’encapsuler les informations requises pour se connecter à un point de terminaison. Une chaîne de connexion inclut généralement l’adresse du point de terminaison et des informations de sécurité, mais les formats de chaîne de connexion varient selon les services. Il existe deux types de chaîne de connexion associés au service IoT Hub :

- Les *Chaînes de connexion d’appareil* permettent aux appareils de se connecter aux points de terminaison visibles par l’appareil sur un hub IoT.

- Les *Chaînes de connexion IoT Hub* activent des applications principales pour se connecter aux points de terminaison côté service sur un hub IoT.

### <a name="custom-endpoints"></a>Points de terminaison personnalisés

Vous pouvez créer des [points de terminaison](../iot-hub/iot-hub-devguide-endpoints.md) personnalisés sur un hub IoT pour transmettre les messages envoyés à l’aide d’une [règle de routage](#routing-rules). Les points de terminaison personnalisés se connectent directement à un hub d’événements, à une file d’attente Service Bus ou à une rubrique Service Bus.

### <a name="custom-gateway"></a>Passerelle personnalisée

Une passerelle assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub). Azure IoT Edge permet de créer des passerelles personnalisées qui implémentent une logique personnalisée pour gérer les messages, les conversions de protocole personnalisées et d'autres opérations à la périphérie.

## <a name="d"></a>D

### <a name="data-point-message"></a>Message de point de données

Un message de point de données est un message [appareil-à-cloud](#device-to-cloud) qui contient des données de [télémétrie](#telemetry) telles que la vitesse du vent ou la température.

### <a name="default-component"></a>Composant par défaut

Dans IoT Plug-and-Play, tous les [modèles d'appareils](#device-model) disposent d'un composant par défaut. Un modèle d’appareil simple n’a qu’un composant par défaut ; ce type de modèle est également appelé « appareil sans composant ». Un modèle plus complexe comporte plusieurs composants imbriqués sous le composant par défaut.

### <a name="device-certification"></a>Certification d’appareil

Le programme de certification d’appareil IoT Plug-and-Play vérifie qu’un appareil répond aux exigences de certification IoT Plug-and-Play. Vous pouvez ajouter un appareil certifié au [catalogue des appareils certifiés pour Azure IoT](https://aka.ms/devicecatalog).

### <a name="device-model"></a>Modèle de l'appareil

Un modèle d'appareil utilise le [langage DTDL (Digital Twins Definition Language)](#digital-twins-definition-language) pour décrire les capacités d'un appareil IoT Plug-and-Play. Un modèle d'appareil simple utilise une interface unique pour décrire les capacités de l'appareil. Un modèle d'appareil plus complexe comprend plusieurs composants, chacun décrivant un ensemble de capacités. Pour plus d'informations, consultez [Composants IoT Plug-and-Play dans les modèles](../iot-pnp/concepts-components.md).

### <a name="device-builder"></a>Générateur d’appareils

Les générateurs d’appareils utilisent un [modèle d’appareil](#device-model) et des [interfaces](#interface) lors de l’implémentation du code à exécuter sur un [appareil IoT Plug-and-Play](#iot-plug-and-play-device). Ils s'appuient généralement sur l'un des [kits de développement logiciel Azure IoT device SDK](#azure-iot-device-sdks) pour implémenter le client d'appareil.

### <a name="device-modeling"></a>Modélisation d’appareil

Un [générateur d’appareils](#device-builder) ou un [générateur de modules](#module-builder) utilise le [langage DTDL (Digital Twins Definition Language)](#digital-twins-definition-language) pour modéliser les capacités d’un [appareil IoT Plug-and-Play](#iot-plug-and-play-device). Un [générateur de solutions](#solution-builder) peut configurer une solution IoT à partir du modèle.

### <a name="desired-configuration"></a>Configuration souhaitée

Dans le contexte d’un [jumeau d’appareil](../iot-hub/iot-hub-devguide-device-twins.md), une configuration souhaitée fait référence à l’ensemble complet des propriétés et métadonnées dans le jumeau d’appareil, qui doivent être synchronisées avec l’appareil.

### <a name="desired-properties"></a>Propriétés souhaitées

Dans le contexte d’un [jumeau d’appareil](../iot-hub/iot-hub-devguide-device-twins.md), les propriétés souhaitées sont une sous-section du jumeau d’appareil qui est utilisée avec des [propriétés signalées](#reported-properties) pour synchroniser une configuration ou une condition d’appareil. Les propriétés souhaitées peuvent être définies uniquement par une [application principale](#back-end-app) et observées par l’[application pour appareil](#device-app).

### <a name="device-to-cloud"></a>Appareil-à-cloud

Qualifie des messages envoyés à partir d’un appareil connecté vers [IoT Hub](#iot-hub). Ces messages peuvent être des messages de [point de données](#data-point-message) ou [interactifs](#interactive-message). Pour plus d’informations, consultez [Envoyer et recevoir des messages avec IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="device"></a>Appareil

Dans le contexte d’IoT, un appareil est généralement un petit dispositif informatique autonome qui peut collecter des données ou contrôler d’autres appareils. Par exemple, un appareil peut être un dispositif de surveillance de l’environnement ou un contrôleur pour les systèmes de ventilation et d’arrosage dans une serre. Le [catalogue d’appareils](https://catalog.azureiotsolutions.com/) fournit la liste des appareils certifiés pour fonctionner avec [IoT Hub](#iot-hub).

### <a name="device-app"></a>Application pour appareil

Une application pour appareil s’exécute sur votre [appareil](#device) et gère la communication avec votre [hub IoT](#iot-hub). En règle générale, vous utilisez l’un des kits [Azure IoT device SDK](#azure-iot-device-sdks) lorsque vous implémentez une application pour appareil. Dans de nombreux didacticiels sur IoT, vous utilisez un [appareil simulé](#simulated-device) pour des raisons pratiques.

### <a name="device-condition"></a>Condition d’appareil

Fait référence aux informations d’état d’un appareil, telles que la méthode de connectivité utilisée, signalée par une [application pour appareil](#device-app). Des [applications pour appareil](#device-app) peuvent également signaler leurs capacités. Vous pouvez interroger des informations de condition et de capacité en utilisant des jumeaux d’appareil.

### <a name="device-data"></a>Données d’appareil

Les données d’appareil font référence aux données stockées pour chaque appareil dans le [registre des identités](#identity-registry) d’IoT Hub. Il est possible d’importer et d’exporter ces données.

### <a name="device-identity"></a>Identité d’appareil

L’identité d’appareil est l’identificateur unique attribué à chaque appareil inscrit dans le [registre des identités](#identity-registry).

### <a name="device-management"></a>Gestion des appareils

La gestion des appareils couvre le cycle de vie complet associé à la gestion des appareils dans votre solution IoT, comprenant la planification, l’approvisionnement, la configuration, la surveillance et la mise hors service.

### <a name="device-management-patterns"></a>Modèle de gestion des appareils

[Hub IoT](#iot-hub) permet d’effectuer les opérations courantes de gestion des appareils, dont le redémarrage, les réinitialisations aux paramètres d’usine et les mises à jour de microprogramme.

### <a name="device-rest-api"></a>API REST d’appareil

Vous pouvez utiliser [l’API REST d’appareil](/rest/api/iothub/device) d’un appareil pour envoyer des messages appareil-à-cloud à un hub IoT et recevoir des messages [cloud-à-appareil](#cloud-to-device) d’un hub IoT. En règle générale, vous devez utiliser l’un des kits [device SDK](#azure-iot-device-sdks) comme indiqué dans les didacticiels sur IoT Hub.

### <a name="device-provisioning"></a>Approvisionnement des appareils

L’approvisionnement des appareils est le processus d’ajout des [données d’appareil](#device-data) initiales aux magasins dans votre solution. Pour permettre à un nouvel appareil de se connecter à votre hub, vous devez ajouter un ID et des clés d’appareil au [registre des identités](#identity-registry) d’IoT Hub. Dans le cadre du processus d’approvisionnement, vous devrez peut-être initialiser les données spécifiques à l’appareil dans d’autres magasins de la solution.

### <a name="device-twin"></a>Jumeau d’appareil

Un jumeau d’appareil est un document JSON contenant des informations d’état d’appareil telles que des métadonnées, des configurations et des conditions. IoT Hub conserve une représentation d’appareil pour chaque appareil que vous configurez dans votre IoT Hub. Les jumeaux d’appareil vous permettent de synchroniser des conditions d’appareil et des configurations entre l’appareil et le back-end de la solution. Vous pouvez interroger des jumeaux d'appareil pour localiser des appareils spécifiques et déterminer l'état d'opérations longues.

### <a name="direct-method"></a>Méthode directe

Une [méthode directe](../iot-hub/iot-hub-devguide-direct-methods.md) est un moyen de déclencher une méthode à exécuter sur un appareil en appelant une API sur votre hub IoT.

### <a name="digital-twin"></a>Jumeau numérique

Un jumeau numérique est une collection de données numériques qui représente un objet physique. Les modifications apportées à l'objet physique se reflètent dans le jumeau numérique. Dans certains scénarios, vous pouvez utiliser le jumeau numérique pour manipuler l'objet physique. Le [service Azure Digital Twins](../digital-twins/index.yml) utilise des modèles exprimés en [langage DTDL (Digital Twins Definition Language)](#digital-twins-definition-language) pour activer un large éventail de solutions basées sur le cloud qui ont recours à des jumeaux numériques. Un appareil [IoT Plug-and-Play](../iot-pnp/index.yml) possède un jumeau numérique, décrit par un [modèle d'appareil](#device-model) DTDL.

### <a name="digital-twin-change-events"></a>Événements de changement de jumeau numérique

Lorsqu'un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) est connecté à un hub IoT, le hub peut utiliser ses capacités de routage pour envoyer des notifications sur les changements de jumeau numérique. Par exemple, chaque fois que la valeur d'une [propriété](#properties) change sur un appareil, IoT Hub peut envoyer une notification à un point de terminaison, comme un hub Event Hub.

### <a name="digital-twins-definition-language"></a>Langage DTDL (Digital Twins Definition Language)

Langage de description des modèles et des interfaces pour les [appareils IoT Plug-and-Play](#iot-plug-and-play-device). Utilisez la [version 2 du langage DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) pour décrire les capacités d'un [jumeau numérique](#digital-twin) et permettre à la plateforme IoT et aux solutions IoT d'utiliser la sémantique de l'entité.

### <a name="digital-twin-route"></a>Route de jumeau numérique

Il s'agit d'un itinéraire configuré dans un hub IoT pour remettre des [événements de changement de jumeau numérique](#digital-twin-change-events) à un point de terminaison, comme un hub Event Hub.

## <a name="e"></a>E

### <a name="endpoint"></a>Point de terminaison

Un hub IoT expose plusieurs [points de terminaison](../iot-hub/iot-hub-devguide-endpoints.md) qui permettent à vos applications de s’y connecter. Des points de terminaison visibles par l’appareil permettent aux appareils d’effectuer des opérations telles que l’envoi de messages [appareil-à-cloud](#device-to-cloud) et la réception de messages [cloud-à-appareil](#cloud-to-device). Des points de terminaison de gestion visibles par le service permettent aux [applications principales](#back-end-app) d’effectuer des opérations telles que la gestion [d’identité d’appareil](#device-identity) et des jumeaux d’appareil. Des [points de terminaison intégrés](#built-in-endpoints) visibles par le service permettent de lire des messages appareil-à-cloud. Vous pouvez créer des [points de terminaison personnalisés](#custom-endpoints) pour recevoir des messages appareil-à-cloud envoyés à l’aide d’une [règle de routage](#routing-rules).

### <a name="event-hub-compatible-endpoint"></a>Point de terminaison compatible Event Hub

Pour lire des messages [appareil-à-cloud](#device-to-cloud) envoyés à votre hub IoT, vous pouvez vous connecter à un point de terminaison sur votre hub et utiliser n’importe quelle méthode compatible Event Hub pour lire ces messages. Les méthodes compatibles Event Hub comprennent l’utilisation des kits [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) et d’[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="f"></a>F

### <a name="field-gateway"></a>Passerelle de champ

Une passerelle de champ assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub), et est généralement déployée localement avec vos appareils. Pour plus d’informations, consultez [Qu’est-ce qu’Azure IoT Hub ?](../iot-hub/about-iot-hub.md).

## <a name="g"></a>G

### <a name="gateway"></a>Passerelle

Une passerelle assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub). Voir aussi [Passerelle de champ](#field-gateway), [Passerelle cloud](#cloud-gateway) et [Passerelle personnalisée](#custom-gateway).

## <a name="i"></a>I

### <a name="identity-registry"></a>Registre des identités

Le [registre des identités](../iot-hub/iot-hub-devguide-identity-registry.md) est le composant intégré d’un IoT Hub, qui stocke des informations sur les appareils autorisés à se connecter à un IoT Hub.

### <a name="interactive-message"></a>Message interactif

Un message interactif est un message [cloud-à-appareil](#cloud-to-device) qui déclenche une action immédiate dans le serveur principal de solution. Par exemple, un appareil peut envoyer une alarme concernant une défaillance qui devrait être journalisée automatiquement dans un système CRM.

### <a name="interface"></a>Interface

Dans IoT Plug-and-Play, une interface décrit les capacités associées qui sont implémentées par un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) ou par un [jumeau numérique](#digital-twin). Il est possible de réutiliser des interfaces sur différents [modèles d’appareil](#device-model). Quand une interface est employée dans un modèle d’appareil, elle définit un [composant](#component) de l’appareil. Un appareil simple contient seulement une interface par défaut.

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge permet d’effectuer un déploiement basé sur le cloud de services Azure et de code spécifique de solution sur des appareils locaux. Les [appareils IoT Edge](#iot-edge-device) peuvent agréger des données provenant d'autres appareils pour effectuer des calculs et des analyses avant d'envoyer les données vers le cloud. Pour en savoir plus, consultez [Azure IoT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>Agent IoT Edge

Partie du runtime IoT Edge responsable des modules de déploiement et de surveillance.

### <a name="iot-edge-device"></a>Appareil IoT Edge

Un appareil IoT Edge utilise des [modules IoT Edge](#iot-edge-module) conteneurisés pour exécuter des services Azure, des services tiers ou votre propre code. Sur l'appareil IoT Edge, le [runtime IoT Edge](#iot-edge-runtime) gère les modules. Vous pouvez surveiller et gérer un appareil IoT Edge à distance à partir du cloud.

### <a name="iot-edge-automatic-deployment"></a>Déploiement automatique IoT Edge

Un déploiement automatique IoT Edge configure un ensemble cible d’appareils IoT Edge pour exécuter un ensemble de modules IoT Edge. Chaque déploiement s’assure en permanence que tous les appareils qui correspondent à la condition cible exécutent l’ensemble spécifié de modules, même lorsque de nouveaux appareils sont créés ou modifiées pour correspondre à la condition cible. Chaque appareil IoT Edge ne reçoit que le déploiement de priorité la plus élevée pour lequel il répond à la condition cible. En savoir plus sur le [déploiement automatique IoT Edge](../iot-edge/module-deployment-monitoring.md).

### <a name="iot-edge-deployment-manifest"></a>Manifeste de déploiement IoT Edge

Document JSON contenant les informations à copier dans un ou plusieurs jumeaux de module d’appareils IoT Edge pour déployer un ensemble de modules, d’itinéraires et de propriétés souhaitées des modules associés.

### <a name="iot-edge-gateway-device"></a>Appareil de passerelle IoT Edge

Appareil IoT Edge avec appareil en aval. L’appareil en aval peut être un appareil IoT Edge ou non IoT Edge.

### <a name="iot-edge-hub"></a>Hub IoT Edge

Partie du runtime IoT Edge responsable des communications entre modules, des communications en amont (vers IoT Hub) et en aval (depuis IoT Hub).

### <a name="iot-edge-leaf-device"></a>Appareil de nœud terminal IoT Edge

Appareil IoT Edge avec aucun appareil en aval.

### <a name="iot-edge-module"></a>Module IoT Edge

Un module IoT Edge est un conteneur Docker que vous pouvez déployer sur des appareils IoT Edge. Il effectue une tâche spécifique, comme l’ingestion de messages provenant d’appareils, la transformation de messages ou l’envoi de messages à un IoT Hub. Il communique avec d’autres modules et envoie des données au runtime IoT Edge. [Comprendre les exigences et outils de développement de modules IoT Edge](../iot-edge/module-development.md).

### <a name="iot-edge-module-identity"></a>Identité de module IoT Edge

Enregistrement dans le registre d’identités de module IoT Hub détaillant les informations d’identification d’existence et de sécurité utilisées par un module pour s’authentifier à Edge Hub ou un IoT Hub.

### <a name="iot-edge-module-image"></a>Image de module IoT Edge

Image Docker utilisée par le runtime IoT Edge pour instancier des instances de module.

### <a name="iot-edge-module-twin"></a>Représentation de module IoT Edge

Document JSON permanent dans l’IoT Hub qui stocke les informations d’état d’une instance de module.

### <a name="iot-edge-priority"></a>Priorité des déploiements IoT Edge

Lorsque deux déploiements IoT Edge ciblent le même appareil, le déploiement avec la priorité plus élevée est appliqué. Si les deux déploiements ont la même priorité, le déploiement avec la date de création la plus tardive est appliqué. En savoir plus sur la [priorité](../iot-edge/module-deployment-monitoring.md#priority).

### <a name="iot-edge-runtime"></a>Runtime IoT Edge

Le runtime IoT Edge inclut tout ce que Microsoft distribue pour l’installation sur un appareil IoT Edge. Il comprend l’agent Edge, le hub Edge et le démon de sécurité IoT Edge.

### <a name="iot-edge-set-modules-to-a-single-device"></a>Modules d’ensemble IoT Edge sur un seul appareil

Opération qui copie le contenu d'un manifeste IoT Edge sur un jumeau de module d'appareil. L'API sous-jacente est une configuration d'application générique, qui utilise simplement un manifeste IoT Edge en tant qu'entrée.

### <a name="iot-edge-target-condition"></a>Condition cible IoT Edge

Dans un déploiement IoT Edge, la condition cible est une condition booléenne sur les balises des jumeaux d'appareil qui permet de sélectionner les appareils cibles du déploiement, par exemple, **tag.environment = prod**. La condition cible est évaluée en permanence pour inclure les nouveaux appareils qui répondent aux exigences ou pour supprimer les appareils qui n’y répondent plus. En savoir plus sur la [condition cible](../iot-edge/module-deployment-monitoring.md#target-condition)

### <a name="iot-hub"></a>IoT Hub

IoT Hub est un service Azure entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un back-end de solution. Pour plus d’informations, consultez [Qu’est-ce qu’Azure IoT Hub ?](../iot-hub/about-iot-hub.md). Votre abonnement Azure vous permet de créer des hubs IoT pour gérer vos charges de travail de messagerie IoT.

### <a name="iot-hub-metrics"></a>Métriques IoT Hub

Les métriques IoT Hub fournissent des données sur l’état des hubs IoT dans votre abonnement Azure. Grâce aux métriques IoT Hub, vous pouvez évaluer l’intégrité globale du service et des appareils connectés à ce dernier. Les métriques IoT Hub peuvent vous aider à voir l’état de votre hub IoT et à examiner des problèmes constituant des causes premières sans devoir contacter le support Azure. Pour plus d’informations, consultez [Surveiller IoT Hub](../iot-hub/monitor-iot-hub.md).

### <a name="iot-hub-query-language"></a>Langage de requête IoT Hub

Le [langage de requête IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) est un langage similaire à SQL, qui vous permet d'interroger vos [Travaux](#job), jumeaux numériques et jumeaux d'appareil.

### <a name="iot-hub-resource-rest-api"></a>API REST de ressource IoT Hub

[L’API REST de ressource IoT Hub](/rest/api/iothub/iothubresource) vous permet de gérer les hubs IoT dans votre abonnement Azure en effectuant des opérations telles que la création, la mise à jour et la suppression de hubs.

### <a name="iot-solution-accelerators"></a>Accélérateurs de solution IoT

Les accélérateurs de solution Azure IoT regroupent plusieurs services Azure dans des solutions. Ces solutions vous permettent de démarrer rapidement avec des implémentations de bout en bout de scénarios IoT courants. Pour plus d’informations, consultez [Présentation des accélérateurs de solution Azure IoT](../iot-accelerators/about-iot-accelerators.md).

### <a name="the-iot-extension-for-azure-cli"></a>Extension IoT pour Azure CLI 

[L’extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension) est un outil de ligne de commande multiplateforme. Cet outil vous permet de gérer vos appareils dans le [registre des identités](#identity-registry), d’envoyer et de recevoir des messages et des fichiers sur vos appareils, et de surveiller les opérations de votre IoT Hub.

### <a name="iot-plug-and-play-bridge"></a>Pont IoT Plug-and-Play

Le pont IoT Plug-and-Play est une application open source permettant à des capteurs ou périphériques attachés à des passerelles Windows ou Linux de se connecter en tant qu’[appareils IoT Plug-and-Play](#iot-plug-and-play-device).

### <a name="iot-plug-and-play-device"></a>Appareil IoT Plug-and-Play

Un appareil IoT Plug-and-Play est généralement un petit appareil informatique autonome qui collecte des données ou contrôle d’autres appareils, et exécute un logiciel ou un microprogramme implémentant un [modèle d’appareil](#device-model).  Par exemple, un appareil IoT Plug-and-Play peut être un appareil de supervision de l’environnement ou un contrôleur pour un système d’irrigation agricole intelligent. Un appareil IoT Plug-and-Play peut être implémenté directement ou en tant que module IoT Edge. Vous pouvez écrire des solutions IoT hébergées dans le cloud pour commander, contrôler et recevoir les données provenant d’appareils IoT Plug-and-Play.

### <a name="iot-plug-and-play-conventions"></a>Conventions IoT Plug-and-Play

Les [appareils](#iot-plug-and-play-device) IoT Plug-and-Play doivent suivre un ensemble de conventions lorsqu’ils échangent des données avec une solution.

## <a name="j"></a>J

### <a name="job"></a>Travail

Votre serveur principal de solution peut utiliser des [travaux](../iot-hub/iot-hub-devguide-jobs.md) pour planifier et suivre des activités sur un ensemble d’appareils inscrits auprès de votre IoT Hub. Ces activités comprennent la mise à jour des [propriétés souhaitées](#desired-properties) de l’appareil, la mise à jour des [balises](#tags) de jumeau d’appareil et l’appel de [méthodes directes](#direct-method). [IoT Hub](#iot-hub) utilise également des travaux pour [importer dans et exporter](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) depuis le [registre des identités](#identity-registry).

## <a name="m"></a>M

### <a name="model-id"></a>ID de modèle

Lorsqu'un appareil IoT Plug-and-Play se connecte à un hub IoT, il envoie l'**ID du modèle** [DTDL](#digital-twins-definition-language) qu'il implémente. Cet ID permet à la solution de trouver le modèle d'appareil.

### <a name="model-repository"></a>Référentiel de modèles

Un référentiel de modèles stocke des [modèles d’appareil](#device-model) et des [interfaces](#interface).

### <a name="model-repository-rest-api"></a>API REST de référentiel de modèles

Il s’agit d’une API permettant de gérer les référentiels de modèles et d’interagir avec eux. Par exemple, vous pouvez l’utiliser pour ajouter et rechercher des [modèles d’appareil](#device-model).

### <a name="module-builder"></a>Générateur de modules

Un générateur de modules utilise un [modèle d’appareil](#device-model) et des [interfaces](#interface) lors de l’implémentation du code à exécuter sur un [appareil IoT Plug-and-Play](#iot-plug-and-play-device). Les générateurs de modules implémentent le code sous la forme d’un module ou d’un module IoT Edge à déployer sur le runtime IoT Edge d’un appareil.

### <a name="modules"></a>Modules

Côté appareil, les kits IoT Hub device SDK vous permettent de créer des [modules](../iot-hub/iot-hub-devguide-module-twins.md) dont chacun ouvre une connexion indépendante à IoT Hub. Cette fonctionnalité vous permet d’utiliser des espaces de noms distincts pour les différents composants de votre appareil.

L'identité de module et le jumeau de module fournissent les mêmes fonctionnalités que l'[identité d'appareil](#device-identity) et le [jumeau d'appareil](#device-twin), mais avec une plus grande précision. Ainsi, les appareils compatibles, tels que les appareils basés sur le système d’exploitation ou les appareils avec microprogramme gérant plusieurs composants, peuvent isoler la configuration et les conditions pour chacun de ces composants.

### <a name="module-identity"></a>Identité de module

L'identité de module est l'identificateur unique attribué à chaque module qui appartient à un appareil. L’identité de module est également enregistrée dans le [registre des identités](#identity-registry).

### <a name="module-twin"></a>Jumeau de module

À l’image d’un jumeau d’appareil, un jumeau de module est un document JSON contenant des informations d’état de module telles que des métadonnées, des configurations et des conditions. IoT Hub conserve un jumeau de module pour chaque identité de module que vous provisionnez sous une identité d’appareil dans votre hub IoT. Les jumeaux de module vous permettent de synchroniser des conditions de module et des configurations entre le module et le backend de la solution. Vous pouvez interroger des jumeaux de module pour localiser des modules spécifiques et déterminer l’état d’opérations longues.

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) est l’un des protocoles de messagerie qu’[IoT Hub](#iot-hub) prend en charge pour communiquer avec des appareils. Pour plus d’informations sur les protocoles de messagerie pris en charge par IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

## <a name="o"></a>O

### <a name="operations-monitoring"></a>Surveillance des opérations

La surveillance des opérations d’[IoT Hub](../iot-hub/iot-hub-operations-monitoring.md) vous permet de surveiller l’état des opérations sur votre IoT Hub en temps réel. [IoT Hub](#iot-hub) suit les événements liés à différentes catégories d’opérations. Vous pouvez opter pour l’envoi des événements d’une ou plusieurs catégories à un point de terminaison d’IoT Hub en vue de leur traitement. Vous pouvez surveiller les données des erreurs ou configurer un traitement plus complexe basé sur des modèles de données.

## <a name="p"></a>P

### <a name="physical-device"></a>Appareil physique

Un appareil physique est un appareil réel, par exemple un Raspberry Pi, qui se connecte à un hub IoT. Pour des raisons pratiques, bon nombre des didacticiels sur IoT Hub utilisent des [appareils simulés](#simulated-device) pour vous permettre d’exécuter les exemples sur votre ordinateur local.

### <a name="primary-and-secondary-keys"></a>Clés primaires et secondaires

Lorsque vous vous connectez à un point de terminaison visible par l’appareil ou par le service sur un hub IoT, votre [chaîne de connexion](#connection-string) inclut la clé vous permettant d’accéder. Lorsque vous ajoutez un appareil au [registre des identités](#identity-registry) ou une [stratégie d’accès partagé](#shared-access-policy) à votre hub, le service génère une clé primaire et une clé secondaire. Le fait de disposer de deux clés vous permet de passer d’une clé à un autre lorsque vous mettez à jour une clé sans perdre l’accès au hub IoT.

### <a name="properties"></a>Propriétés

Les propriétés sont des champs de données définis dans une [interface](#interface). Ils représentent un état d’un jumeau numérique. Vous pouvez déclarer des propriétés comme étant accessibles en lecture seule ou en écriture. Les propriétés en lecture seule, comme le numéro de série, sont définies par du code s’exécutant sur l’[appareil IoT Plug-and-Play](#iot-plug-and-play-device) lui-même.  Les propriétés accessibles en écriture, comme un seuil d’alarme, sont généralement définies à partir de la solution IoT cloud.

### <a name="protocol-gateway"></a>Passerelle de protocole

Une passerelle de protocole est généralement déployée dans le cloud. Elle fournit des services de traduction de protocole pour les appareils qui se connectent à [IoT Hub](#iot-hub). Pour plus d’informations, consultez [Qu’est-ce qu’Azure IoT Hub ?](../iot-hub/about-iot-hub.md).

## <a name="r"></a>R

### <a name="reported-configuration"></a>Configuration signalée

Dans le contexte d’un [jumeau d’appareil](../iot-hub/iot-hub-devguide-device-twins.md), une configuration souhaitée fait référence à l’ensemble complet des propriétés et métadonnées dans le jumeau d’appareil, qui doivent être signalées au serveur principal de solution.

### <a name="reported-properties"></a>Propriétés signalées

Dans le contexte d’un [jumeau d’appareil](../iot-hub/iot-hub-devguide-device-twins.md), les propriétés signalées sont une sous-section du jumeau d’appareil qui est utilisé avec des [propriétés souhaitées](#desired-properties) pour synchroniser une configuration ou une condition d’appareil. Les propriétés signalées peuvent être définies uniquement par l’[application d’appareil](#device-app), et peuvent être lues et interrogées par une [application principale](#back-end-app).

### <a name="retry-policy"></a>Stratégie de nouvelle tentative

Vous utilisez une stratégie de nouvelle tentative pour gérer des [erreurs temporaires](/azure/architecture/best-practices/transient-faults) lorsque vous vous connectez à un service cloud.

### <a name="routing-rules"></a>Règles de routage

Vous pouvez configurer des [règles de routage](../iot-hub/iot-hub-devguide-messages-read-custom.md) dans votre hub IoT pour acheminer les messages appareil-à-cloud vers un [point de terminaison intégré](#built-in-endpoints) ou vers des [points de terminaison personnalisés](#custom-endpoints) afin qu’ils soient traités par votre back-end de solution.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SAPL PLAIN

SASL PLAIN est un protocole utilisé par le protocole AMQP pour transférer des jetons de sécurité.

### <a name="service-rest-api"></a>API REST du service

Vous pouvez utiliser [l’API REST du service](/rest/api/iothub/service/configuration) à partir du backend de la solution pour gérer vos appareils. L’API vous permet de récupérer et de mettre à jour les propriétés de [jumeau d’appareil](#device-twin), d’appeler des [méthodes directes](#direct-method) et de planifier des [travaux](#job). En règle générale, vous devez utiliser l’un des kits [service SDK](#azure-iot-service-sdks) comme indiqué dans les didacticiels concernant IoT Hub.

### <a name="shared-access-signature"></a>Signature d’accès partagé

Les signatures d’accès partagé (SAP) sont des mécanismes d’authentification basés sur des hachages sécurisés SHA-256 ou des URI. Une authentification par SAP comporte deux composants : une _stratégie d’accès partagé_ et une _signature d’accès partagé_ (souvent appelée jeton). Un appareil utilise une SAP pour s’authentifier auprès d’un hub IoT. Les [applications principales](#back-end-app) utilisent également une SAP pour s’authentifier auprès des points de terminaison visibles par le service sur un hub IoT. En règle générale, vous incluez le jeton SAP dans la [chaîne de connexion](#connection-string) qu’une application utilise pour établir une connexion à un hub IoT.

### <a name="shared-access-policy"></a>Stratégie d’accès partagé

Une stratégie d’accès partagé définit les autorisations accordées à toute personne disposant d’une [clé primaire ou secondaire](#primary-and-secondary-keys) valide associée à cette stratégie. Vous pouvez gérer les stratégies d’accès partagé et les clés de votre hub via le portail.

### <a name="simulated-device"></a>Appareil simulé

Pour des raisons pratiques, bon nombre des didacticiels sur IoT Hub utilisent des appareils simulés pour vous permettre d’exécuter les exemples sur votre ordinateur local. À l’inverse, un [appareil physique](#physical-device) est un appareil réel, comme un Raspberry Pi, qui se connecte à un hub IoT.

### <a name="solution"></a>Solution

Une _solution_ peut être une solution Visual Studio incluant un ou plusieurs projets. Une _solution_ peut également être une solution IoT incluant des éléments tels que des appareils, [des applications pour appareil](#device-app), un hub IoT, d’autres services Azure et des [applications principales](#back-end-app).

### <a name="solution-builder"></a>Générateur de solutions

Les générateurs de solutions créent la solution back-end. Ils utilisent généralement des ressources Azure, comme IoT Hub et les [référentiels de modèles](#model-repository).

### <a name="system-properties"></a>Propriétés système

Dans le contexte d’un [jumeau d’appareil](../iot-hub/iot-hub-devguide-device-twins.md), les propriétés système sont en lecture seule et incluent des informations sur l’utilisation de l’appareil, telles que l’heure et l’état de connexion de la dernière activité.

## <a name="t"></a>T

### <a name="tags"></a>Balises

Dans le contexte d’un [jumeau d’appareil](../iot-hub/iot-hub-devguide-device-twins.md), les balises sont des métadonnées d’appareil stockées et récupérées par le back-end de solution sous la forme d’un document JSON. Les balises ne sont pas visibles pour les applications sur un appareil.

### <a name="telemetry"></a>Télémétrie

Les appareils collectent des données de télémétrie, telles que la vitesse du vent ou la température, et utilisent des messages de point de données pour envoyer ces données à un hub IoT.

Dans IoT Plug-and-Play, les champs de télémétrie définis au sein d'une [interface](#interface) représentent des mesures. Ces mesures sont généralement des valeurs comme les lectures d’un capteur envoyées par l’[appareil IoT Plug-and-Play](#iot-plug-and-play-device) sous la forme d’un flux de données.

### <a name="token-service"></a>Service d’émission de jeton

Vous pouvez utiliser un service d’émission de jeton pour implémenter un mécanisme d’authentification pour vos appareils. Il utilise une [stratégie d’accès partagé](#shared-access-policy) IoT Hub avec des autorisations **DeviceConnect** pour créer des jetons *device-scoped*. Ces jetons permettent à un appareil de se connecter à votre hub IoT. Un appareil utilise un mécanisme d’authentification personnalisé pour s’authentifier auprès du service d’émission de jeton. Si l’appareil est correctement authentifié, le service d’émission de jeton lui fournit un jeton SAP avec lequel il peut accéder à votre hub IoT.

### <a name="twin-queries"></a>Requêtes de jumeaux

Les [requêtes de jumeaux d’appareil et de module](../iot-hub/iot-hub-devguide-query-language.md) utilisent le langage de requête similaire à SQL d’IoT Hub pour extraire des informations de vos jumeaux d’appareil ou jumeaux de module. Vous pouvez utiliser le même langage de requête IoT Hub pour récupérer des informations sur l’exécution d’un [Travail](#job) dans votre hub IoT.

### <a name="twin-synchronization"></a>Synchronisation des jumeaux

Une synchronisation de jumeau utilise les [propriétés souhaitées](#desired-properties) de vos jumeaux d’appareil ou jumeaux de module pour configurer vos appareils ou modules et extraire de ceux-ci des [propriétés signalées](#reported-properties) à stocker dans le jumeau.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>Certificat client X.509

Un appareil peut utiliser un certificat X.509 pour s’authentifier auprès de [IoT Hub](#iot-hub). L’utilisation d’un certificat X.509 est une alternative à l’utilisation d’un [jeton SAP](#shared-access-signature).
