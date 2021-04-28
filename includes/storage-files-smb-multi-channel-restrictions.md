---
title: Fichier include
description: Fichier Include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/26/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 04ded4340eef0bfe5bc91ee2e3f2552975df2e05
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065620"
---
SMB Multichannel pour les partages de fichiers Azure présente actuellement les restrictions suivantes :
- Pris en charge uniquement pour les clients Windows. 
- Le nombre maximal de canaux est de quatre.
- SMB Direct n’est pas pris en charge.
- Les points de terminaison privés pour les comptes de stockage ne sont pas pris en charge.
- Pour les comptes de stockage dotés de [l’authentification basée sur l’identité](../articles/storage/files/storage-files-active-directory-overview.md) Active Directory Domain Services (AD DS) ou Azure AD DS locale pour Azure Files, les clients SMB ne peuvent pas utiliser l’Explorateur de fichiers Windows pour configurer des autorisations NTFS sur les répertoires et les fichiers.
    - Utilisez plutôt l’outil Windows [icacls](/windows-server/administration/windows-commands/icacls) ou la commande [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) pour configurer les autorisations.

