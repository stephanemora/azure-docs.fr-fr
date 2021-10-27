---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b0451d7a3f14002ec53cdb410144c6e69f1337a9
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137359"
---
Vous devrez créer une attribution de rôle qui donne accès à votre Event Hub au moment de l’exécution. L’étendue de l’attribution de rôle doit être pour un espace de noms Event Hubs, et non pour le Event Hub lui-même. Les rôles de gestion comme [Propriétaire](../articles/role-based-access-control/built-in-roles.md#owner) ne sont pas suffisants. Le tableau suivant présente les rôles intégrés qui sont recommandés lors de l’utilisation de l’extension Event Hubs dans le cadre d’un fonctionnement normal. Votre application peut nécessiter des autorisations supplémentaires en fonction du code que vous écrivez.

| Type de liaison   | Exemples de rôles intégrés                                          |
|----------------|-----------------------------------------------------------------|
| Déclencheur        | [Récepteur de données Azure Event Hubs], [Propriétaire de données Azure Event Hubs] |
| Liaison de sortie | [Expéditeur de données Azure Event Hubs]                                  |

[Récepteur de données Azure Event Hubs]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver
[Expéditeur de données Azure Event Hubs]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender
[Propriétaire de données Azure Event Hubs]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner