---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b4ce7e128304aa1befd7029ed2a692b1d7f391f3
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137360"
---
Vous devrez créer une attribution de rôle qui donne accès à vos rubriques et files d’attente au moment de l’exécution. L’étendue de l’attribution de rôle doit être pour un espace de noms Service Bus, et pas limitée à une file d’attente ou rubrique spécifique. Les rôles de gestion comme [Propriétaire](../articles/role-based-access-control/built-in-roles.md#owner) ne sont pas suffisants. Le tableau suivant présente les rôles intégrés qui sont recommandés lors de l’utilisation de l’extension Service Bus dans le cadre d’un fonctionnement normal. Votre application peut nécessiter des autorisations supplémentaires en fonction du code que vous écrivez.

| Type de liaison   | Exemples de rôles intégrés                                            |
|----------------|-------------------------------------------------------------------|
| Déclencheur        | [Récepteur de données Azure Service Bus], [Propriétaire de données Azure Service Bus] |
| Liaison de sortie | [Expéditeur de données Azure Service Bus]                                   |

[Récepteur de données Azure Service Bus]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver
[Expéditeur de données Azure Service Bus]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-sender
[Propriétaire de données Azure Service Bus]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-owner
