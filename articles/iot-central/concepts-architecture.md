---
title: Concepts d’architecture dans Azure IoT Central | Microsoft Docs
description: Cet article présente les concepts clés relatifs à l’architecture d’Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 4f4b917808f4973dc83294391f58d7e0e2d01c4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60887401"
---
# <a name="azure-iot-central-architecture"></a>Architecture d’Azure IoT Central

Cet article donne une vue d’ensemble de l’architecture d’Azure IoT Central.

![Architecture globale](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Appareils

Les appareils échangent des données avec votre application Azure IoT Central. Un appareil peut :

- Envoyer des mesures, comme de la télémétrie.
- Synchroniser des paramètres avec votre application.

Dans Azure IoT Central, les données qu’un appareil peut échanger avec votre application sont spécifiées dans un modèle d’appareil. Pour plus d’informations sur les modèles d’appareil, consultez [Gestion des métadonnées](#metadata-management).

Pour plus d’informations sur la façon dont les appareils se connectent à votre application Azure IoT Central, consultez [Connectivité des appareils](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Passerelle cloud

Azure IoT Central utilise Azure IoT Hub comme passerelle cloud pour la connectivité des appareils. IoT Hub permet :

- L’ingestion de données à grande échelle dans le cloud.
- La gestion des appareils.
- La connectivité sécurisée des appareils.

Pour plus d’informations sur IoT Hub, consultez [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Pour plus d’informations sur la connectivité des appareils dans Azure IoT Central, consultez [Connectivité des appareils](concepts-connectivity.md).

## <a name="data-stores"></a>Magasins de données

Azure IoT Central stocke les données d’application dans le cloud. Les données d’application stockées incluent :

- Les modèles d’appareil.
- Les identités des appareils.
- Les métadonnées des appareils.
- Les données des utilisateurs et des rôles.

Azure IoT Central utilise un magasin de séries chronologiques pour les données de mesure envoyées depuis vos appareils. Données de séries chronologiques provenant d’appareils utilisés par le service d’analytique.

## <a name="analytics"></a>Analytics

Le service d’analytique est chargé de générer les données des rapports personnalisés montrés par l’application. Un opérateur peut [personnaliser l’analytique](howto-create-analytics.md) affichée dans l’application. Le service d’analytique utilise la technologie [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) et traite les données de mesure envoyées depuis vos appareils.

## <a name="rules-and-actions"></a>Règles et actions

[Les règles et les actions](howto-create-telemetry-rules.md) travaillent en étroite collaboration pour automatiser les tâches au sein de l’application. Un concepteur peut définir des règles en fonction de la télémétrie des appareils, par exemple une température dépassant un seuil défini. Azure IoT Central utilise un processeur de flux de données pour déterminer quand les conditions d’une règle sont remplies. Quand une condition de règle est remplie, il déclenche une action définie par le concepteur. Par exemple, une action peut envoyer un e-mail pour notifier à un ingénieur que la température d’un appareil est trop élevée.

## <a name="metadata-management"></a>Gestion des métadonnées

Dans une application Azure IoT Central, les modèles d’appareil définissent le comportement et les fonctionnalités des types d’appareils. Par exemple, un modèle de réfrigérateur spécifie la télémétrie qu’un réfrigérateur envoie à votre application.

![Architecture des modèles](media/concepts-architecture/template_architecture.png)

Dans un modèle d’appareil :

- Les **mesures** spécifient la télémétrie que l’appareil envoie à l’application.
- Les **paramètres** spécifient les configurations qu’un opérateur peut définir.
- Les **propriétés** spécifient les métadonnées qu’un opérateur peut définir.
- Les **règles** automatisent les comportements dans l’application en fonction des données envoyées depuis un appareil.
- Les **tableaux de bord** sont des vues personnalisables d’un appareil dans l’application.

Une application peut avoir un ou plusieurs appareils simulés et réels basés sur chaque modèle d’appareil.

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle

Un [administrateur peut définir des règles d’accès](howto-administer.md) pour une application Azure IoT Central en utilisant les rôles prédéfinis. Un administrateur peut affecter des utilisateurs à des rôles qui déterminent les zones de l’application auxquelles l’utilisateur a accès.

## <a name="security"></a>Sécurité

Les fonctionnalités de sécurité dans Azure IoT Central sont les suivantes :

- Les données sont chiffrées en transit et au repos.
- L’authentification est fournie par Azure Active Directory ou via un compte Microsoft. L’authentification à deux facteurs est prise en charge.
- L’isolation complète des locataires.
- La sécurité au niveau des appareils.

## <a name="ui-shell"></a>Le shell d’interface utilisateur

Le shell d’interface utilisateur est une application moderne et réactive, basée sur un navigateur HTML 5.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert l’architecture d’Azure IoT Central, l’étape suivante suggérée est de découvrir la [connectivité des appareils](concepts-connectivity.md) dans Azure IoT Central.