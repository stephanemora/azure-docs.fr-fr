---
title: Accès délégué dans Windows Desktop version d’évaluation virtuel - Azure
description: Comment déléguer des fonctionnalités d’administration sur un déploiement de Windows Virtual Desktop Preview, y compris des exemples.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 250aea52de63a6397ce00e9cadcadf3a8ba39858
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870504"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Accès délégué dans la version préliminaire de Windows Virtual Desktop

Windows Virtual Desktop Preview dispose d’un modèle d’accès délégué qui vous permet de définir les droits d’accès de qu'un utilisateur particulier est autorisé à avoir en leur attribuant un rôle. Une attribution de rôle comporte trois composants : principal de sécurité, la définition de rôle et étendue. Le modèle d’accès délégué de bureau virtuel Windows est basé sur le modèle RBAC d’Azure. Pour en savoir plus sur les attributions de rôles spécifiques et leurs composants, consultez [la vue d’ensemble du contrôle de l’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

Bureau virtuel Windows déléguée access prend en charge les valeurs suivantes pour chaque élément de l’attribution de rôle :

* Principal de sécurité
    * Utilisateurs
    * Principaux de service
* Définition de rôle
    * Rôles intégrés
* Étendue
    * Groupes de client
    * Locataires
    * Pools d’hôte
    * Groupes d’applications

## <a name="built-in-roles"></a>Rôles intégrés

Accès délégué dans un bureau virtuel Windows a plusieurs définitions de rôle intégré que vous pouvez affecter aux utilisateurs et principaux de service.

* Un propriétaire de services Bureau à distance peut tout gérer, y compris l’accès aux ressources.
* Un contributeur de services Bureau à distance peut gérer tout sauf les accès aux ressources.
* Un lecteur de services Bureau à distance peut tout afficher, mais ne peut pas apporter des modifications.
* Un opérateur de services Bureau à distance peut afficher les activités de diagnostic.

## <a name="powershell-cmdlets-for-role-assignments"></a>Applets de commande PowerShell des attributions de rôles

Vous pouvez exécuter les applets de commande suivantes pour créer, afficher et modifier des attributions de rôles :

* **Get-RdsRoleAssignment** affiche une liste des attributions de rôles.
* **Nouvelle RdsRoleAssignment** crée une nouvelle attribution de rôle.
* **Set-RdsRoleAssignment** modifie les attributions de rôles.

### <a name="accepted-parameters"></a>Paramètres acceptés

Vous pouvez modifier les trois applets de commande base avec les paramètres suivants :

* **AadTenantId**: Spécifie l’ID de locataire Azure Active Directory à partir duquel le principal du service est membre.
* **AppGroupName**: nom du groupe d’application de bureau à distance.
* **Diagnostics**: indique la portée de diagnostics. (Doit être associé à un le **Infrastructure** ou **locataire** paramètres.)
* **HostPoolName**: nom du pool d’hôte Bureau à distance.
* **Infrastructure**: indique la portée de l’infrastructure.
* **RoleDefinitionName**: nom du rôle de contrôle d’accès en fonction du rôle Services Bureau à distance affecté à l’utilisateur, groupe ou une application. (Par exemple, propriétaire de Services Bureau à distance, lecteur de Services Bureau à distance et ainsi de suite.)
* **ServerPrincipleName**: nom de l’application Azure Active Directory.
* **SignInName**: adresse de messagerie de l’utilisateur ou le nom d’utilisateur principal.
* **TenantName**: nom du client Bureau à distance.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une liste plus complète des applets de commande PowerShell chaque rôle peut utiliser, consultez le [référence PowerShell](/powershell/windows-virtual-desktop/overview).

Pour obtenir des instructions pour savoir comment configurer un environnement de bureau virtuel de Windows, consultez [environnement en version préliminaire de bureau virtuel Windows](environment-setup.md).
