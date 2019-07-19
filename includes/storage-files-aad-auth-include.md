---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269367"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) prend en charge l’authentification basée sur l’identité sur SMB (Server Message Block) (préversion) via [Azure Active Directory (Azure AD) Domain Services](../articles/active-directory-domain-services/overview.md). Vos machines virtuelles Windows jointes à un domaine peuvent accéder aux partages de fichiers Azure en utilisant des informations d’identification [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md). 

Vous pouvez gérer l’accès du niveau de partage Azure Files à une identité, comme un utilisateur ou un groupe dans Azure AD, avec le [contrôle d’accès en fonction du rôle (RBAC)](../articles/role-based-access-control/overview.md). Vous pouvez définir des rôles RBAC personnalisés qui englobent des jeux communs d’autorisations utilisés pour accéder aux fichiers Azure. Lorsque vous attribuez votre rôle RBAC personnalisé à une identité Azure AD, cette identité se voit octroyer l’accès à un partage de fichiers Azure en fonction de ces autorisations.

Dans la préversion, Azure Files prend aussi en charge la conservation, l’héritage et l’application des listes [DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) sur tous les fichiers et répertoires d’un partage de fichiers. Si vous copiez des données depuis un partage de fichiers vers Azure Files, ou inversement, vous pouvez indiquer de conserver les listes DACL NTFS. De cette façon, vous pouvez implémenter des scénarios de sauvegarde avec Azure Files en conservant vos listes DACL NTFS entre votre partage de fichiers local et votre partage de fichiers cloud. 

> [!NOTE]
> - L’authentification Azure AD Domain Service sur l’accès SMB n’est pas prise en charge pour les machines virtuelles Linux. Seules les machines virtuelles Windows sont prises en charge.
> - L’authentification Azure AD Domain Service sur l’accès SMB n’est pas prise en charge pour une machine jointe au domaine Active Directory. En attendant, vous pouvez projeter de tirer parti d’[Azure Files Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) pour commencer la migration de vos données vers Azure Files, et continuer d’appliquer le contrôle d’accès avec les informations d’identification Active Directory depuis vos machines locales jointes au domaine Active Directory. 
> - L’authentification Azure AD Domain Service sur l’accès SMB est uniquement disponible pour les comptes de stockage créés après le 24 septembre 2018.
> - L’authentification Azure AD Domain Service pour l’accès SMB et les DACL NTFS persistants n’est pas prise en charge sur les partages de fichiers Azure managés par le service Azure File Sync. 
