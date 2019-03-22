---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5d0e919159569e7512b6e7dc0458a4a3e397943b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251965"
---
> [!NOTE]
> - La préversion de l’authentification Azure AD pour les objets blob et les files d’attente est destinée uniquement à une utilisation hors production. Les contrats SLA (contrats de niveau de service) de production ne sont actuellement pas disponibles. Si l’authentification d’Azure AD n’est pas encore prise en charge pour votre scénario, continuez à utiliser l’autorisation basée sur une clé partagée ou les jetons SAP dans vos applications.
>
> - Durant la préversion, les attributions de rôles RBAC peuvent prendre jusqu’à cinq minutes pour se propager.
>
> - Pour autoriser les opérations d’objet blob et de file d’attente avec un jeton OAuth, vous devez utiliser HTTPS.
>
> - Dans sa préversion, le portail Microsoft Azure prend désormais en charge les informations d’identification Azure AD pour lire et écrire les données d’objet blob et de file d’attente. Pour accéder aux données blob et file d’attente dans le portail Azure, un utilisateur doit être affecté le rôle RBAC de lecteur Azure Resource Manager, outre le rôle d’aperçu approprié pour l’accès aux objets blob ou file d’attente. Pour plus d’informations, consultez [accorder l’accès aux conteneurs Azure et de files d’attente avec RBAC dans le portail Azure (aperçu)](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - L’[Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/) utilise actuellement votre clé de compte de stockage pour accéder aux données d’objet blob et de file d’attente. L’accès OAuth est pris en charge pour les objets blob.
>
> - Azure Files prend en charge l’authentification avec Azure AD sur SMB uniquement pour les machines virtuelles jointes au domaine (préversion). Pour en savoir plus sur l’utilisation d’Azure AD sur SMB pour Azure Files, consultez [Vue d’ensemble de l’authentification Azure Active Directory sur SMB pour Azure Files (préversion)](../articles/storage/files/storage-files-active-directory-overview.md).