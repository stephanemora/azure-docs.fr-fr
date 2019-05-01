---
title: PowerShell liée à un bureau virtuel Windows - Azure
description: Comment résoudre les problèmes avec PowerShell quand vous configurez un environnement de client de bureau virtuel Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: ad32f7ff883812830dbcf2ed900c4034bd90abfc
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927506"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows PowerShell de bureau virtuel

Utilisez cet article pour résoudre les problèmes et erreurs lors de l’utilisation de PowerShell avec le bureau virtuel Windows. Pour plus d’informations sur PowerShell de Services Bureau à distance, consultez [Windows Virtual Desktop Powershell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Fournir des commentaires

Nous n’acceptons actuellement aucun cas de support pendant que Windows Virtual Desktop est en préversion. Rendez-vous sur le site [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour discuter du service Windows Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Commandes PowerShell utilisées pendant l’installation de bureau virtuel Windows

Cette section répertorie les commandes PowerShell qui sont généralement utilisés lors de la configuration de bureau virtuel Windows et fournit les moyens de résoudre les problèmes qui peuvent se produire lors de leur utilisation.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Error: Commande RdsAppGroupUser ajouter--UserPrincipalName spécifié est déjà affecté à un groupe d’applications RemoteApp dans le Pool d’hôte spécifié

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Cause :** Le nom d’utilisateur utilisé a déjà été attribué à un groupe de l’application d’un type différent. Les utilisateurs ne peuvent pas être attribués à un groupe d’application à distance et Bureau à distance sous le même pool d’hôte de session.

**Correctif :** Si l’utilisateur a besoin d’applications à distance et Bureau à distance, créer des pools d’hôte différent ou accorder l’accès utilisateur pour le Bureau à distance, ce qui autorise l’utilisation de n’importe quelle application sur la machine virtuelle d’hôte de session.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Error: Commande RdsAppGroupUser ajouter--UserPrincipalName spécifié n’existe pas dans Azure Active Directory associés au client Bureau à distance

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Cause :** Impossible de trouver l’utilisateur spécifié par - UserPrincipalName dans Azure Active Directory lié au client de bureau virtuel Windows.

**Correctif :** Vérifiez les éléments dans la liste suivante.

- L’utilisateur est synchronisé à Azure Active Directory.
- L’utilisateur n’est pas liée à l’entreprise à consommateur (B2C) ou commerce (B2B) de business-to-business.
- Le client de bureau virtuel Windows est lié à correct Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Error: Get-RdsDiagnosticActivities--Utilisateur n’est pas autorisé à interroger le service de gestion

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Cause :** TenantName - paramètre

**Correctif :** Exécutez Get-RdsDiagnosticActivities avec TenantName - <TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Error: Get-RdsDiagnosticActivities--l’utilisateur n’est pas autorisé à interroger le service de gestion

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Cause :** À l’aide de - commutateur de déploiement.

**Correctif :** -commutateur de déploiement peut être utilisé uniquement par les administrateurs de déploiement. Ces administrateurs sont généralement des membres de l’équipe à distance Bureau virtuel Services/Windows de bureau. Remplacez le déploiement commutateur - avec - TenantName <TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Error: Nouveau-RdsRoleAssignment--l’utilisateur n’est pas autorisé à interroger le service de gestion

**Cause 1 :** Le compte utilisé ne dispose pas des autorisations de propriétaire de Services Bureau à distance sur le client.

**Correctif 1 :** Un utilisateur disposant d’autorisations de propriétaire de Services Bureau à distance doit exécuter l’attribution de rôle.

**Cause 2 :** Le compte utilisé dispose des autorisations de propriétaire de Services Bureau à distance, mais ne fait pas partie du locataire Azure Active Directory ou ne possède pas les autorisations pour interroger Azure Active Directory où se trouve l’utilisateur.

**Correctif 2 :** Un utilisateur disposant des autorisations Active Directory doit exécuter l’attribution de rôle.

>[!Note]
>Nouvelle RdsRoleAssignment ne peut pas accorder des autorisations à un utilisateur qui n’existe pas dans Azure Active Directory (AD).

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble sur la résolution des problèmes de bureau virtuel Windows et les pistes d’escalade de verrous, consultez [résolution des problèmes de vue d’ensemble, commentaires et support](troubleshoot-set-up-overview.md).
- Pour résoudre les problèmes lors de la création d’un pool de client et de l’hôte dans un environnement de bureau virtuel de Windows, consultez [locataire et hôte de la création du pool](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes lors de la configuration d’une machine virtuelle (VM) dans un bureau virtuel Windows, consultez [configuration de machine virtuelle hôte de Session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes de connexion de client de bureau virtuel de Windows, consultez [les connexions Bureau à distance client](troubleshoot-client-connection.md).
- Pour en savoir plus sur le service en version préliminaire, consultez [environnement en version préliminaire de Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Suivez le [Didacticiel : Résoudre les problèmes de déploiements de modèle Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).