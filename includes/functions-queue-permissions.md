---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: e9897a9fb28f3ea9489e80b0dd4bb814c836b23b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992726"
---
Vous devrez créer une attribution de rôle qui donne accès à votre file d’attente au moment de l’exécution. Les rôles de gestion comme [Propriétaire](../articles/role-based-access-control/built-in-roles.md#owner) ne sont pas suffisants. Le tableau suivant présente les rôles intégrés qui sont recommandés lors de l’utilisation de l’extension Stockage File d’attente dans le cadre d’un fonctionnement normal. Votre application peut nécessiter des autorisations supplémentaires en fonction du code que vous écrivez.

| Type de liaison   | Exemples de rôles intégrés                                                |
|----------------|-----------------------------------------------------------------------|
| Déclencheur        | [Lecteur de données en file d’attente du stockage], [Processeur de messages de données en file d’attente du stockage]   |
| Liaison de sortie | [Contributeur aux données en file d’attente du stockage], [Expéditeur de messages de données en file d’attente du stockage] |

[Lecteur des données en file d’attente du stockage]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader
[Processeur de messages de données en file d’attente du stockage]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor
[Expéditeur de messages de données en file d’attente du stockage]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender
[Contributeur aux données en file d’attente du stockage]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor
