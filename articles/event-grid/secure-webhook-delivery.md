---
title: Livraison sécurisée de webhooks à l’aide d’Azure AD dans Azure Event Grid
description: Décrit comment livrer des événements aux points de terminaison HTTPS protégés par Azure Active Directory à l’aide d’Azure Event Grid
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 0d92b89b1df6b6969491d39b04764f15b7a510d1
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125802"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publier des événements sur des points de terminaison protégés par Azure Active Directory
Cet article explique comment utiliser Azure Active Directory (Azure AD) pour sécuriser la connexion entre votre **abonnement aux événements** et votre **point de terminaison webhook**. Pour obtenir une vue d’ensemble des applications et des principaux de service Azure AD, consultez [Présentation de la plateforme d’identités Microsoft (v2.0)](../active-directory/develop/v2-overview.md).

Cet article utilise le Portail Azure à des fins de démonstration, mais la fonctionnalité peut également être activée à l’aide de l’interface CLI, de PowerShell ou des Kits de développement logiciel (SDK).

> [!IMPORTANT]
> Une vérification d’accès supplémentaire a été introduite dans le cadre de la création ou de la mise à jour de l’abonnement aux événements le 30 mars 2021 afin de résoudre une faille de sécurité. Le principal de service du client abonné doit être soit propriétaire, soit avoir un rôle attribué sur le principal de service de l’application de destination. Reconfigurez votre application AAD en suivant les nouvelles instructions ci-dessous.


## <a name="create-an-azure-ad-application"></a>Créer une application Azure AD
Inscrivez votre webhook auprès d’Azure AD en créant une application Azure AD pour votre point de terminaison protégé. Consultez [Scénario : API web protégée](../active-directory/develop/scenario-protected-web-api-overview.md). Configurez votre API protégée pour qu’elle soit appelée par une application démon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Activer Event Grid pour utiliser votre application Azure AD
Cette section explique comment activer Event Grid pour utiliser votre application Azure AD. 

> [!NOTE]
> Pour exécuter ce script, vous devez être membre du [rôle d’administrateur de l’application Azure AD](../active-directory/roles/permissions-reference.md#all-roles).

### <a name="connect-to-your-azure-tenant"></a>Se connecter au locataire Azure
Tout d’abord, connectez-vous à votre locataire Azure à l’aide de la commande `Connect-AzureAD`. 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Créer un principal du service Microsoft.EventGrid
Exécutez le script suivant pour créer le principal de service pour **Microsoft.EventGrid** s’il n’existe pas déjà. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else {
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Créer un rôle pour votre application   
Exécutez le script suivant pour créer un rôle pour votre application Azure AD. Dans cet exemple, le nom du rôle est : **AzureEventGridSecureWebhookSubscriber**. Modifiez le `$myTenantId` du script PowerShell pour utiliser votre ID de locataire Azure AD et `$myAzureADApplicationObjectId` avec l’ID d’objet de votre application Azure AD

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
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
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
} else {      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-role-assignment-for-the-client-creating-event-subscription"></a>Créer une attribution de rôle pour le client créant un abonnement aux événements
L’attribution de rôle doit être créée dans l’Azure AD App webhook pour l’application ou l’utilisateur AAD qui crée l’abonnement aux événements. Utilisez l’un des scripts ci-dessous selon qu’une application ou un utilisateur AAD crée l’abonnement aux événements.

> [!IMPORTANT]
> Une vérification d’accès supplémentaire a été introduite dans le cadre de la création ou de la mise à jour de l’abonnement aux événements le 30 mars 2021 afin de résoudre une faille de sécurité. Le principal de service du client abonné doit être soit propriétaire, soit avoir un rôle attribué sur le principal de service de l’application de destination. Reconfigurez votre application AAD en suivant les nouvelles instructions ci-dessous.

#### <a name="create-role-assignment-for-an-event-subscription-aad-app"></a>Créer une attribution de rôle pour une application AAD d’abonnement aux événements 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="create-role-assignment-for-an-event-subscription-aad-user"></a>Créer une attribution de rôle pour un utilisateur AAD d’abonnement aux événements 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="create-role-assignment-for-event-grid-service-principal"></a>Créer une attribution de rôle pour un principal de service Event Grid
Exécutez la commande New-AzureADServiceAppRoleAssignment pour attribuer le principal du service Event Grid au rôle que vous avez créé à l’étape précédente.

```powershell
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Exécutez les commandes suivantes pour générer des informations que vous utiliserez ultérieurement.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Configurer l’abonnement aux événements
Lorsque vous créez un abonnement aux événements, procédez comme suit :

1. Sélectionnez le type de point de terminaison **Webhook**. 
1. Spécifiez l’**URI** du point de terminaison.

    ![Sélectionner un webhook de type de point de terminaison](./media/secure-webhook-delivery/select-webhook.png)
1. Sélectionnez l’onglet **Fonctionnalités supplémentaires** en haut de la page **Créer des abonnements aux événements**.
1. Sous l’onglet **Fonctionnalités supplémentaires**, procédez comme suit :
    1. Sélectionnez **Utiliser l’authentification AAD**, puis configurez l’ID de locataire et l’ID d’application :
    1. Copiez l’ID de locataire Azure AD à partir de la sortie du script, puis entrez-le dans le champ **ID de locataire AAD**.
    1. Copiez l’ID d’application Azure AD à partir de la sortie du script, puis entrez-le dans le champ **ID d’application AAD**. Vous pouvez également utiliser l’URI de l’ID d’application AAD. Pour plus d’informations sur l’URI de l’ID d’application, consultez [cet article](../app-service/configure-authentication-provider-aad.md).

        ![Action de webhook sécurisé](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’information sur la surveillance des remises des événements, consultez [Surveiller la remise des messages Event Grid](monitor-event-delivery.md).
* Pour plus d’informations sur la clé d’authentification, consultez la page [Sécurité et authentification Azure Event Grid](security-authentication.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).