---
title: Génération de solutions pour Azure IoT Central | Microsoft Docs
description: Azure IoT Central est une plateforme d’applications IoT qui simplifie la création de solutions IoT. Cet article offre une vue d’ensemble de la génération de solutions intégrées avec IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100416591"
---
# <a name="iot-central-solution-builder-guide"></a>Guide du générateur de solutions IoT Central

*Cet article s’applique aux créateurs de solutions.*

Une application IoT Central vous permet de surveiller et de gérer des millions d’appareils tout au long de leur cycle de vie. Ce guide s’adresse aux générateurs de solutions qui utilisent IoT Central pour créer des solutions intégrées. Une application IoT Central vous permet de gérer des appareils, d’analyser les données de télémétrie des appareils, et de les intégrer à d’autres services principaux.

Un générateur de solutions :

- Configure des tableaux de bord et des vues dans l’interface utilisateur Web d’IoT Central.
- Utilise les règles intégrées et les outils d’analyse pour extraire des insights métier à partir des appareils connectés.
- Utilise les fonctionnalités d’exportation et de règles des données pour intégrer des IoT Central avec d’autres services principaux.

## <a name="configure-dashboards-and-views"></a>Configurer des tableaux de bord et des vues

Une application IoT Central peut avoir un ou plusieurs tableaux de bord que les opérateurs utilisent pour afficher et interagir avec l’application. En tant que générateur de solutions, vous pouvez personnaliser le tableau de bord par défaut et créer des tableaux de bord spécialisés :

- Pour consulter des exemples de tableaux de bord personnalisés, consultez [Modèles sectoriels](concepts-app-templates.md#industry-focused-templates).
- Pour en savoir plus sur les tableaux de bord, consultez [Créer et gérer plusieurs tableaux de bord](howto-create-personal-dashboards.md) et [Configurer le tableau de bord de l’application](howto-add-tiles-to-your-dashboard.md).

Quand un appareil se connecte à IoT Central, il est associé à un modèle d’appareil pour le type d’appareil. Un modèle d’appareil comporte des vues personnalisables qu’un opérateur utilise pour gérer des appareils individuels. En tant que développeur de solutions, vous pouvez créer et personnaliser les vues disponibles pour un type d’appareil. Pour plus d’informations, consultez [Ajouter des vues](howto-set-up-template.md#add-views).

## <a name="use-built-in-rules-and-analytics"></a>Utiliser des règles et une analytique intégrées

Un développeur de solutions peut ajouter des règles à une application IoT Central qui exécute des actions personnalisables. Les règles évaluent les conditions, en fonction des données provenant d’un appareil, pour déterminer quand exécuter une action. Pour en savoir plus sur les règles, consultez :

- [Tutoriel : Créer une règle et configurer des notifications dans votre application Azure IoT Central](tutorial-create-telemetry-rules.md)
- [Créer des actions de webhook sur des règles dans Azure IoT Central](howto-create-webhooks.md)
- [Regrouper plusieurs actions à exécuter à partir d’une ou plusieurs règles](howto-use-action-groups.md)

IoT Central dispose de fonctionnalités d’analyse intégrées qu’un opérateur peut utiliser pour analyser les données provenant des appareils connectés. Pour en savoir plus, consultez [Comment utiliser l’analytique pour analyser des données d’appareils](howto-create-analytics.md).

## <a name="integrate-with-other-services"></a>Intégrer aux autres services

En tant que générateur de solutions, vous pouvez utiliser les fonctionnalités d’exportation et de règles des données dans IoT Central pour une intégration avec d’autres services. Pour en savoir plus, consultez :

- [Exporter des données IoT vers des destinations cloud à l'aide des fonctionnalités d'exportation de données](howto-export-data.md)
- [Utiliser des workflows pour intégrer votre application Azure IoT Central à d’autres services cloud](howto-configure-rules-advanced.md)
- [Étendre Azure IoT Central avec des règles personnalisées à l’aide de Stream Analytics, d’Azure Functions et de SendGrid](howto-create-custom-rules.md)
- [Étendre Azure IoT Central avec des analyses personnalisées à l’aide d’Azure Databricks](howto-create-custom-analytics.md)
- [Visualiser et analyser des données Azure IoT Central dans un tableau de bord Power BI](howto-connect-powerbi.md)

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur l’utilisation d’IoT Central, nous vous suggérons de tester les démarrages rapides, en commençant par [Créer une application Azure IoT Central](./quick-deploy-iot-central.md).
