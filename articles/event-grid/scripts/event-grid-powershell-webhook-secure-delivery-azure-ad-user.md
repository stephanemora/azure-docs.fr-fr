---
title: 'Azure PowerShell : remise WebHook sécurisée avec Azure AD User dans Azure Event Grid'
description: Explique comment remettre des événements aux points de terminaison HTTPS protégés par Azure AD User à l’aide d’Azure Event Grid
ms.devlang: powershell
ms.topic: sample
ms.date: 09/29/2021
ms.openlocfilehash: 91cdc4f4959d417e8f68dc9712737d234248e4bd
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063694"
---
# <a name="secure-webhook-delivery-with-azure-ad-user-in-azure-event-grid"></a>Livraison sécurisée de webhooks avec Azure AD User dans Azure Event Grid

Ce script fournit la configuration qui permet de remettre des événements aux points de terminaison HTTPS protégés par Azure AD User à l’aide d’Azure Event Grid.

Voici les étapes générales du script :

1. Créer un principal de service pour **Microsoft.EventGrid** s’il n’en existe pas encore
1. Créer un rôle nommé **AzureEventGridSecureWebhookSubscriber** dans l’**application Azure AD pour votre webhook**
1. Ajouter le principal de service de l’utilisateur qui créera l’abonnement au rôle AzureEventGridSecureWebhookSubscriber
1. Ajouter le principal de service de Microsoft.EventGrid au AzureEventGridSecureWebhookSubscriber

## <a name="sample-script---stable"></a>Exemple de script : stable

```azurepowershell
# NOTE: Before run this script ensure you are logged in Azure by using "az login" command.

$webhookAppObjectId = "[REPLACE_WITH_YOUR_ID]"
$eventSubscriptionWriterUserPrincipalName = "[REPLACE_WITH_USER_PRINCIPAL_NAME_OF_THE_USER_WHO_WILL_CREATE_THE_SUBSCRIPTION]"

# Start execution
try {

    # Creates an application role of given name and description

    Function CreateAppRole([string] $Name, [string] $Description)
    {
        $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
        $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
        $appRole.AllowedMemberTypes.Add("Application");
        $appRole.AllowedMemberTypes.Add("User");
        $appRole.DisplayName = $Name
        $appRole.Id = New-Guid
        $appRole.IsEnabled = $true
        $appRole.Description = $Description
        $appRole.Value = $Name;

        return $appRole
    }

    # Creates Azure Event Grid Azure AD Application if not exists

    $eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7" # You don't need to modify this id
    $eventGridRoleName = "AzureEventGridSecureWebhookSubscriber" # You don't need to modify this role name
    $eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
    if ($eventGridSP -match "Microsoft.EventGrid")
    {
        Write-Host "The Azure AD Application is already defined.`n"
    } else {
        Write-Host "Creating the Azure Event Grid Azure AD Application"
        $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
    }

    # Creates the Azure app role for the webhook Azure AD application

    $app = Get-AzureADApplication -ObjectId $webhookAppObjectId
    $appRoles = $app.AppRoles

    Write-Host "Azure AD App roles before addition of the new role..."
    Write-Host $appRoles
    
    if ($appRoles -match $eventGridRoleName)
    {
        Write-Host "The Azure Event Grid role is already defined.`n"
    } else {      
        Write-Host "Creating the Azure Event Grid role in Azure AD Application: " $webhookAppObjectId
        $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
        $appRoles.Add($newRole)
        Set-AzureADApplication -ObjectId $app.ObjectId -AppRoles $appRoles
    }

    Write-Host "Azure AD App roles after addition of the new role..."
    Write-Host $appRoles

    # Creates the user role assignment for the user who will create event subscription

    $servicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $app.AppId + "'")

    try
    {
        Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
        $eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
        $eventGridAppRole = $app.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
        New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $servicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId        
    }
    catch
    {
        if( $_.Exception.Message -like '*Permission being assigned already exists on the object*')
        {
            Write-Host "The Azure AD User Application role is already defined.`n"
        }
        else
        {
            Write-Error $_.Exception.Message
        }
        Break
    }

    # Creates the service app role assignment for Event Grid Azure AD Application

    $eventGridAppRole = $app.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
    New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $servicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
    # Print output references for backup

    Write-Host ">> Webhook's Azure AD Application Id: $($app.AppId)"
    Write-Host ">> Webhook's Azure AD Application ObjectId Id: $($app.ObjectId)"
}
catch {
  Write-Host ">> Exception:"
  Write-Host $_
  Write-Host ">> StackTrace:"  
  Write-Host $_.ScriptStackTrace
}
```

## <a name="script-explanation"></a>Explication du script

Pour plus d’informations, consultez [Remise WebHook sécurisée à l’aide d’Azure AD dans Azure Event Grid](../secure-webhook-delivery.md).
