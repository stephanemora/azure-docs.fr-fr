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
ms.openlocfilehash: fbc5ca4d433be65d43ae535703cc7f765dda0a1e
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292680"
---
> [!NOTE]
> - La préversion de l’authentification Azure AD pour les objets blob et les files d’attente est destinée uniquement à une utilisation hors production. Les contrats SLA (contrats de niveau de service) de production ne sont actuellement pas disponibles. Si l’authentification d’Azure AD n’est pas encore prise en charge pour votre scénario, continuez à utiliser l’autorisation basée sur une clé partagée ou les jetons SAP dans vos applications.
>
> - Durant la préversion, les attributions de rôles RBAC peuvent prendre jusqu’à cinq minutes pour se propager.
>
> - Pour autoriser les opérations d’objet blob et de file d’attente avec un jeton OAuth, vous devez utiliser HTTPS.
>
> - Dans sa préversion, le portail Microsoft Azure prend désormais en charge les informations d’identification Azure AD pour lire et écrire les données d’objet blob et de file d’attente.
> 
> - L’[Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/) utilise actuellement votre clé de compte de stockage pour accéder aux données d’objet blob et de file d’attente.
>
> - Azure Files prend en charge l’authentification avec Azure AD sur SMB uniquement pour les machines virtuelles jointes au domaine (préversion). Pour en savoir plus sur l’utilisation d’Azure AD sur SMB pour Azure Files, consultez [Vue d’ensemble de l’authentification Azure Active Directory sur SMB pour Azure Files (préversion)](../articles/storage/files/storage-files-active-directory-overview.md).



