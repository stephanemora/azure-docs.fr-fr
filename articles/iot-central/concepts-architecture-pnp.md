---
title: Concepts d’architecture dans Azure IoT Central | Microsoft Docs
description: Cet article présente les concepts clés relatifs à l’architecture d’Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: abc09ac1a13537c31fe96fae14edefd0d06b6aef
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879137"
---
# <a name="azure-iot-central-architecture-preview-features"></a>Architecture d’Azure IoT Central (fonctionnalités en préversion)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Cet article donne une vue d’ensemble de l’architecture d’Azure IoT Central.

![Architecture globale](media/concepts-architecture-pnp/architecture.png)

## <a name="devices"></a>Appareils

Les appareils échangent des données avec votre application Azure IoT Central. Un appareil peut :

- Envoyer des mesures, comme de la télémétrie.
- Synchroniser des paramètres avec votre application.

Dans Azure IoT Central, les données qu’un appareil peut échanger avec votre application sont spécifiées dans un modèle d’appareil. Pour plus d’informations sur les modèles d’appareil, consultez [Gestion des métadonnées](#metadata-management).

Pour plus d’informations sur la façon dont les appareils se connectent à votre application Azure IoT Central, consultez [Connectivité des appareils](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="cloud-gateway"></a>Passerelle cloud

Azure IoT Central utilise Azure IoT Hub comme passerelle cloud pour la connectivité des appareils. IoT Hub permet :

- L’ingestion de données à grande échelle dans le cloud.
- La gestion des appareils.
- La connectivité sécurisée des appareils.

Pour plus d’informations sur IoT Hub, consultez [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Pour plus d’informations sur la connectivité des appareils dans Azure IoT Central, consultez [Connectivité des appareils](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="data-stores"></a>Magasins de données

Azure IoT Central stocke les données d’application dans le cloud. Les données d’application stockées incluent :

- Les modèles d’appareil.
- Les identités des appareils.
- Les métadonnées des appareils.
- Les données des utilisateurs et des rôles.

Azure IoT Central utilise un magasin de séries chronologiques pour les données de mesure envoyées depuis vos appareils. Données de séries chronologiques provenant d’appareils utilisés par le service d’analytique.

## <a name="analytics"></a>Analytics

Le service d’analytique est chargé de générer les données des rapports personnalisés montrés par l’application. Un opérateur peut [personnaliser l’analytique](howto-create-analytics.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) affichée dans l’application. Le service d’analytique utilise la technologie [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) et traite les données de mesure envoyées depuis vos appareils.

## <a name="rules-and-actions"></a>Règles et actions

[Les règles et les actions](howto-create-telemetry-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) travaillent en étroite collaboration pour automatiser les tâches au sein de l’application. Un concepteur peut définir des règles en fonction de la télémétrie des appareils, par exemple une température dépassant un seuil défini. Azure IoT Central utilise un processeur de flux de données pour déterminer quand les conditions d’une règle sont remplies. Quand une condition de règle est remplie, il déclenche une action définie par le concepteur. Par exemple, une action peut envoyer un e-mail pour notifier à un ingénieur que la température d’un appareil est trop élevée.

## <a name="metadata-management"></a>Gestion des métadonnées

Dans une application Azure IoT Central, les modèles d’appareil définissent le comportement et les fonctionnalités des types d’appareils. Par exemple, un modèle de réfrigérateur spécifie la télémétrie qu’un réfrigérateur envoie à votre application.

![Architecture des modèles](media/concepts-architecture-pnp/template-architecture.png)

Dans un modèle d’appareil d'application en préversion IoT Central :

- Les **modèles de capacité d’appareil** spécifient les capacités d'un appareil, notamment les données de télémétrie qu'il envoie, les propriétés qui définissent l'état de l’appareil, et les commandes auxquelles l’appareil répond. Les capacités de l'appareil sont organisées en une ou plusieurs interfaces. Pour plus d'informations sur les modèles de capacité d’appareil, consultez la documentation [IoT Plug-and-Play](https://aka.ms/iot-pnp-docs).
- Les **propriétés du cloud** spécifient les propriétés que l'IoT Central stocke pour un appareil. Ces propriétés sont uniquement stockées dans IoT Central et ne sont jamais envoyées à un appareil.
- Les **vues** spécifient les tableaux de bord et les formulaires créés par le concepteur pour permettre à l'opérateur de surveiller et de gérer les appareils.
- Les **personnalisations** permettent au concepteur de remplacer certaines des définitions du modèle de capacité d’appareil afin de les rendre plus pertinentes pour l'application IoT Central.

Une application peut avoir un ou plusieurs appareils simulés et réels basés sur chaque modèle d’appareil.

## <a name="data-export"></a>Exportation de données

Dans une application Azure IoT Central, vous pouvez [exporter en continu vos données](howto-export-data-event-hubs-service-bus-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) vers votre propre Azure Event Hubs et vers les instances Azure Service Bus. Vous pouvez également régulièrement exporter vos données vers votre compte de stockage d’objets blob Azure. IoT Central peut exporter des mesures, des appareils et des modèles d’appareil.

## <a name="batch-device-updates"></a>Mises à jour d’appareils par lots

Dans une application Azure IoT Central, vous pouvez [créer et exécuter des travaux](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) pour gérer des appareils connectés. Ces travaux vous permettent d’effectuer des mises à jour en bloc sur les propriétés ou les paramètres des appareils, ou d’exécuter des commandes. Par exemple, vous pouvez créer un travail pour augmenter la vitesse du ventilateur pour plusieurs distributeurs réfrigérés.

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle

Un [administrateur peut définir des règles d’accès](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) pour une application Azure IoT Central en utilisant les rôles prédéfinis. Un administrateur peut affecter des utilisateurs à des rôles qui déterminent les zones de l’application auxquelles l’utilisateur a accès.

## <a name="security"></a>Sécurité

Les fonctionnalités de sécurité dans Azure IoT Central sont les suivantes :

- Les données sont chiffrées en transit et au repos.
- L’authentification est fournie par Azure Active Directory ou via un compte Microsoft. L’authentification à deux facteurs est prise en charge.
- L’isolation complète des locataires.
- La sécurité au niveau des appareils.

## <a name="ui-shell"></a>Le shell d’interface utilisateur

Le shell d’interface utilisateur est une application moderne et réactive, basée sur un navigateur HTML 5.
Un administrateur peut personnaliser l’interface utilisateur de l’application en appliquant des thèmes personnalisés et en modifiant les liens d’aide pour pointer vers vos propres ressources d’aide personnalisée. Pour en savoir plus sur la personnalisation de l’interface utilisateur, consultez l’article [Personnaliser l’interface utilisateur d’Azure IoT Central](howto-customize-ui.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Un opérateur peut créer des tableaux de bord des applications personnalisées. Vous pouvez disposer de plusieurs tableaux de bord, qui affichent différentes informations, et basculer de l’un à l’autre.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert l’architecture d’Azure IoT Central, l’étape suivante suggérée est de découvrir la [connectivité des appareils](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) dans Azure IoT Central.