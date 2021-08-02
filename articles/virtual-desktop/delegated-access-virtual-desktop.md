---
title: Accès délégué dans Azure Virtual Desktop – Azure
description: Découvrez comment déléguer des capacités d’administration sur un déploiement Azure Virtual Desktop, avec des exemples.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 2367a2840e64956d3dfca41ce8ee5e31c9f354c7
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751988"
---
# <a name="delegated-access-in-azure-virtual-desktop"></a>Accès délégué dans Azure Virtual Desktop

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop pour Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).

Azure Virtual Desktop dispose d’un modèle d’accès délégué qui permet de définir les droits d’accès d’un utilisateur particulier en lui attribuant un rôle. Une attribution de rôle se compose de trois éléments : un principal de sécurité, une définition de rôle et une étendue. Le modèle d’accès délégué d’Azure Virtual Desktop est basé sur le modèle RBAC d’Azure. Pour en savoir plus sur les attributions de rôle spécifique et leurs composants, consultez [la vue d’ensemble du contrôle d'accès en fonction du rôle Azure](../role-based-access-control/built-in-roles.md).

L’accès délégué d’Azure Virtual Desktop prend en charge les valeurs suivantes pour chaque élément de l’attribution de rôle :

* Principal de sécurité
    * Utilisateurs
    * Groupes d’utilisateurs
    * Principaux de service
* Définition de rôle
    * Rôles intégrés
    * Rôles personnalisés
* Étendue
    * Pools hôtes
    * Groupes d’applications
    * Workspaces

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets Azure PowerShell pour les attributions de rôles

Avant de commencer, veillez à suivre les instructions figurant dans [Configuration du module PowerShell](powershell-module.md) pour configurer le module PowerShell Azure Virtual Desktop, si ce n’est pas déjà fait.

Azure Virtual Desktop utilise le contrôle d’accès en fonction du rôle (RBAC, Role-Based Access Control) Azure pendant la publication de groupes d’applications auprès d’utilisateurs ou de groupes d’utilisateurs. Le rôle d’utilisateur Virtualisation du Bureau est attribué à l’utilisateur ou au groupe d’utilisateurs et l’étendue correspond au groupe d’applications. Ce rôle donne à l’utilisateur un accès spécial aux données sur le groupe d’applications.

Exécutez la cmdlet suivante pour ajouter des utilisateurs Azure Active Directory à un groupe d’applications :

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Exécutez la cmdlet suivante pour ajouter un groupe d’utilisateurs Azure Active Directory à un groupe d’applications :

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une liste plus complète des cmdlets PowerShell que chaque rôle peut utiliser, consultez les [Informations de référence PowerShell](/powershell/windows-virtual-desktop/overview).

Pour obtenir la liste complète des rôles pris en charge dans RBAC Azure, consultez [Rôles intégrés Azure](../role-based-access-control/built-in-roles.md).

Pour savoir comment configurer un environnement Azure Virtual Desktop, consultez [Environnement Azure Virtual Desktop](environment-setup.md).
