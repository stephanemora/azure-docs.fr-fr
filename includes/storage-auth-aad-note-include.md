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
ms.openlocfilehash: 2d641287bcf095f13719667a91b7f61295309b5d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430355"
---
> [!NOTE]
> - La préversion de l’authentification Azure AD pour les objets blob et les files d’attente est destinée uniquement à une utilisation hors production. Les contrats SLA (contrats de niveau de service) de production ne sont actuellement pas disponibles. Si l’authentification d’Azure AD n’est pas encore prise en charge pour votre scénario, continuez à utiliser l’autorisation basée sur une clé partagée ou les jetons SAP dans vos applications.
>
> - Durant la préversion, les attributions de rôles RBAC peuvent prendre jusqu’à cinq minutes pour se propager.
>
> - Vous devez utiliser HTTPS pour l’authentification auprès d’Azure AD lors de l’appel d’opérations d’objet blob et de file d’attente.
>
> - Dans sa préversion, le portail Microsoft Azure prend désormais en charge les informations d’identification Azure AD pour lire et écrire les données d’objet blob.
> 
> - Pour l’instant, le portail Microsoft Azure ne prend pas en charge les informations d’identification Azure AD pour lire et écrire les données de file d’attente. Les données de la file d’attente sont accessibles via les clés de votre compte de stockage.
>
> - L’[Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/) utilise actuellement votre clé de compte de stockage pour accéder aux données d’objet blob et de file d’attente.
>
> - Azure Files prend en charge l’authentification avec Azure AD sur SMB uniquement pour les machines virtuelles jointes au domaine (préversion). Pour en savoir plus sur l’utilisation d’Azure AD sur SMB pour Azure Files, consultez [Vue d’ensemble de l’authentification Azure Active Directory sur SMB pour Azure Files (préversion)](../articles/storage/files/storage-files-active-directory-overview.md).



