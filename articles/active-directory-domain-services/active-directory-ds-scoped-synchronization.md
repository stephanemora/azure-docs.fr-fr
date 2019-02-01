---
title: 'Azure Active Directory Domain Services : Synchronisation étendue | Microsoft Docs'
description: Configurer la synchronisation étendue depuis Azure AD vers vos domaines managés
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: e3d13082e3c076061b8d343827266ec04ae80646
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180685"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Configurer la synchronisation étendue depuis Azure AD vers votre domaine managé
Cet article vous montre comment configurer uniquement des comptes utilisateurs spécifiques à synchroniser depuis votre annuaire Azure AD vers votre domaine managé Azure AD Domain Services.


## <a name="group-based-scoped-synchronization"></a>Synchronisation étendue basée sur les groupes
Par défaut, tous les utilisateurs et groupes dans votre annuaire Azure AD sont synchronisés avec votre domaine managé. Si seuls quelques utilisateurs utilisent le domaine managé, vous pouvez synchroniser uniquement ces comptes. C’est ce que vous permet de faire la synchronisation étendue basée sur le groupe. Après configuration, seuls les comptes utilisateurs appartenant aux groupes que vous avez spécifiés sont synchronisées au domaine managé.

Le tableau suivant vous permet de déterminer comment utiliser la synchronisation étendue :

| **État actuel** | **État souhaité** | **Configuration requise** |
| --- | --- | --- |
| Votre domaine managé existant est configuré pour synchroniser tous les comptes et groupes utilisateurs. | Vous souhaitez synchroniser uniquement les comptes utilisateur appartenant à des groupes spécifiques de votre domaine managé. | [Supprimez le domaine managé existant](active-directory-ds-disable-aadds.md). Ensuite, suivez les instructions de cet article pour le recréer avec la synchronisation étendue configurée. |
| Vous ne disposez pas d’un domaine managé existant. | Vous souhaitez créer un nouveau domaine managé et synchroniser uniquement les comptes utilisateur appartenant à des groupes spécifiques. | Suivez les instructions de cet article pour créer un domaine managé avec la synchronisation étendue configurée. |
| Votre domaine managé existant est configuré pour synchroniser uniquement quelques comptes appartenant à des groupes spécifiques. | Vous souhaitez modifier la liste des groupes dont les utilisateurs doivent être synchronisés au domaine de gestion. | Suivez les instructions de cet article pour modifier la synchronisation étendue. |

> [!WARNING]
> **Modifier l’étendue de la synchronisation entraîne la resynchronisation de votre domaine managé.**
>
 * Lorsque vous modifiez l’étendue de synchronisation d’un domaine managé, une resynchronisation complète est déclenchée.
 * Les objets qui ne sont plus requis dans le domaine managé sont supprimés. De nouveaux objets sont créés dans le domaine managé.
 * La resynchronisation peut prendre un certain temps, en fonction du nombre d’objets (utilisateurs, groupes et appartenances aux groupes) dans votre domaine managé et votre annuaire Azure AD. Pour les annuaires volumineux avec plusieurs centaines de milliers d’objets, la resynchronisation peut prendre quelques jours.
>
>


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Créer un domaine managé et activer la synchronisation étendue basée sur les groupes avec le portail Azure

1. Suivez le guide de [Prise en main](active-directory-ds-getting-started.md) pour créer un domaine géré.
2. Choisissez **étendue** lors de la sélection de style de synchronisation dans l’Assistant de création dans Azure AD Domain Services.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Créer un domaine managé et activer la synchronisation étendue basée sur les groupes avec PowerShell
Utilisez PowerShell pour cette procédure. Consultez les instructions pour [activer Azure Active Directory Domain Services à l’aide de PowerShell](active-directory-ds-enable-using-powershell.md). Certaines étapes de cet article sont légèrement modifiées pour configurer la synchronisation étendue.

Procédez comme suit pour configurer la synchronisation étendue basée sur les groupes pour votre domaine managé :

1. Effectuez les tâches suivantes :
  * [Tâche 1 : Installer les modules PowerShell nécessaires](active-directory-ds-enable-using-powershell.md#task-1-install-the-required-powershell-modules).
  * [Tâche 2 : Créer le principal du service requis dans votre annuaire Azure AD](active-directory-ds-enable-using-powershell.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory).
  * [Tâche 3 : Créer et configurer le groupe « AAD DC Administrators »](active-directory-ds-enable-using-powershell.md#task-3-create-and-configure-the-aad-dc-administrators-group).
  * [Tâche 4 : Inscrire le fournisseur de ressources Azure AD Domain Services](active-directory-ds-enable-using-powershell.md#task-4-register-the-azure-ad-domain-services-resource-provider).
  * [Tâche 5 : Créer un groupe de ressources](active-directory-ds-enable-using-powershell.md#task-5-create-a-resource-group).
  * [Tâche 6 : Créer et configurer le réseau virtuel](active-directory-ds-enable-using-powershell.md#task-6-create-and-configure-the-virtual-network).

2. Sélectionnez les groupes que vous souhaitez synchroniser et fournissez le nom d’affichage des groupes que vous souhaitez synchroniser à votre domaine managé.

3. Enregistrez le [script de la section suivante](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) dans un fichier appelé ```Select-GroupsToSync.ps1```. Exécuter le script comme suit :

  ```powershell
  .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
  ```

  > [!WARNING]
  > **N’oubliez pas d’inclure le groupe « AAD DC Administrators ».**
  >
  > Vous devez inclure le groupe « AAD DC Administrators » dans la liste des groupes configurés pour la synchronisation étendue. Si vous n’incluez pas de ce groupe, le domaine managé sera inutilisable.
  >

4. Créez ensuite le domaine managé et activez la synchronisation étendue basée sur les groupes pour le domaine managé. Incluez la propriété ```"filteredSync" = "Enabled"``` dans le paramètre ```Properties```. Par exemple, consultez le fragment de script suivant, copié à partir de [Tâche 7 : Provisionner un domaine managé par Azure AD Domain Services](active-directory-ds-enable-using-powershell.md#task-7-provision-the-azure-ad-domain-services-managed-domain).

  ```powershell
  $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
  $ManagedDomainName = "contoso100.com"
  $ResourceGroupName = "ContosoAaddsRg"
  $VnetName = "DomainServicesVNet_WUS"
  $AzureLocation = "westus"

  # Enable Azure AD Domain Services for the directory.
  New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
  ```

  > [!TIP]
  > N’oubliez pas d’inclure ```"filteredSync" = "Enabled"``` dans le paramètre ```-Properties```, afin d’activer la synchronisation étendue pour le domaine managé.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Script pour sélectionner les groupes à synchroniser avec le domaine managé (Select-GroupsToSync.ps1)
Enregistrez le script suivant dans un fichier (```Select-GroupsToSync.ps1```). Ce script configure Azure AD Domain Services de façon à synchroniser les groupes sélectionnés au domaine managé. Tous les comptes utilisateurs appartenant aux groupes spécifiés sont synchronisées au domaine managé.

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


## <a name="modify-group-based-scoped-synchronization"></a>Modifier la synchronisation étendue basée sur les groupes
Pour modifier la liste des groupes dont les utilisateurs doivent être synchronisés avec votre domaine managé, exécutez de nouveau le [script PowerShell](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) et spécifiez la nouvelle liste de groupes. N’oubliez pas de toujours spécifier le groupe « AAD DC Administrators » dans cette liste.

> [!WARNING]
> **N’oubliez pas d’inclure le groupe « AAD DC Administrators ».**
>
> Vous devez inclure le groupe « AAD DC Administrators » dans la liste des groupes configurés pour la synchronisation étendue. Si vous n’incluez pas de ce groupe, le domaine managé sera inutilisable.
>


## <a name="disable-group-based-scoped-synchronization"></a>Désactiver la synchronisation étendue basée sur les groupes
Utilisez le script PowerShell suivant pour désactiver la synchronisation étendue basée sur les groupes pour votre domaine géré :

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Étapes suivantes
* [Comprendre la synchronisation pour les services de domaine Azure AD](active-directory-ds-synchronization.md)
* [Activer Azure Active Directory Domain Services à l’aide de PowerShell](active-directory-ds-enable-using-powershell.md)
