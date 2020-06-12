---
title: Surveiller l’intégrité d’une application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur ou administrateur, surveillez l’intégrité globale des appareils connectés à votre application IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 664819b209aeb09093ce8711456b86ff4d3e8949
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84249474"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>Surveiller l’intégrité globale des appareils connectés à une application IoT Central

*Cet article s’applique aux opérateurs et aux administrateurs.*

Dans cet article, vous allez apprendre à utiliser l’ensemble des métriques fournies par IoT Central pour évaluer l’intégrité globale des appareils connectés à votre application IoT Central.

Les métriques sont activées par défaut pour votre application IoT Central et vous y accédez à partir du [portail Azure](https://portal.azure.com/). La [plateforme de données Azure Monitor expose ces métriques](../../azure-monitor/platform/data-platform-metrics.md) et offre plusieurs moyens d’interagir avec elles. Par exemple, vous pouvez utiliser des graphiques dans le portail Azure, une API REST ou des requêtes dans PowerShell ou Azure CLI.

### <a name="trial-applications"></a>Applications d’évaluation

Les applications qui utilisent le plan d’essai gratuit n’ont pas d’abonnement Azure associé et ne prennent donc pas en charge les métriques d’Azure Monitor. Vous pouvez [convertir une application en plan de tarification standard](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) et accéder à ces métriques.

## <a name="view-metrics-in-the-azure-portal"></a>Afficher les métriques dans le portail Azure

Les étapes suivantes partent du principe que vous disposez d’une [application IoT Central](./quick-deploy-iot-central.md) avec quelques [appareils connectés](./tutorial-connect-device-nodejs.md).

Pour afficher les métriques IoT Central dans le portail :

1. Accédez à votre ressource d’application IoT Central dans le portail. Par défaut, les ressources IoT Central se trouvent dans un groupe de ressources appelé **IOTC**.
1. Pour créer un graphique à partir des métriques de votre application, sélectionnez **Métriques** dans la section **Surveillance**.

### <a name="azure-portal-permissions"></a>Autorisations de Portail Azure

L’accès aux métriques dans le portail Azure est géré par le [contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md). Utilisez le portail Azure pour ajouter des utilisateurs à l’application, au groupe de ressources ou à l’abonnement IoT Central pour leur accorder l’accès. Vous devez ajouter un utilisateur dans le portail, même s’il a déjà été ajouté à l’application IoT Central. Utilisez des [rôles intégrés Azure](../../role-based-access-control/built-in-roles.md) pour un contrôle d’accès plus précis.

## <a name="iot-central-metrics"></a>Métriques IoT Central

Le tableau suivant décrit les métriques actuellement disponibles pour IoT Central :

| Métrique | Nom d’affichage de la métrique | Unité | Type d’agrégation | Description |
|--------|---------------------|------|------------------|-------------|
| connectedDeviceCount         | Nombre total d’appareils connectés                              | Count  | Total             | Nombre d’appareils connectés à IoT Central                               |
| c2d.property.read.success    | Lectures des propriétés d’appareil ayant abouti à partir d’IoT Central    | Count  | Total             | Nombre total de lectures des propriétés lancées à partir d’IoT Central ayant abouti    |
| c2d.property.read.failure    | Lectures des propriétés d’appareil ayant échoué à partir d’IoT Central        | Count  | Total             | Nombre total de lectures des propriétés lancées à partir d’IoT Central ayant échoué        |
| d2c.property.read.success    | Lectures des propriétés d’appareil ayant abouti à partir d’appareils        | Count  | Total             | Nombre total de lectures des propriétés lancées à partir d’appareils ayant abouti        |
| d2c.property.read.failure    | Lectures des propriétés d’appareil ayant échoué à partir d’appareils            | Count  | Total             | Nombre total de lectures des propriétés lancées à partir d’appareils ayant échoué            |
| c2d.property.update.success  | Mises à jour des propriétés d’appareil ayant abouti à partir d’IoT Central  | Count  | Total             | Nombre total de mises à jour des propriétés lancées à partir d’IoT Central ayant abouti  |
| c2d.property.update.failure  | Mises à jour des propriétés d’appareil ayant échoué à partir d’IoT Central      | Count  | Total             | Nombre total de mises à jour des propriétés lancées à partir d’IoT Central ayant échoué      |
| d2c.property.update.success  | Mises à jour des propriétés d’appareil ayant abouti à partir d’appareils      | Count  | Total             | Nombre total de mises à jour des propriétés lancées à partir d’appareils ayant abouti      |
| d2c.property.update.failure  | Mises à jour des propriétés d’appareil ayant échoué à partir d’appareils          | Count  | Total             | Nombre total de mises à jour des propriétés lancées à partir d’appareils ayant échoué          |

### <a name="metrics-and-invoices"></a>Métriques et factures

Les métriques peuvent être différer des valeurs figurant sur votre facture Azure IoT Central. Cette situation se produit pour plusieurs raisons :

- Les [plans de tarification standard](https://azure.microsoft.com/pricing/details/iot-central/) d’IoT Central incluent deux appareils et des quotas de message variables gratuitement. Si les éléments gratuits sont exclus de la facturation, ils sont toujours comptabilisés dans les métriques.

- IoT Central génère automatiquement une identité d’appareil de test pour chaque modèle d’appareil dans l’application. Cette identité d’appareil est visible sur la page **Gérer l’appareil de test** pour un modèle d’appareil. Les créateurs de solutions peuvent choisir de [valider leurs modèles d’appareils](./overview-iot-central.md#create-device-templates) avant de les publier en générant du code qui utilise ces identités d’appareil de test. Si ces appareils sont exclus de la facturation, ils sont toujours comptabilisés dans les métriques.

- Bien que les métriques puissent afficher un sous-ensemble de communications appareil-à-cloud, toutes les communications entre l’appareil et le cloud [comptent comme un message pour la facturation](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser des modèles d’application, l’étape suivante conseillée est d’apprendre à [Gérer des applications IoT Central à partir du portail Azure](howto-manage-iot-central-from-portal.md)
