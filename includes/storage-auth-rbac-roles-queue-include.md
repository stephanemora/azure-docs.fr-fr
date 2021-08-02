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
ms.openlocfilehash: a93e85114c46f6e0ce252bb4059836d62fadf20f
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904412"
---
RBAC Azure fournit un certain nombre de rôles intégrés pour autoriser l’accès aux données de file d’attente avec Azure AD et OAuth. Voici quelques exemples de rôles qui fournissent des autorisations aux ressources de données dans Stockage Azure :

- [Contributeur aux données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor) : permet d’accorder des autorisations en lecture/écriture/suppression aux files d’attente Azure.
- [Lecteur des données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader) : permet d’accorder des autorisations en lecture seule aux files d’attente Azure.
- [Processeur de messages de données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor) : permet d’accorder des autorisations d’aperçu, de récupération et de suppression aux messages dans les files d’attente Stockage Azure.
- [Expéditeur de messages de données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender) : permet d’accorder des autorisations d’ajout aux messages dans les files d’attente Stockage Azure.

Pour apprendre à lister les rôles RBAC Azure et leurs autorisations, consultez [Lister les définitions de rôles Azure](../articles/role-based-access-control/role-definitions-list.md).

Seuls les rôles explicitement définis pour l’accès aux données permettent à un principal de sécurité d’accéder aux données de file d’attente. Des rôles intégrés tels que **Propriétaire**, **Contributeur** et **Contributeur de comptes de stockage** permettent à un principal de sécurité de gérer un compte de stockage, mais n’accordent pas l’accès aux données de file d’attente dans ce compte via Azure AD. Toutefois, si un rôle comprend **Microsoft.Storage/storageAccounts/listKeys/action**, un utilisateur auquel ce rôle est affecté peut accéder aux données du compte de stockage via l’autorisation de la clé partagée avec les clés d’accès au compte. Pour plus d’informations, consultez [Choisir comment autoriser l’accès à des données blobs dans le portail Azure](../articles/storage/queues/authorize-data-operations-portal.md).

Pour des informations détaillées sur les rôles intégrés Azure pour le Stockage Azure pour les services de données et de gestion, consultez la section **Stockage** dans [Rôles intégrés Azure pour Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Pour plus d’informations sur les différents types de rôles qui fournissent des autorisations dans Azure, consultez [Rôles d’administrateur d’abonnement classique, rôles Azure et rôles Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Les attributions de rôles Azure peuvent prendre jusqu’à 30 minutes pour se propager.
