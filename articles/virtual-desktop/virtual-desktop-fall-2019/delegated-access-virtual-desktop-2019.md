---
title: Accès délégué dans Azure Virtual Desktop (classique) – Azure
description: Découvrez comment déléguer des capacités d’administration lors d’un déploiement d’Azure Virtual Desktop (classique), avec exemples.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9db8ad454ad38f24f32e05bf2f72d67ef7db1971
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111752042"
---
# <a name="delegated-access-in-azure-virtual-desktop-classic"></a>Accès délégué dans Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../delegated-access-virtual-desktop.md).

Azure Virtual Desktop dispose d’un modèle d’accès délégué qui vous permet de définir les droits d’accès d’un utilisateur particulier en lui attribuant un rôle. Une attribution de rôle se compose de trois éléments : un principal de sécurité, une définition de rôle et une étendue. Le modèle d’accès délégué d’Azure Virtual Desktop est basé sur le modèle RBAC d’Azure. Pour en savoir plus sur les attributions de rôle spécifique et leurs composants, consultez [la vue d’ensemble du contrôle d'accès en fonction du rôle Azure](../../role-based-access-control/built-in-roles.md).

L’accès délégué d’Azure Virtual Desktop prend en charge les valeurs suivantes pour chaque élément de l’attribution de rôle :

* Principal de sécurité
    * Utilisateurs
    * Principaux de service
* Définition de rôle
    * Rôles intégrés
* Étendue
    * Groupes de locataires
    * Locataires
    * Pools hôtes
    * Groupes d’applications

## <a name="built-in-roles"></a>Rôles intégrés

L’accès délégué dans Azure Virtual Desktop a plusieurs définitions de rôles intégrés que vous pouvez affecter aux utilisateurs et principaux de service.

* Un propriétaire RDS peut tout gérer, y compris l’accès aux ressources.
* Un contributeur RDS (Services Bureau à distance) peut tout gérer, mais il n’a pas accès aux ressources.
* Un lecteur RDS (Services Bureau à distance) peut tout voir, mais il ne peut apporter aucune modification.
* Un opérateur RDS peut consulter les activités de diagnostic.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets Azure PowerShell pour les attributions de rôles

Vous pouvez exécuter les cmdlets suivantes pour créer, afficher et supprimer des attributions de rôles :

* **Get-RdsRoleAssignment** pour afficher une liste des attributions de rôles.
* **Get-RdsRoleAssignment** pour créer une nouvelle attribution de rôle.
* **Remove-RdsRoleAssignment** pour supprimer les attributions de rôles.

### <a name="accepted-parameters"></a>Paramètres acceptés

Vous pouvez modifier les trois cmdlets de base avec les paramètres suivants :

* **AadTenantId** : spécifie l’ID du locataire Azure Active Directory dont le principal du service est membre.
* **AppGroupName** : nom du groupe d’application du Bureau à distance.
* **Diagnostics** : indique la portée des diagnostics. (Doit être associé aux paramètres **Infrastructure** ou **Locataire**)
* **HostPoolName** : nom du pool d’hôte du Bureau à distance.
* **Infrastructure** : indique la portée de l’infrastructure.
* **RoleDefinitionName** : nom du rôle de contrôle d’accès en fonction du rôle des Services Bureau à distance attribué à l’utilisateur, au groupe ou à l’application. (Par exemple, propriétaire des Services Bureau à distance, lecteur des Services Bureau à distance et ainsi de suite.)
* **ServerPrincipleName** : nom de l’application Azure Active Directory.
* **SignInName** : adresse e-mail de l’utilisateur ou nom d’utilisateur principal.
* **TenantName** : nom du locataire du Bureau à distance.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une liste plus complète des cmdlets PowerShell que chaque rôle peut utiliser, consultez les [Informations de référence PowerShell](/powershell/windows-virtual-desktop/overview).

Pour obtenir des instructions et savoir comment configurer un environnement Bureau virtuel Windows, consultez [Environnement dans Azure Virtual Desktop](environment-setup-2019.md).
