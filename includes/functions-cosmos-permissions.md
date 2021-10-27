---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: d95d50d6e5f69922856e6a3045dfccdf91ddbf1a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994318"
---
Vous devrez créer une attribution de rôle qui donne accès à votre compte de base de données au moment de l’exécution. Les rôles de gestion comme [Propriétaire](../articles/role-based-access-control/built-in-roles.md#owner) ne sont pas suffisants. Le tableau suivant présente les rôles intégrés qui sont recommandés lors de l’utilisation de l’extension Cosmos DB dans le cadre d’un fonctionnement normal. Votre application peut nécessiter des autorisations supplémentaires en fonction du code que vous écrivez.

| Type de liaison   | Exemples de rôles intégrés                |
|----------------|---------------------------------------|
| Déclencheur        | [Lecteur de données intégré Cosmos DB]      |
| Liaison de sortie | [Contributeur de données intégré Cosmos DB] |


[Lecteur de données intégré Cosmos DB]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions
[Contributeur de données intégré Cosmos DB]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions