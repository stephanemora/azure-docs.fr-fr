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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176737"
---
Azure fournit les rôles RBAC intégrés suivants pour autoriser l’accès au blob et aux données de file d’attente à l’aide d’Azure AD et d’OAuth :

- [Propriétaire des données Blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) : permet de définir la propriété et de gérer le contrôle d’accès POSIX pour Azure Data Lake Storage Gen2 (préversion). Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contributeur aux données Blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor) : permet d’accorder des autorisations en lecture/écriture/suppression aux ressources de stockage d’objets blob.
- [Lecteur des données blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader) : permet d’accorder des autorisations en lecture seule aux ressources de stockage d’objets blob.
- [Contributeur aux données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor) : permet d’accorder des autorisations en lecture/écriture/suppression aux files d’attente Azure.
- [Lecteur des données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader) : permet d’accorder des autorisations en lecture seule aux files d’attente Azure.
- [Processeur de messages de données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor) : permet d’accorder des autorisations d’aperçu, de récupération et de suppression aux messages dans les files d’attente Stockage Azure.
- [Expéditeur de messages de données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender) : permet d’accorder des autorisations d’ajout aux messages dans les files d’attente Stockage Azure.

> [!NOTE]
> Gardez à l’esprit que les attributions de rôles RBAC peuvent prendre jusqu’à cinq minutes pour se propager.
