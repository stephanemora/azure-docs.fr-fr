---
title: Concepts d’architecture dans Azure IoT Central | Microsoft Docs
description: Cet article présente les concepts clés relatifs à l’architecture d’Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 37e4224ae1347647d15959a55d19d2c6487935d7
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273210"
---
# <a name="azure-iot-central-architecture"></a>Architecture d’Azure IoT Central

Cet article fournit une vue d’ensemble des concepts clés de l’architecture d’Azure IoT Central.

## <a name="devices"></a>Appareils

Les appareils échangent des données avec votre application Azure IoT Central. Un appareil peut :

- Envoyer des mesures, comme de la télémétrie.
- Synchroniser des paramètres avec votre application.

Dans Azure IoT Central, les données qu’un appareil peut échanger avec votre application sont spécifiées dans un modèle d’appareil. Pour plus d’informations sur les modèles d’appareil, consultez [Modèles d’appareil](concepts-device-templates.md).

Pour plus d’informations sur la façon dont les appareils se connectent à votre application Azure IoT Central, consultez [Connectivité des appareils](concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Appareil Azure IoT Edge

En plus des appareils créés à l’aide des [kits de développement logiciel (SDK) Azure IoT](https://github.com/Azure/azure-iot-sdks), vous pouvez également connecter des [appareils Azure IoT Edge](../../iot-edge/about-iot-edge.md) à une application IoT Central. IoT Edge vous permet d’exécuter l’intelligence Cloud et une logique personnalisée directement sur les appareils IoT gérés par IoT Central. Vous pouvez également utiliser IoT Edge en tant que passerelle pour permettre à d’autres appareils en aval de se connecter à IoT Central.

Pour en savoir plus, reportez-vous à [Connecter des appareils Azure IoT Edge à une application Azure IoT Central](concepts-iot-edge.md).

## <a name="cloud-gateway"></a>Passerelle cloud

Azure IoT Central utilise Azure IoT Hub comme passerelle cloud pour la connectivité des appareils. IoT Hub permet :

- L’ingestion de données à grande échelle dans le cloud.
- La gestion des appareils.
- La connectivité sécurisée des appareils.

Pour plus d’informations sur IoT Hub, consultez [Azure IoT Hub](../../iot-hub/index.yml).

Pour plus d’informations sur la connectivité des appareils dans Azure IoT Central, consultez [Connectivité des appareils](concepts-get-connected.md).

## <a name="data-stores"></a>Magasins de données

Azure IoT Central stocke les données d’application dans le cloud. Les données d’application stockées incluent :

- Les modèles d’appareil.
- Les identités des appareils.
- Les métadonnées des appareils.
- Les données des utilisateurs et des rôles.

Azure IoT Central utilise un magasin de séries chronologiques pour les données de mesure envoyées depuis vos appareils. Données de séries chronologiques provenant d’appareils utilisés par le service d’analytique.

## <a name="data-export"></a>Exportation de données

Dans une application Azure IoT Central, vous pouvez [exporter en continu vos données](howto-export-data.md) vers votre propre Azure Event Hubs et les instances Azure Service Bus. Vous pouvez également régulièrement exporter vos données vers votre compte de stockage d’objets blob Azure. IoT Central peut exporter des mesures, des appareils et des modèles d’appareil.

## <a name="batch-device-updates"></a>Mises à jour d’appareils par lots

Dans une application Azure IoT Central, vous pouvez [créer et exécuter des travaux](howto-manage-devices-in-bulk.md) pour gérer des appareils connectés. Ces travaux vous permettent d’effectuer des mises à jour en bloc sur les propriétés ou les paramètres des appareils, ou d’exécuter des commandes. Par exemple, vous pouvez créer un travail pour augmenter la vitesse du ventilateur pour plusieurs distributeurs réfrigérés.

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle

Chaque application IoT Central possède son propre système RBAC intégré. Un [administrateur peut définir des règles d’accès](howto-manage-users-roles.md) pour une application Azure IoT Central en utilisant un des rôles prédéfinis ou en créant un rôle personnalisé. Les rôles déterminent les zones de l’application auxquelles un utilisateur a accès ainsi que les actions qu’il peut effectuer.

## <a name="security"></a>Sécurité

Les fonctionnalités de sécurité dans Azure IoT Central sont les suivantes :

- Les données sont chiffrées en transit et au repos.
- L’authentification est fournie par Azure Active Directory ou via un compte Microsoft. L’authentification à deux facteurs est prise en charge.
- L’isolation complète des locataires.
- La sécurité au niveau des appareils.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert l’architecture d’Azure IoT Central, l’étape suivante suggérée est de découvrir la [connectivité des appareils](concepts-get-connected.md) dans Azure IoT Central.