---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/07/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 54f88c362f0870776972693d35c7a34d761c9f5b
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904408"
---
RBAC Azure fournit un certain nombre de rôles intégrés pour autoriser l’accès aux données de blob avec Azure AD et OAuth. Voici quelques exemples de rôles qui fournissent des autorisations aux ressources de données dans Stockage Azure :

- [Propriétaire des données Blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) : permet de définir la propriété et de gérer le contrôle d’accès POSIX pour Azure Data Lake Storage Gen2. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contributeur aux données Blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor) : permet d’accorder des autorisations en lecture/écriture/suppression aux ressources de stockage d’objets blob.
- [Lecteur des données blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader) : permet d’accorder des autorisations en lecture seule aux ressources de stockage d’objets blob.
- [Délégation du Stockage Blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator) : Obtenez une clé de délégation d’utilisateur pour créer une signature d’accès partagé qui est signée avec les informations d’identification Azure AD pour un conteneur ou un objet blob.

Pour apprendre à lister les rôles RBAC Azure et leurs autorisations, consultez [Lister les définitions de rôles Azure](../articles/role-based-access-control/role-definitions-list.md).

Seuls les rôles explicitement définis pour l’accès aux données permettent à un principal de sécurité d’accéder aux données de blob. Des rôles intégrés tels que **Propriétaire**, **Contributeur** et **Contributeur de comptes de stockage** permettent à un principal de sécurité de gérer un compte de stockage, mais n’accordent pas l’accès aux données de blob dans ce compte via Azure AD. Toutefois, si un rôle comprend **Microsoft.Storage/storageAccounts/listKeys/action**, un utilisateur auquel ce rôle est affecté peut accéder aux données du compte de stockage via l’autorisation de la clé partagée avec les clés d’accès au compte. Pour plus d’informations, consultez [Choisir comment autoriser l’accès à des données blobs dans le portail Azure](../articles/storage/blobs/authorize-data-operations-portal.md).

Pour des informations détaillées sur les rôles intégrés Azure pour le Stockage Azure pour les services de données et de gestion, consultez la section **Stockage** dans [Rôles intégrés Azure pour Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Pour plus d’informations sur les différents types de rôles qui fournissent des autorisations dans Azure, consultez [Rôles d’administrateur d’abonnement classique, rôles Azure et rôles Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Les attributions de rôles Azure peuvent prendre jusqu’à 30 minutes pour se propager.
