---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: e8b274cd3d9398694661b06a5a1c9e13e42c6c89
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137358"
---
Vous devrez créer une attribution de rôle qui fournit l’accès au compte de stockage pour « AzureWebJobsStorage » au moment de l’exécution. Les rôles de gestion comme [Propriétaire](../articles/role-based-access-control/built-in-roles.md#owner) ne sont pas suffisants. Le rôle [Propriétaire des données Blob du stockage] couvre les besoins de base du stockage d’hôtes de fonctions - L’accès en lecture et en écriture aux objets blob et la capacité à créer des conteneurs sont nécessaires pour l’exécution. Dans certains cas, comme l’utilisation du déclencheur d’objets blob, le rôle [Contributeur aux données en file d’attente du stockage] peut également être nécessaire. Vous pouvez avoir besoin d’autorisations supplémentaires si vous utilisez « AzureWebJobsStorage » à d’autres fins.

[Propriétaire des données Blob du stockage]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
[Contributeur aux données en file d’attente du stockage]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor