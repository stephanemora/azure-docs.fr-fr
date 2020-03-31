---
title: Synchronisation délimitée pour Azure AD Domain Services | Microsoft Docs
description: Découvrez comment configurer une synchronisation délimitée (scoped) entre Azure AD et un domaine managé Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: cc126af67a0d8627d61e595cee56f3df8973340d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613049"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Configurer une synchronisation délimitée entre Azure AD et Azure Active Directory Domain Services

Pour assurer des services d’authentification, Azure Active Directory Domain Services (Azure AD DS) synchronise les utilisateurs et les groupes à partir d’Azure AD. Dans un environnement hybride, les utilisateurs et les groupes d’un environnement Active Directory Domain Services (AD DS) local peuvent d’abord être synchronisés avec Azure AD via Azure AD Connect, puis avec Azure AD DS.

Par défaut, tous les utilisateurs et groupes d’un annuaire Azure AD sont synchronisés avec un domaine managé Azure AD DS. Si vous avez des besoins spécifiques, vous pouvez opter pour la synchronisation d’un ensemble défini d’utilisateurs.

Cet article vous montre comment créer un domaine managé Azure AD DS qui utilise la synchronisation délimitée et comment modifier ou désactiver l’ensemble des utilisateurs visés.

## <a name="scoped-synchronization-overview"></a>Vue d’ensemble de la synchronisation délimitée

Par défaut, tous les utilisateurs et groupes d’un annuaire Azure AD sont synchronisés avec un domaine managé Azure AD DS. Si seuls quelques utilisateurs ont besoin d’accéder au domaine managé, vous pouvez synchroniser uniquement ces comptes d’utilisateurs. Cette synchronisation délimitée est basée sur les groupes. Quand vous configurez la synchronisation délimitée basée sur les groupes, seuls les comptes d’utilisateurs qui appartiennent aux groupes que vous spécifiez sont synchronisés avec le domaine managé Azure AD DS.

Le tableau suivant indique dans les grandes lignes comment utiliser la synchronisation délimitée :

| État actuel | État souhaité | Configuration nécessaire |
| --- | --- | --- |
| Un domaine managé existant est configuré pour synchroniser tous les comptes et groupes d’utilisateurs. | Or, vous souhaitez synchroniser uniquement les comptes d’utilisateurs qui appartiennent à certains groupes. | Vous ne pouvez pas passer d’une synchronisation de tous les utilisateurs à une utilisation de la synchronisation délimitée. [Supprimez le domaine managé existant](delete-aadds.md), puis suivez les étapes de cet article pour recréer un domaine managé Azure AD DS avec la synchronisation délimitée configurée. |
| Aucun domaine managé existant. | Vous souhaitez créer un nouveau domaine managé et synchroniser uniquement les comptes utilisateur appartenant à des groupes spécifiques. | Suivez les étapes de cet article pour créer un domaine managé Azure AD DS avec la synchronisation délimitée configurée. |
| Un domaine managé existant est configuré pour synchroniser uniquement les comptes qui appartiennent à certains groupes. | Vous voulez modifier la liste des groupes dont les utilisateurs doivent être synchronisés avec le domaine managé Azure AD DS. | Suivez les étapes de cet article pour modifier la synchronisation délimitée. |

Vous utilisez le portail Azure ou PowerShell pour configurer les paramètres de la synchronisation délimitée :

| Action | | |
|--|--|--|
| Créer un domaine managé Azure AD DS et configurer la synchronisation délimitée | [Azure portal](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Modifier la synchronisation délimitée | [Azure portal](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Désactiver la synchronisation délimitée | [Azure portal](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> La modification de l’étendue de la synchronisation conduit le domaine managé Azure AD DS à resynchroniser toutes les données.
> 
>  * Quand vous modifiez l’étendue de synchronisation d’un domaine managé Azure AD DS, une resynchronisation complète se produit.
>  * Les objets qui ne sont plus nécessaires dans le domaine managé Azure AD DS sont supprimés. De nouveaux objets sont créés dans le domaine managé.
>  * La resynchronisation peut prendre un certain temps. La durée de cette opération varie en fonction du nombre d’objets (utilisateurs, groupes et appartenances aux groupes) présents dans le domaine managé Azure AD DS et l’annuaire Azure AD. Pour les annuaires volumineux avec plusieurs centaines de milliers d’objets, la resynchronisation peut prendre quelques jours.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Activer la synchronisation délimitée à partir du portail Azure

1. Suivez le [tutoriel pour créer et configurer une instance Azure AD DS](tutorial-create-instance-advanced.md). Respectez tous les prérequis et effectuez toutes les étapes de déploiement en dehors de celles prévues pour l’étendue de la synchronisation.
1. Choisissez **Inclus dans l’étendue** à l’étape de synchronisation, puis sélectionnez les groupes Azure AD à synchroniser avec l’instance Azure AD DS.

Le déploiement du domaine managé Azure AD DS peut prendre jusqu’à une heure. Sur le portail Azure, la page **Vue d’ensemble** de votre domaine managé Azure AD DS indique l’état actuel tout au long de cette phase de déploiement.

Une fois que le portail Azure a indiqué que le provisionnement du domaine managé Azure AD DS était terminé, voici les tâches qu’il convient d’effectuer :

* Mettez à jour les paramètres DNS pour le réseau virtuel afin que les machines virtuelles puissent trouver le domaine géré pour l’authentification ou la jonction de domaine.
    * Pour configure le DNS, sélectionnez votre domaine managé Azure AD DS dans le portail. Dans la fenêtre **Vue d’ensemble**, vous êtes invité à configurer automatiquement ces paramètres DNS.
* [Activez la synchronisation de mots de passe avec Azure AD Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) de sorte que les utilisateurs puissent se connecter au domaine managé avec leurs informations d’identification d’entreprise.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Modifier la synchronisation délimitée à partir du portail Azure

Pour modifier la liste des groupes dont les utilisateurs doivent être synchronisés avec le domaine managé Azure AD DS, effectuez les étapes suivantes :

1. Sur le portail Azure, recherchez et sélectionnez **Azure AD Domain Services**. Choisissez votre instance, par exemple *aaddscontoso.com*.
1. Sélectionnez **Synchronisation** dans le menu de gauche.
1. Pour ajouter un groupe, choisissez **+ Sélectionner des groupes** dans la partie supérieure, puis choisissez les groupes à ajouter.
1. Pour supprimer un groupe de l’étendue de la synchronisation, sélectionnez-le dans la liste des groupes actuellement synchronisés et choisissez **Supprimer les groupes**.
1. Une fois que toutes les modifications ont été apportées, sélectionnez **Enregistrer l’étendue de la synchronisation**.

La modification de l’étendue de la synchronisation conduit le domaine managé Azure AD DS à resynchroniser toutes les données. Les objets qui ne sont plus nécessaires dans le domaine managé Azure AD DS sont supprimés, et la resynchronisation peut prendre un certain temps.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Désactiver la synchronisation délimitée à partir du portail Azure

Pour désactiver la synchronisation délimitée basée sur les groupes pour un domaine managé Azure AD DS, effectuez les étapes suivantes :

1. Sur le portail Azure, recherchez et sélectionnez **Azure AD Domain Services**. Choisissez votre instance, par exemple *aaddscontoso.com*.
1. Sélectionnez **Synchronisation** dans le menu de gauche.
1. Faites passer la définition de la synchronisation de **Inclus dans l’étendue** à **Tout**, puis sélectionnez **Enregistrer l’étendue de la synchronisation**.

La modification de l’étendue de la synchronisation conduit le domaine managé Azure AD DS à resynchroniser toutes les données. Les objets qui ne sont plus nécessaires dans le domaine managé Azure AD DS sont supprimés, et la resynchronisation peut prendre un certain temps.

## <a name="powershell-script-for-scoped-synchronization"></a>Script PowerShell pour la synchronisation délimitée

Pour configurer la synchronisation délimitée à l’aide de PowerShell, commencez par enregistrer le script suivant dans un fichier nommé `Select-GroupsToSync.ps1`. Ce script configure Azure AD DS de façon à synchroniser les groupes sélectionnés à partir d’Azure AD. Tous les comptes d’utilisateurs qui font partie des groupes spécifiés sont synchronisés avec le domaine managé Azure AD DS.

Ce script est utilisé dans les étapes supplémentaires de cet article.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization-using-powershell"></a>Activer la synchronisation délimitée à partir de PowerShell

Utilisez PowerShell pour cette procédure. Consultez les instructions pour [activer Azure Active Directory Domain Services à l’aide de PowerShell](powershell-create-instance.md). Certaines étapes de cet article sont légèrement modifiées pour configurer la synchronisation étendue.

1. Effectuez les tâches de l’article ci-dessous pour activer Azure AD DS à l’aide de PowerShell. Arrêtez-vous à l’étape où le domaine managé est réellement créé. Vous devez configurer la synchronisation délimitée après avoir créé le domaine managé AD DS.

   * [Installez les modules PowerShell nécessaires](powershell-create-instance.md#prerequisites).
   * [Créez le principal du service et le groupe Azure AD nécessaires à l’accès administratif](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Créez des ressources Azure complémentaires comme un réseau virtuel et des sous-réseaux](powershell-create-instance.md#create-supporting-azure-resources).

1. Identifiez-y les groupes et les utilisateurs que vous souhaitez synchroniser à partir d’Azure AD. Dressez une liste des noms complets des groupes à synchroniser avec Azure AD DS.

1. Exécutez le [script de la section précédente](#powershell-script-for-scoped-synchronization) et utilisez le paramètre *-groupsToAdd* pour transmettre la liste des groupes à synchroniser.

   > [!WARNING]
   > Vous devez inclure le groupe *AAD DC Administrators* dans la liste des groupes configurés pour la synchronisation délimitée. Si vous n’incluez pas ce groupe, le domaine managé Azure AD DS est inutilisable.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Créez ensuite le domaine managé Azure AD DS et activez la synchronisation délimitée basée sur les groupes. Incluez *"filteredSync" = "Enabled"* dans le paramètre *-Properties*.

    Définissez votre ID d’abonnement Azure, puis attribuez un nom au domaine managé, par exemple *aaddscontoso.com*. Vous pouvez obtenir votre ID d’abonnement à l’aide de l’applet de commande [Get-AzSubscription][Get-AzSubscription]. Définissez le nom du groupe de ressources, le nom du réseau virtuel et la région en reprenant les valeurs utilisées dans les étapes précédentes où vous avez créé les ressources Azure complémentaires :

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Créer la ressource et retourner le contrôle à l’invite PowerShell prend quelques minutes. Le provisionnement du domaine managé Azure AD DS se poursuit en arrière-plan et le déploiement peut prendre jusqu’à une heure. Sur le portail Azure, la page **Vue d’ensemble** de votre domaine managé Azure AD DS indique l’état actuel tout au long de cette phase de déploiement.

Une fois que le portail Azure a indiqué que le provisionnement du domaine managé Azure AD DS était terminé, voici les tâches qu’il convient d’effectuer :

* Mettez à jour les paramètres DNS pour le réseau virtuel afin que les machines virtuelles puissent trouver le domaine géré pour l’authentification ou la jonction de domaine.
    * Pour configure le DNS, sélectionnez votre domaine managé Azure AD DS dans le portail. Dans la fenêtre **Vue d’ensemble**, vous êtes invité à configurer automatiquement ces paramètres DNS.
* Si vous avez créé un domaine managé Azure AD DS dans une région qui prend en charge les Zones de disponibilité, créez un groupe de sécurité réseau pour limiter le trafic dans le réseau virtuel pour ce domaine. Un équilibreur Azure Standard Load Balancer, pour lequel ces règles doivent être en place, est créé. Ce groupe de sécurité réseau, qui sécurise Azure AD DS, est nécessaire pour que le domaine managé fonctionne correctement.
    * Pour créer le groupe de sécurité réseau et les règles requises, sélectionnez votre domaine managé Azure AD DS sur le portail. Dans la fenêtre **Vue d’ensemble**, il vous est demandé de créer automatiquement le groupe de sécurité réseau et de le configurer.
* [Activez la synchronisation de mots de passe avec Azure AD Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) de sorte que les utilisateurs puissent se connecter au domaine managé avec leurs informations d’identification d’entreprise.

## <a name="modify-scoped-synchronization-using-powershell"></a>Modifier la synchronisation délimitée à partir de PowerShell

Pour modifier la liste des groupes dont les utilisateurs doivent être synchronisés avec votre domaine managé Azure AD DS, réexécutez le [script PowerShell](#powershell-script-for-scoped-synchronization) et spécifiez la nouvelle liste de groupes. Dans l’exemple suivant, les groupes à synchroniser n’incluent plus *GroupName2*, mais incluent maintenant *GroupName3*.

> [!WARNING]
> Vous devez inclure le groupe *AAD DC Administrators* dans la liste des groupes configurés pour la synchronisation délimitée. Si vous n’incluez pas ce groupe, le domaine managé Azure AD DS est inutilisable.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

La modification de l’étendue de la synchronisation conduit le domaine managé Azure AD DS à resynchroniser toutes les données. Les objets qui ne sont plus nécessaires dans le domaine managé Azure AD DS sont supprimés, et la resynchronisation peut prendre un certain temps.

## <a name="disable-scoped-synchronization-using-powershell"></a>Désactiver la synchronisation délimitée à partir de PowerShell

Pour désactiver la synchronisation délimitée basée sur les groupes pour un domaine managé Azure AD DS, définissez *"filteredSync" = "Disabled"* sur la ressource Azure AD DS, puis mettez à jour le domaine managé. À l’issue de cette opération, l’ensemble des utilisateurs et des groupes sont définis pour être synchronisés à partir d’Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

La modification de l’étendue de la synchronisation conduit le domaine managé Azure AD DS à resynchroniser toutes les données. Les objets qui ne sont plus nécessaires dans le domaine managé Azure AD DS sont supprimés, et la resynchronisation peut prendre un certain temps.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le processus de synchronisation, consultez [Comprendre la synchronisation dans Azure AD Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
