---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565081"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) prend en charge l’authentification basée sur l’identité sur SMB (Server Message Block) via [Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) [préversion] et [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) en disponibilité générale. Cet article traite de la façon dont Azure Files peut tirer parti des services de domaine, localement ou dans Azure, pour prendre en charge l’accès basé sur l’identité à Azure Files sur SMB. Cela vous permet de remplacer facilement vos serveurs de fichiers existants par Azure Files et de continuer à utiliser votre service d’annuaire existant en conservant l’accès transparent des utilisateurs aux partages de fichiers. 

Azure Files applique l’autorisation d’accès de l’utilisateur au partage de fichiers et au niveau du répertoire/fichier. L’attribution d’autorisations au niveau du partage peut être attribuée aux utilisateurs ou groupes gérés Azure AD par le modèle standard de [contrôle d’accès en fonction du rôle (RBAC)](../articles/role-based-access-control/overview.md). Avec RBAC, les informations d’identification que vous utilisez pour l’accès aux fichiers doivent être disponibles ou synchronisées avec Azure AD. Vous pouvez attribuer des rôles RBAC intégrés tels que Lecteur de partage SMB de données de fichier de stockage à des utilisateurs ou des groupes dans Azure AD pour accorder un accès en lecture à un partage de fichiers Azure.

Au niveau du répertoire/fichier, Azure Files prend en charge la préservation, l’héritage et l’application des [DACL Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) tout comme les serveurs de fichiers Windows. Si vous copiez des données sur SMB depuis un partage de fichiers vers Azure Files, ou inversement, vous pouvez choisir de conserver les DACL Windows. Que vous ayez l’intention ou non d’appliquer l’autorisation, vous pouvez tirer parti d’Azure Files pour sauvegarder des listes de contrôle d’accès ainsi que vos données. 
