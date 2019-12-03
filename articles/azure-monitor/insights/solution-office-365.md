---
title: Solution de gestion Office 365 dans Azure | Microsoft Docs
description: Cet article donne des informations sur la configuration et l’utilisation de la solution Office 365 dans Azure.  Il inclut une description détaillée des enregistrements d’Office 365 créés dans Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: aff6be1a6abf2550013b752ba4f796ffe255499f
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539048"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Solution de gestion Office 365 dans Azure (préversion)

![Logo Office 365](media/solution-office-365/icon.png)


> [!NOTE]
> Pour installer et configurer la solution Office 365, nous vous recommandons d’activer le [connecteur Office 365](../../sentinel/connect-office-365.md) dans [Azure Sentinel](../../sentinel/overview.md) plutôt que d’utiliser les étapes du présent article. Il s’agit d’une version mise à jour de la solution Office 365, qui offre une expérience de configuration optimisée. Pour connecter les journaux Azure AD, vous pouvez utiliser le [connecteur Azure AD Azure Sentinel](../../sentinel/connect-azure-active-directory.md) ou [configurer les paramètres de diagnostic Azure AD](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), qui fournissent des données de journal plus riches que les journaux de gestion Office 365. 
>
> Lorsque vous [intégrez Azure Sentinel](../../sentinel/quickstart-onboard.md), spécifiez l’espace de travail Log Analytics dans lequel vous souhaitez installer la solution Office 365 Une fois le connecteur activé, la solution est disponible dans l’espace de travail, et vous pouvez l’utiliser exactement comme les autres solutions de surveillance que vous avez installées.
>
> Les utilisateurs du cloud Azure Government doivent installer Office 365 en suivant la procédure de cet article, car Azure Sentinel n’est pas encore disponible sur ce Cloud.

La solution de gestion Office 365 vous permet d’effectuer le monitoring de votre environnement Office 365 dans Azure Monitor.

- Surveillez les activités des utilisateurs dans vos comptes Office 365 pour analyser les modèles d’utilisation ainsi que pour identifier les tendances de comportement. Par exemple, vous pouvez extraire des scénarios d’utilisation spécifiques, tels que les fichiers partagés en dehors de votre organisation ou les sites SharePoint les plus populaires.
- Analysez les activités d’administrateur pour effectuer le suivi des modifications de configuration ou d’opérations de privilèges élevés.
- Détectez et analysez le comportement des utilisateurs indésirables, qui peut être personnalisé pour les besoins de votre organisation.
- Présentation d’audit et de conformité. Par exemple, vous pouvez surveiller les opérations d’accès aux fichiers sur des fichiers confidentiels, ce qui peut vous aider dans le processus d’audit et de conformité.
- Effectuez un dépannage opérationnel à l’aide des [requêtes dans les journaux](../log-query/log-query-overview.md) en haut des données d’activité Office 365 de votre organisation.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Les conditions suivantes sont requises avant l’installation et la configuration de cette solution.

