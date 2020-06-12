---
title: Développement d’appareils pour Azure IoT Central | Microsoft Docs
description: Azure IoT Central est une plateforme d’applications IoT qui simplifie la création de solutions IoT. Cet article fournit une vue d’ensemble du développement d’appareils pour se connecter à votre application IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: aa442e15dbc95709ecf3c818f69301d2f02e9b5b
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417019"
---
# <a name="iot-central-device-development-overview"></a>Vue d’ensemble du développement d’appareils IoT Central

*Cet article s’applique aux développeurs d’appareils.*

Une application IoT Central vous permet de surveiller et de gérer des millions d’appareils tout au long de leur cycle de vie. Cette vue d’ensemble s’adresse aux développeurs d’appareils qui implémentent un code qui s’exécutera sur des appareils connectés à IoT Central.

Les appareils interagissent avec une application IoT Central à l’aide des primitives suivantes :

- La _télémétrie_ représente les données qu’un appareil envoie à IoT Central. Par exemple, un flux de valeurs de température provenant d’un capteur intégré.
- Les _propriétés_ représentent des valeurs d’état qu’un appareil transmet à IoT Central. Par exemple, la version actuelle du microprogramme installé sur l’appareil. Vous pouvez également utiliser des propriétés accessibles en écriture qu’IoT Central peut mettre à jour sur l’appareil.
- Les _commandes_ sont appelées à partir d’IoT Central pour contrôler le comportement d’un appareil. Par exemple, votre application IoT Central peut appeler une commande pour redémarrer un appareil.

Un générateur de solutions est responsable de la configuration des tableaux de bord et des vues dans l’interface utilisateur Web IoT Central pour visualiser les données de télémétrie, gérer les propriétés et appeler les commandes.

## <a name="types-of-device"></a>Types d’appareils

Les sections suivantes décrivent les principaux types d’appareils que vous pouvez connecter à une application IoT Central :

### <a name="standalone-device"></a>Appareil autonome

Un appareil autonome se connecte directement à IoT Central. Un appareil autonome envoie généralement des données de télémétrie à partir de ses capteurs intégrés ou connectés à votre application IoT Central. Les appareils autonomes peuvent également transmettre des valeurs de propriété, recevoir des valeurs de propriété accessibles en écriture, et répondre à des commandes.

### <a name="gateway-device"></a>Appareil de passerelle

Un appareil de passerelle gère un ou plusieurs appareils en aval qui se connectent à votre application IoT Central. Vous utilisez IoT Central pour configurer les relations entre les appareils en aval et l’appareil de passerelle. Pour en savoir plus, voir [Définir un nouveau type d’appareil de passerelle IoT dans votre application Azure IoT Central](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Appareil de périphérie

Un appareil de périphérie se connecte directement à IoT Central, mais agit en tant qu’intermédiaire pour d’autres appareils appelés _appareils de nœud terminal_. Un appareil de périphérie est généralement situé près des appareils de nœud terminal pour lesquels il joue le rôle d’intermédiaire. Les scénarios suivants utilisent des appareils de périphérie :

- Activer les appareils qui ne peuvent pas se connecter directement à IoT Central pour se connecter via l’appareil de périphérie. Par exemple, un appareil de nœud terminal peut utiliser Bluetooth pour se connecter à l’appareil de périphérie, qui se connecte ensuite via Internet à IoT Central.
- Agréger les données de télémétrie avant leur envoi à IoT Central. Cette approche peut aider à réduire les coûts liés à l’envoi de données à IoT Central.
- Contrôler localement des appareils de nœud terminal pour éviter la latence associée à la connexion à IoT Central via Internet.

Un appareil de périphérie peut également envoyer ses propres données de télémétrie, transmettre ses propriétés, et répondre à des commandes et des mises à jour de propriétés accessibles en écriture.

IoT Central ne voit que l’appareil de périphérie et pas les appareils de nœud terminal qui y sont connectés.

Pour en savoir plus, voir [Ajouter un appareil Azure IoT Edge à votre application Azure IoT Central](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Connexion d'un appareil

Azure IoT Central utilise le [service Azure IoT Device Provisioning (DPS)](../../iot-dps/about-iot-dps.md) pour gérer toutes les inscriptions et connexions d’appareils.

L’utilisation de DPS permet :

- à IoT Central de prendre en charge l’intégration et la connexion des appareils à l’échelle ;
- aux clients de générer des informations d’identification d’appareils et de les configurer hors ligne sans avoir à les inscrire via l’interface utilisateur d’IoT Central ;
- aux clients d’utiliser leurs propres ID d’appareils pour inscrire des appareils dans IoT Central. L’utilisation de vos propres ID d’appareils simplifie l’intégration dans des systèmes back-office existants.
- C’est une manière cohérente de connecter des appareils à IoT Central.

Pour plus d’informations, consultez [Se connecter à Azure IoT Central](./concepts-get-connected.md).

### <a name="security"></a>Sécurité

La connexion entre un appareil et votre application IoT Central est sécurisée à l’aide de [signatures d’accès partagé](./concepts-get-connected.md#connect-devices-at-scale-using-sas) ou de [certificats X.509](./concepts-get-connected.md#connect-devices-using-x509-certificates) standard.

### <a name="communication-protocols"></a>Protocoles de communication

Les protocoles de communication qu’un appareil peut utiliser pour se connecter à IoT Central incluent MQTT, AMQP et HTTPS. En interne, IoT Central utilise un hub IoT pour activer la connectivité des appareils. Pour plus d’informations sur les protocoles de communication pris en charge par IoT Hub pour la connectivité des appareils, voir [Choisir un protocole de communication](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implémenter l’appareil

Utilisez l’un des [kits Azure IoT device SDK](#languages-and-sdks) pour implémenter le comportement de votre appareil. Le code doit :

- Inscrire l’appareil auprès de DPS et utiliser les informations de DPS pour se connecter au hub IoT interne de votre application IoT Central.
- Envoyer des données de télémétrie au format spécifié par le modèle d’appareil dans IoT Central. IoT Central utilise le modèle d’appareil afin de déterminer comment utiliser les données de télémétrie pour les visualisations et l’analyse.
- Synchroniser les valeurs de propriété entre l’appareil et IoT Central. Le modèle d’appareil spécifie les noms de propriété et les types de données afin qu’IoT Central puisse afficher les informations.
- Implémenter des gestionnaires de commandes pour les commandes spécifiées dans le modèle d’appareil. Le modèle d’appareil spécifie les noms de commande et les paramètres que l’appareil doit utiliser.

Pour plus d’informations sur le rôle des modèles d’appareil, consultez [Présentation des modèles d’appareils](./concepts-device-templates.md).

Pour accéder à un exemple de code, consultez [Créer et connecter une application cliente Node.js](./tutorial-connect-device-nodejs.md) ou [Créer et connecter une application cliente Python](./tutorial-connect-device-python.md).

### <a name="languages-and-sdks"></a>Langues et kits de développement logiciel (SDK)

Pour plus d’informations sur les langages et kits de développement logiciel (SDK) pris en charge, voir [Comprendre et utiliser les kits de développement logiciel (SDK) Azure IoT Hub](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes développeur d’appareils et que vous souhaitez vous immerger dans du code, l’étape suivante suggérée consiste à [Créer et connecter une application cliente à votre application Azure IoT Central](./tutorial-connect-device-nodejs.md).

Si vous souhaitez en savoir plus sur l’utilisation d’IoT Central, nous vous suggérons de tester les démarrages rapides, en commençant par [Créer une application Azure IoT Central](./quick-deploy-iot-central.md).
