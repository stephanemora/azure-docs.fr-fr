---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218088"
---
Azure fournit les rôles RBAC intégrés suivants pour l’accès aux données de stockage :

- [Propriétaire des données Blob du stockage (préversion)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview) : permet de définir la propriété et de gérer le contrôle d’accès POSIX pour Azure Data Lake Storage Gen2 (préversion). Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contributeur aux données Blob du stockage (préversion)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview) : permet d’accorder des autorisations en lecture/écriture/suppression aux ressources de stockage d’objets blob.
- [Lecteur des données Blob du stockage (préversion)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview) : permet d’accorder des autorisations en lecture seule aux ressources de stockage d’objets blob.
- [Contributeur aux données en file d’attente du stockage (préversion)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview) : permet d’accorder des autorisations en lecture/écriture/suppression aux files d’attente Azure.
- [Lecteur des données en file d’attente du stockage (préversion)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview) : permet d’accorder des autorisations en lecture seule aux files d’attente Azure.

Pour plus d’informations sur la définition des rôles intégrés pour le Stockage Azure, consultez [Comprendre les définitions de rôles](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Pour plus d’informations sur la création des rôles RBAC personnalisés, consultez [créer des rôles personnalisés pour le contrôle d’accès](../articles/role-based-access-control/custom-roles.md). 
