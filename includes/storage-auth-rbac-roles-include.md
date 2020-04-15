---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519575"
---
Azure fournit les rôles RBAC intégrés suivants pour autoriser l’accès au blob et aux données de file d’attente à l’aide d’Azure AD et d’OAuth :

- [Propriétaire des données Blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) : permet de définir la propriété et de gérer le contrôle d’accès POSIX pour Azure Data Lake Storage Gen2. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contributeur aux données Blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor) : permet d’accorder des autorisations en lecture/écriture/suppression aux ressources de stockage d’objets blob.
- [Lecteur des données blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader) : permet d’accorder des autorisations en lecture seule aux ressources de stockage d’objets blob.
- [Contributeur aux données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor) : permet d’accorder des autorisations en lecture/écriture/suppression aux files d’attente Azure.
- [Lecteur des données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader) : permet d’accorder des autorisations en lecture seule aux files d’attente Azure.
- [Processeur de messages de données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor) : permet d’accorder des autorisations d’aperçu, de récupération et de suppression aux messages dans les files d’attente Stockage Azure.
- [Expéditeur de messages de données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender) : permet d’accorder des autorisations d’ajout aux messages dans les files d’attente Stockage Azure.

Pour obtenir des informations détaillées sur les rôles RBAC intégrés pour Stockage Azure pour les services de données et de gestion, consultez la section **Stockage** dans [Rôles intégrés Azure pour Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). En outre, pour plus d’informations sur les différents types de rôles qui fournissent des autorisations dans Azure, consultez [Rôles d’administrateur d’abonnement classique, rôles RBAC Azure et rôles Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!NOTE]
> Les attributions de rôles RBAC peuvent prendre jusqu’à cinq minutes pour se propager.
>
> Seuls les rôles explicitement définis pour l’accès aux données permettent à un principal de sécurité d’accéder aux données d’objet blob ou de file d’attente. Des rôles tels que **Propriétaire**, **Contributeur** et **Contributeur de comptes de stockage** permettent à un principal de sécurité de gérer un compte de stockage, mais n’accordent pas l’accès aux données de l’objet blob ou de la file d’attente dans ce compte.
>
> Vous pouvez autoriser l’accès aux données d’objet blob ou de file d’attente dans le Portail Azure à l’aide de votre compte Azure AD ou de la clé d’accès du compte de stockage. Pour plus d’informations, voir [Utiliser le portail Azure pour accéder aux données d’objet blob ou de file d’attente](../articles/storage/common/storage-access-blobs-queues-portal.md).
