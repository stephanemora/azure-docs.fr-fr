---
title: Limites de service de la préversion publique d’Azure Digital Twins | Microsoft Docs
description: Présentation des limites de service de la préversion publique d’Azure Digital Twins
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: dwalthermsft
ms.openlocfilehash: aa5f6053bf1c98d2b84c02617da30f5d856ed3fc
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323722"
---
# <a name="public-preview-service-limits"></a>Limites de service de la préversion publique

Dans la préversion publique, Azure Digital Twins présente des limites temporaires au niveau des abonnements, des instances et des taux, qui sont décrites ci-dessous.

Ces limites ont pour but de simplifier l’apprentissage du nouveau service et de ses nombreuses fonctionnalités.

> [!NOTE]
> Ces limites seront réduites ou supprimées dans la version en disponibilité générale.

## <a name="per-subscription-limits"></a>Limites par abonnement

Dans la préversion publique, chaque abonnement Azure ne peut créer ou comprendre qu’une seule instance d’Azure Digital Twins.

> [!TIP]
> Pour créer une nouvelle instance, vous devez supprimer l’instance actuelle.

## <a name="per-instance-limits"></a>Limites par instance

Chaque instance d’Azure Digital Twins peut comprendre :

- Une ressource `IoTHub`
- Un point de terminaison `EventHub` pour le type d’événement DeviceMessage
- Jusqu’à trois points de terminaison `EventHub`, `ServiceBus` ou `EventGrid` de type d’événement `SensorChange`, `SpaceChange`, `TopologyOperation` ou `UdfCustom`

## <a name="management-api-limits"></a>Limites de l’API de gestion

Les limites de taux de requêtes pour votre API de gestion sont les suivantes :

- 100 requêtes par seconde à l’API de gestion
- Une requête d’API de gestion peut retourner jusqu’à 1 000 objets

> [!IMPORTANT]
> Si vous dépassez la limite de 1 000 objets, vous recevrez un message d’erreur et devrez simplifier votre requête.

## <a name="udf-rate-limits"></a>Limites du taux de fonctions définies par l’utilisateur

Les limites suivantes définissent le nombre total d’appels de fonction définie par l’utilisateur qui peuvent être effectués vers votre instance d’Azure Digital Twins :

- 400 appels de bibliothèque cliente par seconde
- 100 appels SendNotification par seconde

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