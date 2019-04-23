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
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150215"
---
Azure fournit les rôles RBAC intégrés suivants pour autoriser l’accès aux données blob et file d’attente à l’aide d’Azure AD et OAuth :

- [Propriétaire des données de stockage Blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): permet de définir la propriété et de gérer le contrôle d’accès POSIX pour Azure Data Lake Storage Gen2 (préversion). Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contributeur aux données stockage Blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): permet d’accorder des autorisations en lecture/écriture/suppression aux ressources de stockage d’objets blob.
- [Lecteur de données de stockage Blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): permet d’accorder des autorisations en lecture seule aux ressources de stockage d’objets blob.
- [Contributeur de données de file d’attente de stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): permet d’accorder des autorisations en lecture/écriture/suppression aux files d’attente Azure.
- [Lecteur de données de file d’attente de stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): permet d’accorder des autorisations en lecture seule aux files d’attente Azure.
- [Stockage de données de file d’attente de messages processeur](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Utilisez grant peek, récupérer et les autorisations de suppression pour les messages dans les files d’attente de stockage Azure.
- [Expéditeur de Message de données de file d’attente de stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Permet d’accorder ajouter des autorisations à des messages dans les files d’attente de stockage Azure.

> [!NOTE]
> N’oubliez pas que les attributions de rôles RBAC peuvent prendre jusqu'à cinq minutes pour se propager.
