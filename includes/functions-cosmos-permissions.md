---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2021
ms.author: mahender
ms.openlocfilehash: 15f68c6339135139f6f6fcbf335306f6edf2a3c6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257122"
---
Vous devrez créer une attribution de rôle qui donne accès à votre compte de base de données au moment de l’exécution. Les rôles de gestion comme [Propriétaire](../articles/role-based-access-control/built-in-roles.md#owner) ne sont pas suffisants. Le tableau suivant présente les rôles intégrés qui sont recommandés lors de l’utilisation de l’extension Cosmos DB dans le cadre d’un fonctionnement normal. Votre application peut nécessiter des autorisations supplémentaires en fonction du code que vous écrivez.

| Type de liaison   | Exemples de rôles intégrés                |
|----------------|---------------------------------------|
| Déclencheur        | [Contributeur de données intégré Cosmos DB] |
| Liaison d’entrée  | [Lecteur de données intégré Cosmos DB]      |
| Liaison de sortie | [Contributeur de données intégré Cosmos DB] |


[Lecteur de données intégré Cosmos DB]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions
[Contributeur de données intégré Cosmos DB]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions