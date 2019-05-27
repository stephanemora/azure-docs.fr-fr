---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/22/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 64751e0fcbf9a2255964d0de673e2cc2020ceb9a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114232"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) prend en charge l’authentification basée sur l’identité sur SMB (Server Message Block) (préversion) via [Azure Active Directory (Azure AD) Domain Services](../articles/active-directory-domain-services/active-directory-ds-overview.md). Vos machines virtuelles Windows jointes à un domaine peuvent accéder aux partages de fichiers Azure en utilisant des informations d’identification [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md). 

Azure AD authentifie une identité, par exemple un utilisateur, un groupe ou un principal de service, avec le [contrôle d’accès en fonction du rôle (RBAC)](../articles/role-based-access-control/overview.md). Vous pouvez définir des rôles RBAC personnalisés qui englobent des jeux communs d’autorisations utilisés pour accéder aux fichiers Azure. Lorsque vous attribuez votre rôle RBAC personnalisé à une identité Azure AD, cette identité se voit octroyer l’accès à un partage de fichiers Azure en fonction de ces autorisations.

Dans la préversion, Azure Files prend aussi en charge la conservation, l’héritage et l’application des listes [DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) sur tous les fichiers et répertoires d’un partage de fichiers. Si vous copiez des données depuis un partage de fichiers vers Azure Files, ou inversement, vous pouvez indiquer de conserver les listes DACL NTFS. De cette façon, vous pouvez implémenter des scénarios de sauvegarde avec Azure Files en conservant vos listes DACL NTFS entre votre partage de fichiers local et votre partage de fichiers cloud. 

> [!NOTE]
> - Dans la préversion, l’authentification Azure AD sur SMB n’est pas prise en charge pour les machines virtuelles Linux. Seules les machines virtuelles Windows Server sont prises en charge.
> - L’authentification Azure AD sur SMB n’est pas prise en charge pour les machines locales accédant à Azure Files.
> - L’authentification Azure AD est disponible uniquement pour les comptes de stockage créés après le 24 septembre 2018.
> - L’authentification Azure AD sur SMB et les ACL NTFS persistants n’est pas prise en charge sur les partages de fichiers Azure gérés par Azure File Sync Service. 
