---
title: Limites de service de la préversion publique d’Azure Digital Twins | Microsoft Docs
description: Présentation des limites de service de la préversion publique d’Azure Digital Twins
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dwalthermsft
ms.openlocfilehash: f9a3d934de47630ac3fd2356001014d006c2a4eb
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212265"
---
# <a name="public-preview-service-limits"></a>Limites de service de la préversion publique

Dans la **préversion publique**, Azure Digital Twins présente des limites temporaires au niveau des abonnements, des instances et des taux ; celles-ci sont décrites ci-dessous.

Ces limites ont pour but de simplifier l’apprentissage du nouveau service et de ses nombreuses fonctionnalités.

> [!NOTE]
> Ces limites sont réduites ou supprimées dans la **version en disponibilité générale** (**GA**).

## <a name="per-subscription-limits"></a>Limites par abonnement

Dans la **préversion publique**, chaque abonnement Azure ne peut créer ou comprendre qu’une seule instance d’Azure Digital Twins.

> [!TIP]
> Pour créer une nouvelle instance, vous devez supprimer l’instance actuelle.

## <a name="per-instance-limits"></a>Limites par instance

Chaque instance d’Azure Digital Twins peut comprendre :

- Une ressource **IotHub**
- Un point de terminaison **EventHub** pour le type d’événement **DeviceMessage**
- Jusqu’à trois points de terminaison **EventHub**, **ServiceBus** ou **EventGrid** du type d’événement **SensorChange**, **SpaceChange** , **TopologyOperation** ou **UdfCustom**

## <a name="management-api-limits"></a>Limites de l’API de gestion

Les limites de taux de requêtes pour votre API de gestion sont les suivantes :

- 100 requêtes par seconde à l’API de gestion
- Une requête d’API de gestion peut retourner jusqu’à 1 000 objets

> [!IMPORTANT]
> Si vous dépassez la limite de 1 000 objets, vous recevrez un message d’erreur et devrez simplifier votre requête.

## <a name="udf-rate-limits"></a>Limites du taux de fonctions définies par l’utilisateur

Les limites suivantes définissent le nombre total d’appels de fonction définie par l’utilisateur qui peuvent être effectués vers votre instance d’Azure Digital Twins :

- 400 appels de bibliothèque cliente par seconde
- 100 appels **SendNotification** par seconde

> [!NOTE]
> D’autres limites temporaires peuvent être appliquées aux actions suivantes :
> - Modification des métadonnées d’objets de topologie
> - Mise à jour des définitions de fonctions définies par l’utilisateur
> - Premier envoi de données de télémétrie par un appareil

## <a name="device-telemetry-limits"></a>Limites de télémétrie des appareils

Les limites ci-dessous restreignent le nombre total de messages que vos appareils peuvent envoyer à votre instance d’Azure Digital Twins :

- 100 messages par seconde

## <a name="next-steps"></a>Étapes suivantes

Pour tester un exemple Azure Digital Twins, consultez [Démarrage rapide : Rechercher des salles disponibles](./quickstart-view-occupancy-dotnet.md).