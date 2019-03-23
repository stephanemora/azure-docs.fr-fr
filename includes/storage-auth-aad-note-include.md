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
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372663"
---
> [!NOTE]
> - Attributions de rôles RBAC peuvent prendre jusqu'à deux minutes pour se propager.
>
> Pour autoriser les opérations d’objet blob et de file d’attente avec un jeton OAuth, vous devez utiliser HTTPS.
>
> - Dans sa préversion, le portail Microsoft Azure prend désormais en charge les informations d’identification Azure AD pour lire et écrire les données d’objet blob et de file d’attente. Pour accéder aux données blob et file d’attente dans le portail Azure, un utilisateur doit être affecté à Azure Resource Manager **lecteur** rôle, outre le rôle approprié pour l’accès aux objets blob ou file d’attente. Pour plus d’informations, consultez [accorder l’accès aux conteneurs Azure et de files d’attente avec RBAC dans le portail Azure](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - L’[Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/) utilise actuellement votre clé de compte de stockage pour accéder aux données d’objet blob et de file d’attente. Si la clé n’est pas disponible, d’autorisation Azure AD est utilisée pour l’accès aux objets BLOB. D’autorisation Azure AD n’est pas pris en charge pour les files d’attente. 
>
> - Azure Files prend en charge l’authentification avec Azure AD sur SMB uniquement pour les machines virtuelles jointes au domaine (préversion). Pour en savoir plus sur l’utilisation d’Azure AD sur SMB pour Azure Files, consultez [Vue d’ensemble de l’authentification Azure Active Directory sur SMB pour Azure Files (préversion)](../articles/storage/files/storage-files-active-directory-overview.md).