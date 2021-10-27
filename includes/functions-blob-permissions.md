---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 32184e2b836aa881dc3175bb0790fc9cbbca1df7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996427"
---
Vous devrez créer une attribution de rôle qui donne accès à votre conteneur de blobs au moment de l’exécution. Les rôles de gestion comme [Propriétaire](../articles/role-based-access-control/built-in-roles.md#owner) ne sont pas suffisants. Le tableau suivant présente les rôles intégrés qui sont recommandés lors de l’utilisation de l’extension Stockage Blob dans le cadre d’un fonctionnement normal. Votre application peut nécessiter des autorisations supplémentaires en fonction du code que vous écrivez.

| Type de liaison   | Exemples de rôles intégrés                |
|----------------|---------------------------------------|
| Déclencheur        | [Propriétaire des données Blob du stockage]<sup>1</sup> |
| Liaison d’entrée  | [Lecteur des données blob du stockage]            |
| Liaison de sortie | [Propriétaire des données Blob du stockage]             |

<sup>1</sup> Dans certaines configurations, un déclencheur blob peut également nécessiter le rôle [Contributeur aux données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor).

[Lecteur des données blob du stockage]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader
[Propriétaire des données Blob du stockage]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
