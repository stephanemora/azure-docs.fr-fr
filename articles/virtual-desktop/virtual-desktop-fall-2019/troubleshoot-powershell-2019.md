---
title: Azure Virtual Desktop (classique) PowerShell – Azure
description: Guide pratique pour résoudre les problèmes de PowerShell lors de la configuration d’un environnement de locataire Azure Virtual Desktop (classique).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6ce64aa104ca261845740f9b448d5d4e7afd10e5
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751646"
---
# <a name="azure-virtual-desktop-classic-powershell"></a>Azure Virtual Desktop (classique) PowerShell

> [!IMPORTANT]
> Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../troubleshoot-powershell.md).

Cet article permet de résoudre les problèmes et erreurs rencontrés lors de l’utilisation de PowerShell avec Azure Virtual Desktop. Pour plus d’informations sur les Services Bureau à distance PowerShell, consultez [Azure Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Fournir des commentaires

Pour discuter du service Azure Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté, consultez le site [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="powershell-commands-used-during-azure-virtual-desktop-setup"></a>Commandes PowerShell utilisées pendant la configuration d’Azure Virtual Desktop

Cette section présente les commandes PowerShell généralement employées pour configurer Azure Virtual Desktop. Elle explique également comment résoudre les problèmes qui peuvent se produire lors de leur utilisation.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Erreur : Commande Add-RdsAppGroupUser : le UserPrincipalName spécifié est déjà affecté à un groupe d’applications RemoteApp dans le pool d’hôte spécifié

```powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Cause :** Le nom d’utilisateur utilisé a déjà été attribué à un groupe d’applications d’un type différent. Les utilisateurs ne peuvent pas être affectés à la fois à un Bureau à distance et à un groupe d’applications à distance dans un même pool d’hôte de session.

**Correctif :** Si un utilisateur a besoin tant d’applications à distance que d’un Bureau à distance, créez des pools d’hôte différents ou accordez un accès utilisateur au Bureau à distance, ce qui permet d’utiliser tout e application sur la machine virtuelle de l’hôte de la session.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Erreur : Commande Add-RdsAppGroupUser : le UserPrincipalName spécifié n’existe pas dans l’Azure Active Directory associé au locataire du Bureau à distance

```powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Cause :** L’utilisateur spécifié par -UserPrincipalName est introuvable dans l’instance Azure Active Directory liée au locataire Azure Virtual Desktop.

**Correctif :** Vérifiez les éléments dans la liste suivante.

- L’utilisateur est synchronisé sur Azure Active Directory.
- L’utilisateur n’est pas lié à commerce B2C ou B2B.
- Le locataire Azure Virtual Desktop est lié à la bonne instance Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Erreur : Get-RdsDiagnosticActivities : l’utilisateur n’est pas autorisé à interroger le service de gestion

```powershell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Cause :** paramètre -TenantName

**Correctif :** &mettez Get-RdsDiagnosticActivities avec -TenantName \<TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Erreur : Get-RdsDiagnosticActivities : l’utilisateur n’est pas autorisé à interroger le service de gestion

```powershell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Cause :** utilisation du commutateur -Deployment.

**Correctif :** le commutateur -Deployment ne peut être utilisé que par des administrateurs de déploiement. Ces administrateurs sont généralement membres de l’équipe Services Bureau à distance/Azure Virtual Desktop. Remplacez le commutateur -Deployment par -TenantName \<TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Erreur : New-RdsRoleAssignment : l’utilisateur n’est pas autorisé à interroger le service de gestion

**Cause 1 :** Le compte utilisé ne dispose pas des autorisations de propriétaire de Services Bureau à distance sur le client.

**Correctif 1 :** Un utilisateur disposant d’autorisations de propriétaire de Services Bureau à distance doit exécuter l’attribution de rôle.

**Cause 2 :** Le compte utilisé dispose d’autorisations de propriétaire Services Bureau à distance, mais ne fait pas partie de l’annuaire Azure Active Directory du locataire ou n’est pas autorisé à interroger l’annuaire Azure Active Directory où se trouve l’utilisateur.

**Correctif 2 :** Un utilisateur disposant d’autorisations Active Directory doit exécuter l’attribution de rôle.

> [!NOTE]
> New-RdsRoleAssignment ne peut pas accorder d’autorisations à un utilisateur qui n’existe pas dans l’Azure Active Directory (AD).

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la résolution des problèmes d’Azure Virtual Desktop et des procédures d’escalade, consultez [Vue d’ensemble de la résolution des problèmes, commentaires et support](troubleshoot-set-up-overview-2019.md).
- Pour résoudre les problèmes de création d’un locataire et d’un pool d’hôtes dans un environnement Azure Virtual Desktop, consultez [Création d’un locataire et d’un pool d’hôtes](troubleshoot-set-up-issues-2019.md).
- Pour résoudre les problèmes de configuration d’une machine virtuelle dans Azure Virtual Desktop, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration-2019.md).
- Pour résoudre les problèmes de connexion client Azure Virtual Desktop, consultez [Connexions au service Azure Virtual Desktop](troubleshoot-service-connection-2019.md).
- Pour résoudre les problèmes liés aux clients Bureau à distance, consultez [Résoudre des problèmes du client Bureau à distance](../troubleshoot-client.md).
- Pour plus d’informations sur le service, consultez [Environnement Azure Virtual Desktop](environment-setup-2019.md).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](../../azure-resource-manager/management/view-activity-logs.md).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](../../azure-resource-manager/templates/deployment-history.md).
