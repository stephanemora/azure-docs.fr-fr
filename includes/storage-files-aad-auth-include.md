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
ms.openlocfilehash: d1b10b55481b41f42c6c872522d3dd4dd4be0e77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570587"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) prend en charge l’authentification basée sur l’identité sur SMB (Server Message Block) (préversion) avec [Azure Active Directory Domain Services (AD DS)](../articles/active-directory-domain-services/overview.md). Vos machines virtuelles Windows jointes à un domaine peuvent accéder aux partages de fichiers Azure en utilisant les informations d’identification [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md).

Vous pouvez gérer l’accès du niveau de partage Azure Files à une identité, comme un utilisateur ou un groupe dans Azure AD en utilisant le [contrôle d’accès en fonction du rôle (RBAC)](../articles/role-based-access-control/overview.md). Vous pouvez définir des rôles RBAC personnalisés comprenant des ensembles communs d’autorisations utilisés pour accéder à Azure Files. Lorsque vous attribuez votre rôle RBAC personnalisé à une identité Azure AD, cette identité se voit octroyer l’accès à un partage de fichiers Azure en fonction de ces autorisations.

Dans la préversion, Azure Files prend aussi en charge la conservation, l’héritage et l’application des listes [DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) sur tous les fichiers et répertoires d’un partage de fichiers. Si vous copiez des données depuis un partage de fichiers vers Azure Files, ou inversement, vous pouvez indiquer de conserver les listes DACL NTFS. De cette façon, vous pouvez implémenter des scénarios de sauvegarde à ’aide d’Azure Files en conservant vos listes DACL NTFS entre votre partage de fichiers local et votre partage de fichiers cloud. 

> [!NOTE]
> - L’authentification Azure Active Directory Domain Services sur l’accès SMB n’est pas prise en charge pour les machines virtuelles Linux. Seules les machines virtuelles Windows sont prises en charge.
> - L’authentification Azure Active Directory Domain Services sur l’accès SMB n’est pas prise en charge les machines jointes au domaine Active Directory. Entre-temps, envisagez d’utiliser [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) pour commencer à migrer vos données vers Azure Files et continuer à appliquer le contrôle d’accès à l’aide des informations d’identification d’Active Directory à partir de vos machines locales jointes au domaine Active Directory. 
> - L’authentification Azure Active Directory Domain Services sur l’accès SMB est uniquement disponible pour les comptes de stockage créés après le 24 septembre 2018.
> - L’authentification Azure Active Directory Domain Services sur l’accès SMB et les DACL NTFS persistants n’est pas prise en charge sur les partages de fichiers Azure managés par le service Azure File Sync.
