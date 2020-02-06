---
title: Glossaire terminologique - Préversion d’IoT Plug-and-Play | Microsoft Docs
description: 'Concepts : glossaire des termes courants relatifs à la préversion d’IoT Plug-and-Play.'
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ef5ce9cc1cda7f1ff6b1985771e20cb20123e264
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025637"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glossaire terminologique pour la préversion d’IoT Plug-and-Play

Définitions des termes courants utilisés dans les articles sur IoT Plug-and-Play.

## <a name="azure-certified-for-iot-portal"></a>Portail Azure Certified pour IoT

Vous pouvez utiliser le site web [portail Azure Certified pour IoT](https://aka.ms/ACFI) pour :

- Effectuer le [processus de certification](#device-certification) pour votre [appareil IoT Plug-and-Play](#iot-plug-and-play-device).
- Rechercher des [modèles de capacité d’appareil](#device-capability-model).
- Publier un modèle de capacité d’appareil dans le [référentiel de modèles public](#public-model-repository).

## <a name="azure-cli"></a>Azure CLI

Azure CLI est un outil en ligne de commande multiplateforme permettant de gérer des ressources Azure. L’extension Azure IoT pour Azure CLI est un outil en ligne de commande permettant d’interagir avec des appareils [IoT Plug-and-Play](#iot-plug-and-play-device) et de les tester. Vous pouvez utiliser l’extension pour :

- Vous connecter un appareil IoT Plug-and-Play.
- Voir la [télémétrie](#telemetry) envoyée par l’appareil.
- Utiliser des [propriétés](#properties) de l’appareil.
- Appeler des [commandes](#commands) de l’appareil.
- Gérer des [référentiels de modèles](#model-repository), des [interfaces](#interface) et des [modèles de capacité d’appareil](#device-capability-model).

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central est une solution SaaS (software-as-a-service) entièrement managée, qui facilite la connexion, la supervision et la gestion de vos [appareils IoT Plug-and-Play](#iot-plug-and-play-device). Vous pouvez utiliser des [modèles de capacité d’appareil](#device-capability-model) pour configurer automatiquement une application IoT Central pour superviser et gérer vos appareils.

## <a name="azure-iot-certification-service"></a>Service de certification Azure IoT

Le service de certification Azure IoT effectue un ensemble de tests de certification quand vous soumettez un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) pour certification via le [portail Azure Certified pour IoT](#azure-certified-for-iot-portal). Avant de pouvoir ajouter un appareil au [catalogue d’appareils Certified pour IoT](#certified-for-iot-device-catalog), l’appareil doit être certifié.

## <a name="azure-iot-tools-extension"></a>Extension Azure IoT Tools

Azure IoT Tools est une collection d’extensions dans [Visual Studio Code](#visual-studio-code) qui vous permettent d’interagir avec IoT Hub et de développer des appareils IoT. Pour le développement d’appareils IoT Plug-and-Play, il vous aide à :

- Créer des [modèles de capacité d’appareil](#device-capability-model) et des [interfaces](#interface).
- Publier des [référentiels de modèles](#model-repository).
- Générer un squelette de code pour implémenter l’application d’appareil.

## <a name="azure-iot-explorer-tool"></a>Outil Explorateur Azure IoT

L’explorateur Azure IoT est un outil graphique que vous pouvez utiliser pour interagir avec vos [appareils IoT Plug-and-Play](#iot-plug-and-play-device) et les tester. Après avoir installé l’outil sur votre machine locale, vous pouvez l’utiliser pour :

- Voir les appareils connectés à votre [hub IoT](#azure-iot-hub).
- Vous connecter un appareil IoT Plug-and-Play.
- Voir la [télémétrie](#telemetry) envoyée par l’appareil.
- Utiliser des [propriétés](#properties) de l’appareil.
- Appeler des [commandes](#commands) de l’appareil.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub est un service managé, hébergé dans le cloud, qui agit en tant que concentrateur de messages central pour la communication bidirectionnelle entre votre application IoT et les appareils qu’il gère. Les [appareils IoT Plug-and-Play](#iot-plug-and-play-device) peuvent se connecter à un hub IoT. Une solution IoT utilise un hub IoT pour :

- Permettre aux appareils d’envoyer de la télémétrie à une solution cloud.
- Permettre à une solution cloud de gérer les appareils connectés.

## <a name="azure-iot-device-sdk"></a>Azure IoT device SDK

Il existe des SDK d’appareil pour plusieurs langages, que vous pouvez utiliser pour créer des applications clientes d’appareil IoT Plug-and-Play. Une des exigences pour la [certification d’un appareil](#device-certification) est que le code du client d’appareil utilise un des SDK d’appareil Azure IoT.

## <a name="certified-for-iot-device-catalog"></a>Catalogue d’appareils Certified pour IoT

Le [catalogue d’appareils Certified pour IoT](https://catalog.azureiotsolutions.com/) liste les [appareils IoT Plug-and-Play](#iot-plug-and-play-device) qui ont réussi les tests de [certification d’appareil](#device-certification). [Modèles de capacité d’appareil](#device-capability-model) pour les appareils IoT Plug-and-Play dans le catalogue et publiés dans le référentiel de modèles public.

## <a name="commands"></a>Commandes

Les commandes définies dans une [interface](#interface) représentent les méthodes qui peuvent être exécutées sur le [jumeau numérique](#digital-twin). Par exemple, une commande pour redémarrer un appareil.

## <a name="common-interface"></a>Interface commune

Tous les [appareils IoT Plug-and-Play](#iot-plug-and-play-device) sont censés implémenter des [interfaces](#interface) communes. Par exemple, l’interface d’informations de l’appareil définit les informations sur le matériel et le système d’exploitation sur l’appareil. La [certification d’appareil](#device-certification) nécessite que votre appareil implémente plusieurs interfaces communes. Vous pouvez récupérer des définitions d’interfaces communes dans le référentiel de modèles public.

## <a name="company-model-repository"></a>Référentiel de modèles d’entreprise

Une organisation peut utiliser un [référentiel de modèles](#model-repository) d’entreprise comme magasin privé pour les [modèles de capacité d’appareil](#device-capability-model) et des [interfaces](#interface) .

## <a name="connection-string"></a>Chaîne de connexion

Une chaîne de connexion encapsule les informations nécessaires pour se connecter à un point de terminaison. Une chaîne de connexion inclut généralement l’adresse du point de terminaison et des informations de sécurité, mais les formats de chaîne de connexion varient selon les services. Il existe deux types de chaîne de connexion associés au service IoT Hub :

- Les chaînes de connexion d’appareil permettent aux [appareils IoT Plug-and-Play](#iot-plug-and-play-device) de se connecter aux points de terminaison visibles par l’appareil sur un hub IoT. Le code client sur un appareil utilise la chaîne de connexion pour établir une connexion sécurisée avec un hub IoT.
- Les chaînes de connexion IoT Hub permettent aux applications back-end de se connecter aux points de terminaison visibles par le service sur un hub IoT. Ces solutions et ces outils gèrent le hub IoT et les appareils qui y sont connectés.
- Les chaînes de connexion de référentiel de modèles d’entreprise permettent d’utiliser des solutions et des outils back-end pour se connecter en toute sécurité à un [référentiel de modèles d’entreprise](#company-model-repository). Ces solutions et outils utilisent ou gèrent les [modèles de capacité d’appareil](#device-capability-model) et les [interfaces](#interface) dans le référentiel.

## <a name="device-capability-model"></a>Modèle de capacité d’appareil

Un modèle de capacité d’appareil décrit un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) et définit l’ensemble des [interfaces](#interface) qu’il implémente. Un modèle de capacité d’appareil correspond généralement à un appareil physique, à un produit ou à une référence SKU. Vous utilisez le [langage DTDL (Digital Twin Definition Language)](#digital-twin-definition-language) pour créer un modèle de capacité d’appareil.

## <a name="device-certification"></a>Certification d’appareil

La certification des appareils est le processus de certification d’un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) qui permet de l’ajouter au [catalogue d’appareils Certified pour IoT](#certified-for-iot-device-catalog), et d’ajouter son [modèle de capacité d’appareil](#device-capability-model) et ses [interfaces](#interface) au [référentiel de modèles public](#public-model-repository).

## <a name="device-developer"></a>Développeur d’appareils

Un développeur d’appareil utilise un [modèle de capacité d’appareil](#device-capability-model), des [interfaces](#interface) et un [SDK d’appareil Azure IoT](#azure-iot-device-sdk) pour implémenter le code à exécuter sur un [appareil IoT Plug-and-Play](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modélisation d’appareil

Un [développeur d’appareil](#device-developer) utilise le [DTDL (Digital Twin Definition Language)](#digital-twin-definition-language) pour modéliser les capacités d’un [appareil IoT Plug-and-Play](#iot-plug-and-play-device). Le modèle peut être partagé via un référentiel de modèles. Un développeur d’appareil peut générer un squelette de code de l’appareil à partir du modèle. Un [développeur de solutions](#solution-developer) peut configurer une solution IoT à partir du modèle.

## <a name="device-provisioning-service"></a>Service Device Provisioning

[Azure IoT Central](#azure-iot-central) utilise le service Device Provisioning pour gérer toutes les inscriptions et connexions d’appareils. Pour plus d’informations, consultez [Connectivité des appareils dans Azure IoT Central](../iot-central/core/concepts-get-connected.md). Vous pouvez également utiliser le service Device Provisioning pour gérer l’inscription et la connexion des appareils à votre solution IoT basée sur IoT Hub. Pour plus d’informations, consultez [Provisionnement d’appareils avec le service Azure IoT Hub Device Provisioning](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Enregistrement de l’appareil

Pour qu’un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) puisse se connecter à une solution IoT, il doit être inscrit auprès de la solution. [Azure IoT Central](#azure-iot-central) utilise le [service Device Provisioning](#device-provisioning-service) pour gérer toutes les inscriptions d’appareils. Dans une solution IoT personnalisée, vous pouvez inscrire des appareils auprès de votre hub IoT dans le portail Azure ou par programmation.

## <a name="device-first"></a>Appareil en premier

[Azure IoT Central](#azure-iot-central) prend en charge un scénario d’inscription et de connexion « appareil en premier ». Dans ce scénario, un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) peut se connecter à une application IoT central sans être préalablement inscrit. L’inscription se fait automatiquement quand un appareil se connecte pour la première fois à l’application.

## <a name="digital-twin"></a>Jumeau numérique

Un jumeau numérique est un modèle d’un [appareil IoT Plug-and-Play](#iot-plug-and-play-device). Un jumeau numérique est modélisé avec le [langage DTDL (Digital Twin Definition Language)](#digital-twin-definition-language). Vous pouvez utiliser les [SDK d’appareil Azure IoT](#azure-iot-device-sdk) pour interagir avec des jumeaux numériques à l’exécution. Par exemple, vous pouvez définir une valeur de propriété dans un jumeau numérique sur un appareil : le SDK communique alors cette modification à votre solution IoT dans le cloud.

## <a name="digital-twin-change-events"></a>Événements de changement de jumeau numérique

Quand un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) est connecté à un [hub IoT](#azure-iot-hub), le hub peut utiliser ses capacités de routage pour envoyer des notifications de changements de jumeau numérique. Par exemple, chaque fois que la valeur d’une [propriété](#properties) change sur un appareil, IOT Hub peut envoyer une notification à un point de terminaison, comme une file d’attente Service Bus.

## <a name="digital-twin-definition-language"></a>Langage DTDL (Digital Twin Definition Language)

Langage de description des modèles et des interfaces pour les [appareils IoT Plug-and-Play](#iot-plug-and-play-device). Utilisez le [langage DTDL](https://aka.ms/DTDL) pour décrire les capacités d’un [jumeau numérique](#digital-twin). Vous permettez ainsi à la plateforme IoT et aux solutions IoT de tirer parti de la sémantique de l’entité.

## <a name="digital-twin-route"></a>Route de jumeau numérique

Route configurée dans un [hub IoT](#azure-iot-hub) pour délivrer des [événements de changement de jumeau numérique](#digital-twin-change-events) à un point de terminaison, comme une file d’attente Service Bus.

## <a name="interface"></a>Interface

Une interface décrit les capacités associées qui sont implémentées par un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) ou par un [jumeau numérique](#digital-twin). Vous pouvez réutiliser des interfaces sur différents [modèles de capacité d’appareil](#device-capability-model).

## <a name="iot-hub-query-language"></a>Langage de requête IoT Hub

Le langage de requête IoT Hub est utilisé dans plusieurs objectifs. Par exemple, vous pouvez utiliser le langage pour rechercher des [appareils inscrits](#device-registration) auprès de votre hub IoT ou affiner le comportement de [routage des jumeaux numériques](#digital-twin-route).

## <a name="iot-plug-and-play-device"></a>Appareil IoT Plug-and-Play

Un appareil IoT Plug-and-Play est généralement un petit appareil informatique autonome qui collecte des données ou contrôle d’autres appareils. Il exécute un logiciel ou un microprogramme qui implémente un [modèle de capacité d’appareil](#device-capability-model).  Par exemple, un appareil IoT Plug-and-Play peut être un appareil de supervision de l’environnement ou un contrôleur pour un système d’irrigation agricole intelligent. Vous pouvez écrire des solutions IoT hébergées dans le cloud pour commander, contrôler et recevoir les données provenant d’appareils IoT Plug-and-Play. Le [catalogue d’appareils Azure Certified pour IoT](#certified-for-iot-device-catalog) liste les appareils IoT Plug-and-Play disponibles. Chaque appareil IoT Plug-and-Play du catalogue a été validé et dispose d’un [modèle de capacité d’appareil](#device-capability-model).

## <a name="microsoft-partner-center"></a>Espace partenaires Microsoft

L’[Espace partenaires Microsoft](https://docs.microsoft.com/partner-center/) est l’endroit où votre organisation gère sa relation de bout en bout avec Microsoft. Vous avez besoin d’un compte Espace partenaires Microsoft pour pouvoir certifier votre [appareil IoT Plug-and-Play](#iot-plug-and-play-device) dans le [portail Azure Certified pour IOT](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Découverte de modèles

Quand un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) se connecte à une solution IoT, la solution peut découvrir les capacités de l’appareil en recherchant le [modèle de capacité d’appareil](#device-capability-model). Un appareil peut envoyer son modèle de capacité à la solution, ou la solution peut trouver un modèle de capacité d’appareil dans un [référentiel de modèles](#model-repository).

## <a name="model-repository"></a>Référentiel de modèles

Un référentiel de modèles stocke des [modèles de capacité d’appareil](#device-capability-model) et des [interfaces](#interface). Il n’existe qu’un seul [référentiel de modèles public](#public-model-repository). Les organisations peuvent créer leurs propres référentiels de modèles d’organisation.

## <a name="model-repository-rest-api"></a>API REST de référentiel de modèles

API pour la gestion et l’interaction avec les référentiels de modèles. Par exemple, vous pouvez utiliser l’API pour ajouter des [modèles de capacité d’appareil](#device-capability-model) et pour rechercher des modèles de capacité.

## <a name="properties"></a>Propriétés

Les propriétés sont des champs de données définis dans une [interface](#interface). Ils représentent un état d’un jumeau numérique. Vous pouvez déclarer des propriétés comme étant accessibles en lecture seule ou en écriture. Les propriétés en lecture seule, comme le numéro de série, sont définies par du code s’exécutant sur l’[appareil IoT Plug-and-Play](#iot-plug-and-play-device) lui-même.  Les propriétés accessibles en écriture, comme un seuil d’alarme, sont généralement définies à partir de la solution IoT cloud.

## <a name="public-model-repository"></a>Référentiel de modèles public

Il n’existe qu’un seul référentiel de modèles public qui stocke les [modèles de capacité d’appareil](#device-capability-model) et les [interfaces](#interface) pour les [appareils certifiés](#device-certification). Le référentiel de modèles public stocke également les définitions d’[interfaces communes](#common-interface).

## <a name="registration-id"></a>ID d’enregistrement

Un ID d’inscription identifie de façon univoque un appareil dans le [service Device Provisioning](#device-provisioning-service). Cet ID n’est pas identique à l’ID d’appareil, qui est un identificateur unique pour un appareil dans un [hub IoT](#azure-iot-hub).

## <a name="scope-id"></a>ID d’étendue

L’ID d’étendue identifie de façon univoque une instance du [service Device Provisioning](#device-provisioning-service).

## <a name="shared-access-signature"></a>Signature d’accès partagé

Les signatures d’accès partagé sont un mécanisme d’authentification basé sur des URI ou des hachages sécurisés SHA-256. Une authentification par signature d’accès partagé a deux composants : une stratégie d’accès partagé et une signature d’accès partagé (souvent appelée « jeton »). Un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) utilise une signature d’accès partagé pour s’authentifier auprès d’un [hub IoT](#azure-iot-hub).

## <a name="solution-developer"></a>Développeur de solutions

Un développeur de solutions crée la solution back-end. Un développeur de solutions utilise généralement des ressources Azure, comme [IoT Hub](#azure-iot-hub) et des [référentiels de modèles](#model-repository), ou il utilise [IoT Central](#azure-iot-central).

## <a name="telemetry"></a>Télémétrie

Les champs de télémétrie définis dans une [interface](#interface) représentent des mesures. Ces mesures sont généralement des valeurs comme les lectures d’un capteur envoyées par l’[appareil IoT Plug-and-Play](#iot-plug-and-play-device) sous la forme d’un flux de données.

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code est un éditeur de code moderne disponible pour plusieurs plateformes. Des extensions, comme celles des outils [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), vous permettent de personnaliser l’éditeur pour prendre en charge un large éventail de scénarios de développement.
