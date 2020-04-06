---
title: Gérer les comptes d’identification Azure Automation
description: Cet article décrit comment gérer vos comptes d’identification avec PowerShell ou à partir du portail.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 8d7d0baacd5f702e8f435ab440eaf0338a60f4cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500775"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gérer les comptes d’identification Azure Automation

Les comptes d’identification dans Azure Automation fournissent une authentification permettant de gérer des ressources dans Azure au moyen des applets de commande Azure. Quand vous créez un compte d’identification, un nouvel utilisateur du principal du service dans Azure Active Directory (AD) est créé, auquel est attribué le rôle de contributeur au niveau de l’abonnement. Pour les runbooks qui utilisent des Runbooks Workers hybrides sur des machines virtuelles Azure, vous pouvez utiliser [des identités managées pour les ressources Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) plutôt que des comptes d’identification pour authentifier vos ressources Azure.

Le principal du service d’un compte d’identification ne dispose pas d’autorisations pour lire Azure AD par défaut. Si vous souhaitez ajouter des autorisations pour lire ou gérer Azure AD, vous devrez accorder ces autorisations sur le principal du service, sous **Autorisations des API**. Pour en savoir plus, consultez [Ajouter des autorisations pour accéder aux API web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="types-of-run-as-accounts"></a>Types de comptes d’identification

Azure Automation utilise deux types de comptes d’identification :

* Compte d’identification Azure
* Compte d’identification Azure Classic

>[!NOTE]
>Les abonnements Fournisseur de solutions Azure Cloud (CSP) prennent uniquement en charge le modèle Azure Resource Manager. Les services non-Azure Resource Manager ne sont pas disponibles dans le programme. Lorsque vous utilisez un abonnement CSP, le compte d’identification Azure Classic n’est pas créé, c’est le compte d’identification Azure qui l’est. Pour en savoir plus sur les abonnements CSP, consultez [Services disponibles dans les abonnements CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>compte d'identification

Le compte d’identification gère les ressources du [modèle de déploiement Resource Manager](../azure-resource-manager/management/deployment-models.md). Il effectue les tâches suivantes.

* Crée une application Azure AD avec un certificat auto-signé, crée un compte de principal de service pour cette application dans Azure AD et affecte le rôle Collaborateur pour le compte dans votre abonnement actuel. Vous pouvez remplacer le paramètre de certificat par un rôle de propriétaire ou tout autre rôle. Pour plus d’informations, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).
  
* Crée une ressource de certificat Automation nommée `AzureRunAsCertificate` dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat que l’application Azure AD utilise.
  
* Crée une ressource de connexion Automation nommée `AzureRunAsConnection` dans le compte Automation spécifié. La ressource de connexion contient l'ID de l'application, l'ID du locataire, l'ID de l'abonnement et l'empreinte du certificat.

### <a name="azure-classic-run-as-account"></a>Compte d’identification Azure Classic

Le compte d’identification Azure Classic gère les ressources du [modèle de déploiement Classic](../azure-resource-manager/management/deployment-models.md). Vous devez être coadministrateur sur l’abonnement pour créer ou renouveler ce type de compte.

Le compte d’identification Azure Classic effectue les tâches suivantes.

  * Crée un certificat de gestion dans l’abonnement.

  * Crée une ressource de certificat Automation nommée `AzureClassicRunAsCertificate` dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat utilisée par le certificat de gestion.

  * Crée une ressource de connexion Automation nommée `AzureClassicRunAsConnection` dans le compte Automation spécifié. La ressource de connexion conserve le nom de l’abonnement, l’ID d’abonnement et le nom de la ressource de certificat.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Autorisations de compte d’identification

Cette section définit les autorisations pour les comptes d’identification standard et les comptes d’identification Classic.

### <a name="permissions-to-configure-run-as-accounts"></a>Autorisations pour configurer des comptes d’identification

Pour créer ou mettre à jour un compte d’identification, vous devez disposer d’autorisations et de privilèges spécifiques. Un administrateur d’application dans Azure Active Directory et un propriétaire dans un abonnement peuvent effectuer toutes les tâches. Dans une situation où les responsabilités sont partagées, le tableau suivant donne une liste des tâches, avec les applets de commande équivalentes et les autorisations nécessaires :

|Tâche|Applet de commande  |Autorisations minimales  |Où vous définissez les autorisations|
|---|---------|---------|---|
|Créer une application Azure AD|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | Rôle de développeur d’applications<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Accueil > Azure AD > Inscriptions d’applications |
|Ajouter des informations d’identification à l’application|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | Administrateur d’application ou administrateur général<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Accueil > Azure AD > Inscriptions d’applications|
|Créer et obtenir un principal du service Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | Administrateur d’application ou administrateur général<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Accueil > Azure AD > Inscriptions d’applications|
|Attribuer ou obtenir le rôle RBAC pour le principal spécifié|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | Administrateur ou propriétaire de l’accès utilisateur, ou détenteur des autorisations suivantes :</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Accueil > Abonnements > \<nom_abonnement\> - Contrôle d’accès (IAM)|
|Créer ou supprimer un certificat Automation|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | Contributeur sur le groupe de ressources         |Groupe de ressources du compte Automation|
|Créer ou supprimer une connexion Automation|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|Contributeur sur le groupe de ressources |Groupe de ressources du compte Automation|

<sup>1</sup> Les utilisateurs non-administrateurs dans votre locataire Azure AD peuvent [inscrire des applications AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) si l’option **Les utilisateurs peuvent inscrire des applications** du locataire Azure AD dans la page des Paramètres utilisateur a la valeur **Oui**. Si le paramètre d’inscription des applications est **Non**, l’utilisateur qui effectue cette action doit être tel que défini dans ce tableau.

Si vous n’êtes pas membre de l’instance Active Directory de l’abonnement avant d’être ajouté au rôle Administrateur général de l’abonnement, vous êtes ajouté en tant qu’invité. Dans ce cas, vous recevez un avertissement `You do not have permissions to create…` dans la page Ajouter un compte Automation. 

Si vous êtes membre de l’instance Active Directory de l’abonnement lorsque le rôle Administrateur général est attribué, vous pouvez également recevoir un avertissement `You do not have permissions to create…` dans la page Ajouter un compte Automation. Dans ce cas, vous pouvez demander la suppression à partir de l’instance Active Directory de l’abonnement, puis demander à être ajouté de nouveau, afin de devenir un utilisateur à part entière dans Active Directory.

Pour vérifier que la situation produisant le message d’erreur a été corrigée :

1. Dans le volet Azure Active Directory du portail Azure, sélectionnez **Utilisateurs et groupes**. 
2. Sélectionnez **Tous les utilisateurs**.
3. Choisissez votre nom, puis sélectionnez **Profil**. 
4. Vérifiez que la valeur de l’attribut **Usertype** sous votre profil d’utilisateur n’est pas définie sur **Invité**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Autorisations pour configurer des comptes d’identification Classic

Pour configurer ou renouveler des comptes d’identification Classic, vous devez disposer du rôle Coadministrateur au niveau de l’abonnement. Pour en savoir plus sur les autorisations d’abonnement Classic, consultez [Administrateurs d’abonnement Azure Classic](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Création d’un compte d’identification dans le portail Azure

Effectuez les étapes suivantes pour mettre à jour votre compte Azure Automation dans le portail Azure. Créez individuellement les comptes d’identification standard et les comptes d’identification Classic. Si vous n’avez pas besoin de gérer des ressources classiques, vous pouvez simplement créer le compte d’identification Azure.

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
2. Recherchez et sélectionnez **Comptes Automation**.
3. Dans la page Comptes Automation, sélectionnez votre compte Automation dans la liste.
4. Dans le volet gauche, sélectionnez **Comptes d’identification** dans la section des paramètres de compte.
5. Selon le compte dont vous avez besoin, sélectionnez **Compte d’identification Azure** ou **Compte d’identification Azure Classic**. 
6. En fonction du compte qui vous intéresse, utilisez le volet **Ajouter un compte d’identification Azure** ou **Ajouter un compte d’identification Azure Classic**. Après avoir passé en revue les informations générales, cliquez sur **Créer**.
6. Pour suivre la progression de la création du compte d’identification, accédez à l’onglet **Notifications** du menu. Une bannière s’affiche également indiquant que le compte est en cours de création. L’exécution de ce processus peut prendre plusieurs minutes.

## <a name="creating-a-run-as-account-using-powershell"></a>Création d’un compte d’identification avec PowerShell

La liste suivante indique la configuration nécessaire permettant de créer un compte d’identification dans PowerShell. Ces conditions requises s’appliquent aux deux types de comptes d’identification.

* Windows 10 ou Windows Server 2016 avec les modules Azure Resource Manager 3.4.1 et ultérieur. Le script PowerShell ne prend pas en charge les versions antérieures de Windows.
* Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations sur la version PowerShell 1.0, voir [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Un compte Automation, qui est référencé comme valeur pour les paramètres `AutomationAccountName` et `ApplicationDisplayName`.
* Les autorisations équivalentes à celles qui sont listées dans [Autorisations nécessaires pour configurer des comptes d’identification](#permissions).

Pour obtenir les valeurs de `SubscriptionId` et de `ResourceGroupName`, qui sont des paramètres obligatoires pour le script PowerShell, effectuez les étapes suivantes.

1. Dans le portail Azure, sélectionnez **Comptes Automation**.
1. Dans la page Comptes Automation, sélectionnez votre compte Automation.
1. Dans la section des paramètres de compte, sélectionnez **Propriétés**.
1. Notez les valeurs de **NOM**, **ID D’ABONNEMENT** et **GROUPE DE RESOURCES** dans la page des Propriétés. Ces valeurs correspondent respectivement aux valeurs des paramètres du script PowerShell `AutomationAccountName`, `SubscriptionId` et `ResourceGroupName`.

   ![Page des propriétés du compte Automation](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script PowerShell pour créer un compte d’identification

Cette section fournit un script PowerShell pour créer un compte d’identification. Le script comprend la prise en charge de plusieurs configurations.

* Créez un compte d’identification à l’aide d’un certificat auto-signé.
* Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé.
* Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat émis par votre AC d’entreprise.
* Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé dans le cloud Azure Government.

Le script utilise plusieurs applets de commande Azure Resource Manager pour créer des ressources. Pour les applets de commande et les autorisations qu’elles nécessitent, consultez [Autorisations pour configurer des comptes d’identification](#permissions-to-configure-run-as-accounts).

Enregistrez le script sur votre ordinateur en utilisant le nom de fichier **New-RunAsAccount.ps1**.

```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRm.Profile
    Import-Module AzureRm.Resources

    $AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
    if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzAccount -Environment $EnvironmentName
    $Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
```

>[!NOTE]
>`Add-AzAccount` et `Add-AzureRMAccount` sont des alias pour [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Vous pouvez utiliser ces cmdlets ou [mettre à jour vos modules](automation-update-azure-modules.md) dans votre compte Automation vers les dernières versions. Il est possible que vous deviez mettre à jour vos modules, même si vous venez de créer un compte Automation.

### <a name="execute-the-powershell-script"></a>Exécuter le script PowerShell

1. Sur votre ordinateur, démarrez **Windows PowerShell** dans l’écran **Démarrer** avec des droits de l’utilisateur élevés.
1. À partir de l’interface de ligne de commande avec élévation de privilèges, accédez au dossier contenant votre script.
1. Exécutez le script en utilisant les valeurs des paramètres pour la configuration dont vous avez besoin.
1. Si vous créez un compte d’identification Classic, après l’exécution du script, chargez le certificat public (extension de nom de fichier **.cer**) dans le magasin de gestion de l’abonnement dans lequel le compte Automation a été créé.

Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Connectez-vous avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Créer un compte d’identification à l’aide d’un certificat auto-signé

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat d’entreprise

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Si vous avez créé un compte d’identification Classic avec un certificat public d’entreprise (fichier **.cer**), utilisez ce certificat. Consultez [Chargement d’un certificat d’API de gestion dans le portail Azure](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé dans le cloud Azure Government

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Si vous avez créé un compte d’identification Classic avec un certificat public auto-signé (fichier **.cer**), le script le crée et l’enregistre dans le dossier des fichiers temporaires sur votre ordinateur. Il se trouve dans le profil utilisateur `%USERPROFILE%\AppData\Local\Temp` que vous avez utilisé pour exécuter la session PowerShell.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Suppression d’un compte d’identification standard ou Classic

Cette section décrit comment supprimer un compte d’identification standard ou Classic. Lorsque vous effectuez cette opération, le compte Automation est conservé. Après avoir supprimé le compte, vous pouvez le recréer dans le portail Azure.

1. Dans le portail Azure, ouvrez le compte Automation.

2. Dans le volet gauche, sélectionnez **Comptes d’identification** dans la section des paramètres de compte.

3. Dans la page de propriétés Comptes d’identification, sélectionnez le compte d’identification standard ou le compte d’identification Classic que vous voulez supprimer. 

4. Dans le volet Propriétés, pour le compte sélectionné, cliquez sur **Supprimer**.

   ![Supprimer un compte d’identification](media/manage-runas-account/automation-account-delete-runas.png)

5. Pour suivre la progression de la suppression du compte, accédez à l’onglet **Notifications** du menu.

6. Une fois le compte supprimé, vous pouvez le recréer dans la page de propriétés Comptes d’identification en sélectionnant l’option de création **Compte d’identification Azure**.

   ![Recréer le compte d’identification Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Renouvellement d’un certificat auto-signé

Le certificat auto-signé que vous avez créé pour le compte d’identification expire au bout d’un an après la date de création. Avant que votre compte d’identification n’expire, vous devez renouveler le certificat. Vous pouvez le renouveler à tout moment avant qu’il n’expire. 

Lorsque vous renouvelez le certificat auto-signé, le certificat valide en cours est conservé afin de garantir que les Runbooks en file d’attente ou en cours d’exécution, qui s’authentifient avec le compte d’identification, ne sont pas affectés. Le certificat reste valide jusqu’à sa date d’expiration.

>[!NOTE]
>Si vous pensez que le compte d’identification a été compromis, vous pouvez supprimer et recréer le certificat auto-signé.

>[!NOTE]
>Si vous avez configuré votre compte d’identification pour utiliser un certificat émis par votre autorité de certification d’entreprise, et que vous utilisez l’option permettant de renouveler un certificat auto-signé, le certificat d’entreprise est remplacé par un certificat auto-signé.

Effectuez les étapes suivantes pour renouveler le certificat auto-signé.

1. Dans le portail Azure, ouvrez le compte Automation.

1. Sélectionnez **Comptes d’identification** dans la section des paramètres de compte.

    ![Panneau des propriétés du compte Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Dans la page de propriétés des Comptes d’identification, sélectionnez le compte d’identification standard ou le compte d’identification Classic pour lequel renouveler le certificat.

1. Dans le volet Propriétés, pour le compte sélectionné, cliquez sur **Renouveler le certificat**.

    ![Renouveler le certificat pour le compte d’identification](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Pour suivre la progression du renouvellement du certificat, accédez à l’onglet **Notifications** du menu.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Configuration du renouvellement automatique de certificat à l’aide d’un runbook Automation

Pour renouveler automatiquement les certificats, vous pouvez utiliser un runbook Automation. Ce script sur [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) active cette fonctionnalité dans votre compte Automation.

>[!NOTE]
>Pour exécuter le script, vous devez être Administrateur général ou Administrateur d’entreprise dans Azure AD.

Ce script crée une planification hebdomadaire pour renouveler les certificats de compte d’identification. Il ajoute un runbook **Update-AutomationRunAsCredential** à votre compte Automation. Vous pouvez afficher le code du runbook sur GitHub, dans le script [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). Vous pouvez utiliser le code PowerShell dans le fichier pour renouveler les certificats manuellement, en fonction des besoins.

Effectuez les étapes suivantes pour tester immédiatement le processus de renouvellement.

1. Modifiez le runbook **Update-AutomationRunAsCredential** et placez un caractère de commentaire (#) à la ligne 122, devant la commande **Exit(1)** .

   ```powershell
   #Exit(1)
   ```

2. Publier le runbook.
3. Démarrer le runbook.
4. Vérifiez que le renouvellement a réussi avec le code suivant :

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Sortie :

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Après le test, modifiez le runbook et supprimez le caractère de commentaire que vous avez ajouté à l’étape 1.
6. Publier le runbook.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Limitation des autorisations de compte d’identification

Pour contrôler le ciblage de l’automatisation sur des ressources dans Azure, vous pouvez exécuter le script [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Ce script modifie votre principal du service du compte d’identification existant pour créer et utiliser une définition de rôle personnalisée. Ce rôle dispose d’autorisations pour toutes les ressources, à l’exception de [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Après l’exécution du script **Update-AutomationRunAsAccountRoleAssignments.ps1**, les runbooks qui accèdent à Key Vault par le biais de l’utilisation de comptes d’identification ne fonctionnent plus. Avant d’exécuter le script, vous devez examiner les runbooks de votre compte pour repérer les appels à Azure Key Vault. Pour activer l’accès à Key Vault à partir des runbooks Azure Automation, vous devez [ajouter le compte d’identification aux autorisations de Key Vault](#add-permissions-to-key-vault).

Si vous devez restreindre davantage ce que le principal du service du compte d’identification peut faire, vous pouvez ajouter d’autres types de ressources à l’élément `NotActions` de la définition de rôle personnalisée. L’exemple suivant restreint l’accès à `Microsoft.Compute/*`. Si vous ajoutez ce type de ressource à l’élément `NotActions` de la définition de rôle, le rôle ne sera pas en mesure d’accéder à une ressource de calcul. Pour en savoir plus sur les définitions de rôle, consultez [Comprendre les définitions de rôle relatives aux ressources Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Vous pouvez déterminer si le principal du service utilisé par votre compte d’identification se trouve dans la définition du rôle Contributeur ou dans une autre définition personnalisée. 

1. Accédez à votre compte Automation, puis sélectionnez **Comptes d’identification** dans la section des paramètres de compte.
2. Sélectionnez **Compte d'identification Azure**. 
3. Sélectionnez **Rôle** pour rechercher la définition de rôle en cours d’utilisation.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Vous pouvez également déterminer la définition de rôle utilisée par les comptes d’identification pour plusieurs abonnements ou comptes Automation. Pour ce faire, utilisez le script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) dans PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Ajouter des autorisations à Key Vault

Vous pouvez autoriser Azure Automation à vérifier si Key Vault et votre principal du service du compte d’identification utilisent une définition de rôle personnalisée. Vous devez respecter les consignes suivantes :

* accorder les autorisations à Key Vault ;
* définir la stratégie d’accès.

Vous pouvez utiliser le script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) dans PowerShell Gallery pour donner à votre compte d’identification des autorisations sur Key Vault. Pour plus d’informations sur la définition des autorisations sur Key Vault, consultez [Accorder aux applications l’accès à un coffre de clés](../key-vault/key-vault-group-permissions-for-apps.md).

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Résolution des problèmes de configuration incorrecte pour les comptes d’identification

Certains éléments de configuration nécessaires à un compte d’identification standard ou à un compte d’identification Classic ont peut-être été supprimés, ou n’ont pas été créés correctement lors de l’installation initiale. Les cas possibles de configuration incorrecte sont les suivants :

* Ressource de certificat
* Ressource de connexion
* Compte d’identification supprimé du rôle Contributeur
* Principal du service ou application dans Azure AD

Pour de tels cas de configuration incorrecte, le compte Automation détecte les modifications et affiche pour le compte l’état `Incomplete`, dans le volet des propriétés des Comptes d’identification.

![État Incomplet pour la configuration du compte d’identification](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Lorsque vous sélectionnez le compte d’identification, le volet des propriétés du compte affiche le message d’erreur suivant :

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Vous pouvez rapidement résoudre ces problèmes liés au compte d’identification en supprimant et en recréant le compte.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les principaux de service, consultez [Objets principal du service et application](../active-directory/develop/app-objects-and-service-principals.md).
* Pour plus d’informations sur les certificats et les services Azure, consultez [Vue d’ensemble des certificats pour Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