- Abonnement à Office 365 organisationnel.
- Informations d’identification pour un compte d’utilisateur qui est un administrateur global.
- Pour recevoir des données d’audit, vous devez [configurer l’audit](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) dans votre abonnement Office 365.  Notez que [l’audit de boîte aux lettres](https://technet.microsoft.com/library/dn879651.aspx) est configuré séparément.  Vous pouvez toujours installer la solution et collecter d’autres données si l’audit n’est pas configuré.
 

## <a name="management-packs"></a>Packs d’administration

Cette solution n’installe aucun pack d’administration dans les [groupes d’administration connectés](../platform/om-agents.md).
  

## <a name="install-and-configure"></a>Installer et configurer

Commencez par ajouter la [solution Office 365 à votre abonnement](solutions.md#install-a-monitoring-solution). Ensuite, suivez les étapes de configuration de cette section pour lui donner accès à votre abonnement Office 365.

### <a name="required-information"></a>Informations requises

Avant de commencer cette procédure, rassemblez les informations suivantes.

Dans votre espace de travail Log Analytics :

- Nom de l’espace de travail : l’espace de travail dans lequel les données Office 365 seront collectées.
- Nom du groupe de ressources : groupe de ressources qui contient l’espace de travail.
- ID d’abonnement Azure : abonnement qui contient l’espace de travail.

Dans votre abonnement Office 365 :

- Nom d’utilisateur : adresse e-mail d’un compte Administrateur.
- ID de locataire : ID unique de l’abonnement à Office 365.

Les informations suivantes doivent être collectées lors de la création et de la configuration de l’application Office 365 dans Azure Active Directory :

- ID d’application (client) : chaîne de 16 caractères qui représente le client Office 365.
- Clé secrète client : chaîne chiffrée nécessaire à l’authentification.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Créer une application Office 365 dans Azure Active Directory

La première étape consiste à créer une application dans Azure Active Directory qui sera utilisée par la solution de gestion pour accéder à votre solution Office 365.

1. Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com/).
1. Sélectionnez **Azure Active Directory**, puis **Inscription des applications**.
1. Cliquez sur **Nouvelle inscription**.

    ![Ajouter une inscription d’application](media/solution-office-365/add-app-registration.png)
1. Entrer le **nom** d’une application. Sélectionnez **Comptes dans un annuaire d’organisation (tout annuaire Azure AD - Multilocataire)** pour **Types de comptes pris en charge**.
    
    ![Créer une application](media/solution-office-365/create-application.png)
1. Cliquez sur **Inscrire** et vérifiez les informations de l’application.

    ![Application inscrite](media/solution-office-365/registered-app.png)

1. Enregistrez l’ID d’application (client) ainsi que le reste des informations qui ont été collectées avant.


### <a name="configure-application-for-office-365"></a>Configurer l’application pour Office 365

1. Sélectionnez **Authentification** et vérifiez que **Comptes dans un annuaire d’organisation (tout annuaire Azure AD - Multilocataire)** est sélectionné sous **Types de comptes pris en charge**.

    ![Paramètres Mutualisé](media/solution-office-365/settings-multitenant.png)

1. Sélectionnez **Autorisations d’API**, puis **Ajouter une autorisation**.
1. Cliquez sur **API Gestion Office 365**. 

    ![Sélectionner une API](media/solution-office-365/select-api.png)

1. Sous **Quel type d’autorisation votre application nécessite-t-elle ?** , sélectionnez les options suivantes pour **Autorisations d’application** et **Autorisations déléguées** :
   - Lire les informations sur l’intégrité du service pour votre organisation
   - Lire les données d’activité pour votre organisation
   - Lire les rapports d’activité pour votre organisation

     ![Sélectionner une API](media/solution-office-365/select-permissions-01.png)![Sélectionner une API](media/solution-office-365/select-permissions-02.png)

1. Cliquez sur **Ajouter des autorisations**.
1. Cliquez sur **Accorder le consentement administrateur**, puis sur **Oui** en réponse à la demande de vérification.


### <a name="add-a-secret-for-the-application"></a>Ajouter un secret pour l’application

1. Sélectionnez **Certificats et secrets**, puis **Nouveau secret client**.

    ![Clés](media/solution-office-365/secret.png)
 
1. Tapez une **Description** et une **Durée** pour la nouvelle clé.
1. Cliquez sur **Ajouter**, puis enregistrez la **Valeur** qui a été générée comme clé secrète client, ainsi que le reste des informations qui ont été collectées avant.

    ![Clés](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Ajouter le consentement d’administration

Pour activer le compte d’administration pour la première fois, vous devez fournir le consentement d’administration de l’application. Vous pouvez pour cela utiliser un script PowerShell. 

1. Enregistrez le script suivant sous le nom *office365_consent.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Exécutez le script avec la commande suivante. Vous êtes ensuite invité à entrer deux fois vos informations d’identification. Fournissez d’abord les informations d’identification de votre espace de travail Log Analytics, puis les informations d’identification d’administrateur général de votre locataire Office 365.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Exemple :

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Une fenêtre de ce type s’affiche. Cliquez sur **Accepter**.
    
    ![Consentement de l’administrateur](media/solution-office-365/admin-consent.png)

> [!NOTE]
> Vous pouvez être redirigé vers une page qui n’existe pas. Considérez-le comme un succès.

### <a name="subscribe-to-log-analytics-workspace"></a>S’abonner à l’espace de travail Log Analytics

La dernière étape consiste à abonner l’application à votre espace de travail Log Analytics. Vous utiliserez ici aussi un script PowerShell.

1. Enregistrez le script suivant sous le nom *office365_subscription.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }

    Function RESTAPI-Auth { 
    $global:SubscriptionID = $Subscription.Subscription.Id
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()

    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Exécutez le script avec la commande suivante :

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    Exemple :

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Résolution de problèmes

Le message d’erreur suivant peut s’afficher si votre application est déjà abonnée à cet espace de travail ou si ce locataire est abonné à un autre espace de travail.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

L’erreur suivante se produit si les valeurs de paramètre fournies ne sont pas valides.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Désinstaller l’interface

Vous pouvez supprimer la solution de gestion Office 365 suivant le processus décrit dans [Supprimer une solution de gestion](solutions.md#remove-a-monitoring-solution). Néanmoins, cela n’empêchera pas la collecte des données d’Office 365 dans Azure Monitor. Suivez la procédure ci-dessous pour vous désabonner d’Office 365 et arrêter la collecte de données.

1. Enregistrez le script suivant sous le nom *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Exécutez le script avec la commande suivante :

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Exemple :

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Vous êtes invité à entrer des informations d’identification. Fournissez les informations d’identification de votre espace de travail Log Analytics.

## <a name="data-collection"></a>Collecte des données

### <a name="supported-agents"></a>Agents pris en charge

La solution Office 365 ne récupère pas de données des [agents Log Analytics](../platform/agent-data-sources.md).  Il récupère les données directement à partir d’Office 365.

### <a name="collection-frequency"></a>Fréquence de collecte

La collecte initiale des données peut prendre quelques heures. Dès qu’elle a commencé, Office 365 envoie une [notification Webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) avec des données détaillées à Azure Monitor chaque fois qu’un enregistrement est créé. Cet enregistrement est disponible dans Azure Monitor quelques minutes après sa réception.

## <a name="using-the-solution"></a>Utilisation de la solution

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quand vous ajoutez la solution Office 365 à votre espace de travail Log Analytics, la vignette **Office 365** est ajoutée à votre tableau de bord. Cette mosaïque affiche une valeur et une représentation graphique du nombre d’ordinateurs de votre environnement et leur conformité de mise à jour.<br><br>
![Vignette de résumé Office 365](media/solution-office-365/tile.png)  

Cliquez sur la vignette **Office 365** pour ouvrir le tableau de bord **Office 365**.

![Tableau de bord Office 365](media/solution-office-365/dashboard.png)  

Le tableau de bord comprend les colonnes figurant dans le tableau suivant. Chaque colonne répertorie les dix premières alertes (classées par nombre d’alertes) correspondant aux critères de cette colonne pour l’étendue et l’intervalle de temps spécifiés. Vous pouvez exécuter une recherche de journaux qui fournit la liste complète. Pour cela, cliquez sur Afficher tout en bas de la colonne ou cliquez sur l’en-tête de colonne.

| Colonne | Description |
|:--|:--|
| Opérations | Fournit des informations sur les utilisateurs actifs depuis vos abonnements entièrement surveillés Office 365. Vous serez également en mesure de voir le nombre d’activités qui se produisent au fil du temps.
| Microsoft Exchange | Affiche la répartition des activités d’Exchange Server telles que l’autorisation ajouter une boîte aux lettres ou Définir une boîte aux lettres. |
| SharePoint | Affiche les activités supérieures que les utilisateurs exécutent sur les documents SharePoint. Quand vous explorez depuis cette vignette, la page de recherche affiche les détails de ces activités, tels que le document cible et l’emplacement de cette activité. Par exemple, pour un événement Fichier ouvert, vous pouvez afficher le document ouvert, son nom de compte associé et son adresse IP. |
| Azure Active Directory | Inclut les activités utilisateur supérieures, telles que la réinitialisation du mot de passe utilisateur et les tentatives de connexion. Quand vous explorez, vous pouvez afficher les détails de ces activités, telles que l’état du résultat. Ceci est particulièrement utile si vous souhaitez surveiller les activités suspectes sur votre Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Enregistrements de journaux Azure Monitor

Tous les enregistrements créés dans l’espace de travail Log Analytics dans Azure Monitor par la solution Office 365 ont un **Type** de **OfficeActivity**.  La propriété **OfficeWorkload** détermine le service Office 365 auquel fait référence l’enregistrement : Exchange, AzureActiveDirectory, SharePoint ou OneDrive.  La propriété **RecordType** spécifie le type d’opération.  Les propriétés varient pour chaque type d’opération et sont affichées dans les tables ci-dessous.

### <a name="common-properties"></a>Propriétés communes

Les propriétés suivantes sont communes à tous les enregistrements d’Office 365.

| Propriété | Description |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | L’adresse IP du terminal qui a été utilisée lorsque l’activité a été enregistrée. L’adresse IP s’affiche au format d’adresse IPv4 ou IPv6. |
| OfficeWorkload | Service d’Office 365 auquel fait référence l’enregistrement.<br><br>AzureActiveDirectory<br>Microsoft Exchange<br>SharePoint|
| Opération | Le nom de l’activité utilisateur ou administrateur.  |
| OrganizationId | Le GUID pour le client Office 365 de votre organisation. Cette valeur est toujours la même pour votre organisation, quel que soit le service Office 365 dans lequel il se produit. |
| RecordType | Type d’opération effectuée. |
| ResultStatus | Indique si l’action (spécifiée dans la propriété Operation) a réussi ou non. Les valeurs possibles sont Réussie, Partiellement réussie ou Échec. Pour l’activité de l’administrateur Exchange, la valeur est True ou False. |
| UserId | L’UPN (nom d’utilisateur principal) de l’utilisateur qui a exécuté l’action enregistrée dans l’enregistrement ; par exemple, my_name@my_domain_name. Notez que les enregistrements pour l’activité exécutée par les comptes système (tels que SHAREPOINT\system ou NTAUTHORITY\SYSTEM) sont également inclus. | 
| UserKey | Un autre ID pour l’utilisateur identifié dans la propriété UserId.  Par exemple, cette propriété est remplie par l’ID unique du passeport (PUID) pour les événements exécutés par les utilisateurs dans SharePoint, OneDrive entreprise et Exchange. Cette propriété peut également spécifier la même valeur que la propriété UserID pour les événements qui se produisent dans d’autres services et les événements exécutés par des comptes système|
| UserType | Le type d’utilisateur qui a effectué l’opération.<br><br>Admin<br>Application<br>DcAdmin<br>Normal<br>Réservé<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Base Azure Active Directory

Les propriétés suivantes sont communes à tous les enregistrements d’Azure Active Directory.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Le type de client Azure AD. |
| ExtendedProperties | Les propriétés étendues de l’événement d’Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Connexion au compte Azure Active Directory

Ces enregistrements sont créés lorsqu’un utilisateur Active Directory tente de se connecter.

| Propriété | Description |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | L’application qui a déclenché l’événement de connexion du compte, telles que Office 15. |
| `Client` | Détails sur le terminal client, le système d’exploitation du terminal et le navigateur du terminal qui a été utilisé pour l’événement de connexion du compte. |
| `LoginStatus` | Cette propriété est directement issue de OrgIdLogon.LoginStatus. Le mappage de différentes connexions intéressantes peut être effectué par les alertes des algorithmes. |
| `UserDomain` | Les informations d’identité client (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Ces enregistrements sont créés lors de la modification ou des ajouts apportés aux objets Azure Active Directory.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | L’utilisateur sur lequel l’action est exécutée (identifiée par la propriété Opération). |
| Acteur | L'utilisateur ou le principal du service qui a effectué l'action. |
| ActorContextId | Le GUID de l’organisation à laquelle appartient l’acteur. |
| ActorIpAddress | L’adresse IP de l’acteur au format d’adresse IPV4 ou IPV6. |
| InterSystemsId | Le GUID qui effectuent le suivi des actions sur des composants au sein du service Office 365. |
| IntraSystemId |   Le GUID généré par Azure Active Directory pour effectuer le suivi de l’action. |
| SupportTicketId | L’ID du-ticket de support client pour l’action dans les situations de « agir-au-nom-de ». |
| TargetContextId | Le GUID de l’organisation à laquelle appartient l’utilisateur cible. |


### <a name="data-center-security"></a>Sécurité du centre de données

Ces enregistrements sont créés à partir des données d’audit de sécurité du centre de données.  

| Propriété | Description |
|:--- |:--- |
| EffectiveOrganization | Le nom du client vers lequel l’élévation/l’applet de commande est ciblée. |
| ElevationApprovedTime | L’horodatage de l’approbation de l’élévation. |
| ElevationApprover | Le nom d’un gestionnaire Microsoft. |
| ElevationDuration | La durée pendant laquelle l’élévation est active. |
| ElevationRequestId |  Un identifiant unique pour la requête d’élévation. |
| ElevationRole | Le rôle pour lequel l’élévation a été demandé. |
| ElevationTime | L’heure de début de l’élévation. |
| start_time | L’heure de début de l’exécution de l’applet de commande. |


### <a name="exchange-admin"></a>Administrateur Exchange

Ces enregistrements sont créés lorsque des modifications sont apportées à la configuration Exchange.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | Microsoft Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Spécifie si l’applet de commande a été exécutée par un utilisateur dans votre organisation, par le personnel du centre de données Microsoft ou un compte de service du centre de données ou par un administrateur délégué. La valeur False indique que l’applet de commande a été exécuté par une personne dans votre organisation. La valeur True indique que l’applet de commande a été exécutée par le personnel du centre de données, un compte de service du centre de données ou un administrateur délégué. |
| ModifiedObjectResolvedName |  Il s’agit du nom convivial de l’objet qui a été modifié par l’applet de commande. Cette opération est enregistrée uniquement si l’applet de commande modifie l’objet. |
| OrganizationName | Le nom du client. |
| OriginatingServer | Le nom du serveur à partir duquel l’applet de commande a été exécutée. |
| parameters | Le nom et la valeur de tous les paramètres utilisés avec l’applet de commande qui est identifiée dans la propriété Operations. |


### <a name="exchange-mailbox"></a>Boîte aux lettres Exchange

Ces enregistrements sont créés lorsque des modifications ou ajouts sont apportés aux boîtes aux lettres Exchange.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | Microsoft Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informations sur le client de messagerie utilisé pour effectuer l’opération, telles que les informations sur la version du navigateur, la version d’Outlook et l’appareil mobile. |
| Client_IPAddress | L’adresse IP du terminal utilisée lorsque l’activité a été enregistrée. L’adresse IP s’affiche au format d’adresse IPv4 ou IPv6. |
| ClientMachineName | Le nom de l’ordinateur qui héberge le client Outlook. |
| ClientProcessName | Le client de messagerie utilisé pour accéder à la boîte aux lettres. |
| ClientVersion | La version du client de messagerie. |
| InternalLogonType | Réservé à un usage interne. |
| Logon_Type | Indique le type d’utilisateur qui a accédé à la boîte aux lettres et effectué l’opération qui a été enregistrée. |
| LogonUserDisplayName |    Le nom convivial de l’utilisateur qui a effectué l’opération. |
| LogonUserSid | Le SID de l’utilisateur qui a effectué l’opération. |
| MailboxGuid | Le GUID Exchange de la boîte aux lettres ouverte. |
| MailboxOwnerMasterAccountSid | SID de compte principal du compte du propriétaire de boîte aux lettres. |
| MailboxOwnerSid | Le SID du propriétaire de la boîte aux lettres. |
| MailboxOwnerUPN | L’adresse de messagerie de la personne qui possède la boîte ouverte. |


### <a name="exchange-mailbox-audit"></a>Audit de la boîte aux lettres Exchange

Ces enregistrements sont créés lors de la création d’une entrée d’audit de boîte aux lettres.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | Microsoft Exchange |
| RecordType     | ExchangeItem |
| Item | Représente l’élément sur lequel l’opération a été effectuée | 
| SendAsUserMailboxGuid | Le GUID Exchange de la boîte aux lettres ouverte pour envoyer un e-mail. |
| SendAsUserSmtp | Adresse SMTP de l’utilisateur dont le nom a été emprunté. |
| SendonBehalfOfUserMailboxGuid | Le GUID Exchange de la boîte aux lettres ouverte pour envoyer un e-mail au nom de. |
| SendOnBehalfOfUserSmtp | Adresse SMTP de l’utilisateur au nom duquel le courrier électronique est envoyé. |


### <a name="exchange-mailbox-audit-group"></a>Groupe d’audit de la boîte aux lettres Exchange

Ces enregistrements sont créés lorsque des modifications ou des ajouts sont apportés aux groupes Exchange.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | Microsoft Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informations sur chaque élément dans le groupe. |
| CrossMailboxOperations | Indique si l’opération a impliqué plusieurs boîtes aux lettres. |
| DestMailboxId | Définie uniquement si le paramètre CrossMailboxOperations a la valeur True. Spécifie le GUID de la boîte aux lettres cible. |
| DestMailboxOwnerMasterAccountSid | Définie uniquement si le paramètre CrossMailboxOperations a la valeur True. Spécifie le SID pour le SID du compte principal du propriétaire de la boîte aux lettres cible. |
| DestMailboxOwnerSid | Définie uniquement si le paramètre CrossMailboxOperations a la valeur True. Spécifie le SID de la boîte aux lettres cible. |
| DestMailboxOwnerUPN | Définie uniquement si le paramètre CrossMailboxOperations a la valeur True. Spécifie le nom UPN du propriétaire de la boîte aux lettres cible. |
| DestFolder | Le dossier de destination, pour les opérations telles que Déplacer. |
| Dossier | Le dossier où se trouve un groupe d’éléments. |
| Dossiers |     Plus d’informations sur les dossiers sources impliqués dans une opération ; par exemple, si les dossiers sont sélectionnés, puis supprimés. |


### <a name="sharepoint-base"></a>SharePoint Base

Ces propriétés sont communes à tous les enregistrements SharePoint.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifie qu’un événement s’est produit dans SharePoint. Les valeurs possibles sont ObjectModel ou SharePoint. |
| itemType | Le type d’objet qui a été ouvert ou modifié. Consultez la table ItemType pour plus d’informations sur les types d’objets. |
| MachineDomainInfo | Informations sur les opérations de synchronisation de terminal. Ces informations sont enregistrées uniquement si elles sont présente dans la requête. |
| MachineId |   Informations sur les opérations de synchronisation de terminal. Ces informations sont enregistrées uniquement si elles sont présente dans la requête. |
| Site_ | Le GUID du site où se trouve le fichier ou le dossier ouvert par l’utilisateur. |
| Source_Name | L’entité qui a déclenché l’opération d’audit. Les valeurs possibles sont ObjectModel ou SharePoint. |
| UserAgent | Informations sur le client ou le navigateur de l’utilisateur. Ces informations sont fournies par le client ou le navigateur. |


### <a name="sharepoint-schema"></a>Schéma de SharePoint

Ces enregistrements sont créés lorsque des modifications de configuration sont apportées à SharePoint.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Chaîne facultative pour les événements personnalisés. |
| Event_Data |  Charge facultative pour les événements personnalisés. |
| ModifiedProperties | La propriété est incluse pour les événements d’administrateur, tels que l’ajout d’un utilisateur en tant que membre d’un site ou un groupe administrateur de collection de sites. La propriété inclut le nom de la propriété modifiée (par exemple, le groupe d’administrateurs de site), la nouvelle valeur de la propriété modifiée (l’utilisateur ajouté en tant qu’un administrateur de site) et la valeur précédente de l’objet modifié. |


### <a name="sharepoint-file-operations"></a>Opérations sur les fichiers SharePoint

Ces enregistrements sont créés en réponse aux opérations de fichiers dans SharePoint.

| Propriété | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | L’extension de fichier d’un fichier qui est copié ou déplacé. Cette propriété s’affiche uniquement pour les événements FileCopied et FileMoved. |
| DestinationFileName | Le nom du fichier qui est copié ou déplacé. Cette propriété s’affiche uniquement pour les événements FileCopied et FileMoved. |
| DestinationRelativeUrl | L’URL du dossier de destination dans lequel un fichier est copié ou déplacé. La combinaison des valeurs pour les paramètres SiteURL, DestinationRelativeURL et DestinationFileName est identique à la valeur de la propriété ObjectID, qui est le nom de chemin d’accès complet du fichier qui a été copié. Cette propriété s’affiche uniquement pour les événements FileCopied et FileMoved. |
| SharingType | Le type d’autorisations de partage attribuées à l’utilisateur avec lequel la ressource a été partagée. Cet utilisateur est identifié par le paramètre UserSharedWith. |
| Site_Url | L’URL du site où se trouve le fichier ou le dossier ouvert par l’utilisateur. |
| SourceFileExtension | L’extension de fichier du fichier qui est ouvert par l’utilisateur. Cette propriété est vide si l’objet a été ouvert est un dossier. |
| SourceFileName |  Le nom du fichier ou du dossier ouvert par l’utilisateur. |
| SourceRelativeUrl | L’URL du dossier qui contient le fichier ouvert par l’utilisateur. La combinaison des valeurs pour les paramètres SiteURL, DestinationRelativeURL et DestinationFileName est identique à la valeur de la propriété ObjectID, qui est le nom de chemin d’accès complet du fichier ouvert par l’utilisateur. |
| UserSharedWith |  L’utilisateur avec lequel une ressource a été partagée. |




## <a name="sample-log-searches"></a>Exemples de recherches dans les journaux

Le tableau suivant fournit des exemples de recherches de journaux pour les enregistrements de mises à jour collectés par cette solution.

| Requête | Description |
| --- | --- |
|Nombre de toutes les opérations sur votre abonnement Office 365 |OfficeActivity &#124; summarize count() by Operation |
|Utilisation des sites SharePoint|OfficeActivity &#124; où OfficeWorkload =~ "sharepoint" &#124; summarize count() by SiteUrl \| sort by Count asc|
|Opérations d’accès de fichier par type d’utilisateur|search in (OfficeActivity) OfficeWorkload =~ "azureactivedirectory" and "MyTest"|
|Recherche avec un mot clé spécifique|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Analyser des actions externes sur Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Étapes suivantes

* Utilisez les [requêtes de journaux dans Azure Monitor](../log-query/log-query-overview.md) pour afficher des données détaillées sur les mises à jour.
* [Créez vos propres tableaux de bord](../learn/tutorial-logs-dashboards.md) pour afficher vos requêtes de recherche favoris Office 365.
* [Créez des alertes](../platform/alerts-overview.md) pour être informé de façon proactive des activités importantes Office 365.  
