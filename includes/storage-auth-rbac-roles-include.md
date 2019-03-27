---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4c66f35537cff87eb53a6cfe9bc0389f038f1a10
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449755"
---
Azure fournit les rôles RBAC intégrés suivants pour l’accès aux données de stockage :

- [Propriétaire des données de stockage Blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): permet de définir la propriété et de gérer le contrôle d’accès POSIX pour Azure Data Lake Storage Gen2 (préversion). Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contributeur aux données stockage Blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): permet d’accorder des autorisations en lecture/écriture/suppression aux ressources de stockage d’objets blob.
- [Lecteur de données de stockage Blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): permet d’accorder des autorisations en lecture seule aux ressources de stockage d’objets blob.
- [Contributeur de données de file d’attente de stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): permet d’accorder des autorisations en lecture/écriture/suppression aux files d’attente Azure.
- [Lecteur de données de file d’attente de stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): permet d’accorder des autorisations en lecture seule aux files d’attente Azure.
- [Stockage de données de file d’attente de messages processeur](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor-preview): Utilisez grant peek, récupérer et les autorisations de suppression pour les messages dans les files d’attente de stockage Azure.
- [Expéditeur de Message de données de file d’attente de stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender-preview): Permet d’accorder ajouter des autorisations à des messages dans les files d’attente de stockage Azure.

> [!IMPORTANT]
> Attributions de rôles RBAC peuvent prendre jusqu'à cinq minutes pour se propager.

Pour plus d’informations sur la définition des rôles intégrés pour le Stockage Azure, consultez [Comprendre les définitions de rôles](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Pour plus d’informations sur la création des rôles RBAC personnalisés, consultez [créer des rôles personnalisés pour le contrôle d’accès](../articles/role-based-access-control/custom-roles.md). 
