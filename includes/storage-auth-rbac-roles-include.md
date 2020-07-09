---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b49d757067d8ee888bee52f3931c8d48d786c044
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024886"
---
Azure fournit les rôles RBAC intégrés suivants pour autoriser l’accès au blob et aux données de file d’attente à l’aide d’Azure AD et d’OAuth :

- [Propriétaire des données Blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) : permet de définir la propriété et de gérer le contrôle d’accès POSIX pour Azure Data Lake Storage Gen2. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contributeur aux données Blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor) : permet d’accorder des autorisations en lecture/écriture/suppression aux ressources de stockage d’objets blob.
- [Lecteur des données blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader) : permet d’accorder des autorisations en lecture seule aux ressources de stockage d’objets blob.
- [Délégation du Stockage Blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator) : Obtenez une clé de délégation d’utilisateur pour créer une signature d’accès partagé qui est signée avec les informations d’identification Azure AD pour un conteneur ou un objet blob.
- [Contributeur aux données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor) : permet d’accorder des autorisations en lecture/écriture/suppression aux files d’attente Azure.
- [Lecteur des données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader) : permet d’accorder des autorisations en lecture seule aux files d’attente Azure.
- [Processeur de messages de données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor) : permet d’accorder des autorisations d’aperçu, de récupération et de suppression aux messages dans les files d’attente Stockage Azure.
- [Expéditeur de messages de données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender) : permet d’accorder des autorisations d’ajout aux messages dans les files d’attente Stockage Azure.

Seuls les rôles explicitement définis pour l’accès aux données permettent à un principal de sécurité d’accéder aux données d’objet blob ou de file d’attente. Des rôles intégrés, tels que **Propriétaire**, **Contributeur** et **Contributeur de comptes de stockage**, permettent à un principal de sécurité de gérer un compte de stockage, mais n’accordent pas l’accès aux données de l’objet blob ou de la file d’attente dans ce compte via Azure AD. Toutefois, si un rôle comprend **Microsoft.Storage/storageAccounts/listKeys/action**, un utilisateur auquel ce rôle est affecté peut accéder aux données du compte de stockage via l’autorisation de la clé partagée avec les clés d’accès au compte. Pour plus d’informations, voir [Utiliser le portail Azure pour accéder aux données d’objet blob ou de file d’attente](../articles/storage/common/storage-access-blobs-queues-portal.md).

Pour obtenir des informations détaillées sur les rôles RBAC intégrés pour Stockage Azure pour les services de données et de gestion, consultez la section **Stockage** dans [Rôles intégrés Azure pour Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). En outre, pour plus d’informations sur les différents types de rôles qui fournissent des autorisations dans Azure, consultez [Rôles d’administrateur d’abonnement classique, rôles RBAC Azure et rôles Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Les attributions de rôles RBAC peuvent prendre jusqu’à cinq minutes pour se propager.
