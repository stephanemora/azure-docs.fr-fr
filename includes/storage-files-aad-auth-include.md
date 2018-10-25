---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c2dc0f2dea752dd44ab2805a0d6fc0e5517f5c6c
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48831702"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) prend en charge l’authentification basée sur l’identité sur SMB (Server Message Block) (préversion) via [Azure Active Directory (Azure AD) Domain Services](../articles/active-directory-domain-services/active-directory-ds-overview.md). Vos machines virtuelles Windows jointes à un domaine peuvent accéder aux partages de fichiers Azure en utilisant des informations d’identification [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md). 

Azure AD authentifie une identité, par exemple un utilisateur, un groupe ou un principal de service, avec le [contrôle d’accès en fonction du rôle (RBAC)](../articles/role-based-access-control/overview.md). Vous pouvez définir des rôles RBAC personnalisés qui englobent des jeux communs d’autorisations utilisés pour accéder aux fichiers Azure. Lorsque vous attribuez votre rôle RBAC personnalisé à une identité Azure AD, cette identité se voit octroyer l’accès à un partage de fichiers Azure en fonction de ces autorisations.

Dans la préversion, Azure Files prend aussi en charge la conservation, l’héritage et l’application des listes [DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) sur tous les fichiers et répertoires d’un partage de fichiers. Si vous copiez des données depuis un partage de fichiers vers Azure Files, ou inversement, vous pouvez indiquer de conserver les listes DACL NTFS. De cette façon, vous pouvez implémenter des scénarios de sauvegarde avec Azure Files en conservant vos listes DACL NTFS entre votre partage de fichiers local et votre partage de fichiers cloud. 

> [!NOTE]
> Dans la préversion, l’authentification Azure AD sur SMB n’est pas prise en charge pour les machines virtuelles Linux. Seules les machines virtuelles Windows Server sont prises en charge.

> L’authentification Azure AD sur SMB n’est pas prise en charge pour les machines locales accédant à Azure Files à l’aide d’informations d’identification AD ou AAD.

> L’authentification Azure AD est disponible uniquement pour les comptes de stockage créés après le 24 septembre 2018.
