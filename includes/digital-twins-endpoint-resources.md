---
author: baanders
description: Inclure le fichier des ressources nécessaires à la création de points de terminaison Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "99054503"
---
### <a name="prerequisite-create-endpoint-resources"></a>Condition préalable : Créer des ressources de point de terminaison

Pour lier un point de terminaison à Azure Digital Twins, la rubrique Event Hub, Event Grid ou Service Bus que vous utilisez pour le point de terminaison doit pré-exister.

Utilisez le graphique suivant pour identifier les ressources qui doivent être configurées avant de créer votre point de terminaison.

| Type de point de terminaison | Ressources requises (liées aux instructions de création) |
| --- | --- |
| Point de terminaison Event Grid | [Rubrique Event Grid](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Point de terminaison Event Hubs | [Espace de noms&nbsp;Event&nbsp;Hubs](../articles/event-hubs/event-hubs-create.md)<br/><br/>[hub d’événements](../articles/event-hubs/event-hubs-create.md)<br/><br/>(Facultatif) [Règle d’autorisation](../articles/event-hubs/authorize-access-shared-access-signature.md) pour l’authentification basée sur une clé | 
| Point de terminaison de Service Bus | [Espace de noms Service Bus](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Rubrique Service Bus](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (Facultatif) [Règle d’autorisation](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) pour l’authentification basée sur une clé|
