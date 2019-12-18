---
title: Gérer les comptes d’identification Azure Automation
description: Cet article décrit comment gérer vos comptes d’identification avec PowerShell ou à partir du portail.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ce6c27487e834325b59fb21ba0d54d1e788ea57
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951375"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gérer les comptes d’identification Azure Automation

Les comptes d’identification dans Azure Automation sont utilisés pour fournir une authentification permettant de gérer des ressources dans Azure avec des applets de commande Azure.

Quand vous créez un compte d’identification, il crée un nouvel utilisateur du principal du service dans Azure Active Directory et attribue le rôle de contributeur à cet utilisateur au niveau de l’abonnement. Pour les runbooks qui utilisent des Runbooks Workers hybrides sur des machines virtuelles Azure, vous pouvez utiliser [des identités managées pour les ressources Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) plutôt que des comptes d’identification pour authentifier vos ressources Azure.

Il existe deux types de comptes d’identification :

* **Compte d’identification Azure** : ce compte est utilisé pour gérer les ressources des [modèles de déploiement Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).
  * Crée une application Azure AD avec un certificat auto-signé, crée un compte de principal de service pour cette application dans Azure AD et affecte le rôle Collaborateur pour le compte dans votre abonnement actuel. Vous pouvez remplacer ce paramètre par un rôle de propriétaire ou par tout autre rôle. Pour plus d’informations, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).
  * Crée une ressource de certificat Automation nommée *AzureRunAsCertificate* dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat utilisée par l’application Azure AD.
  * Crée une ressource de connexion Automation nommée *AzureRunAsConnection* dans le compte Automation spécifié. La ressource de connexion conserve les ID applicationId, tenantId et subscriptionId, et l’empreinte de certificat.

* **Compte d’identification Classic Azure** : ce compte est utilisé pour gérer les ressources des [modèles de déploiement classiques](../azure-resource-manager/resource-manager-deployment-model.md).
  * Crée un certificat de gestion dans l’abonnement
  * Crée une ressource de certificat Automation nommée *AzureClassicRunAsCertificate*dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat utilisée par le certificat de gestion.
  * Crée une ressource de connexion Automation nommée *AzureClassicRunAsConnection* dans le compte Automation spécifié. La ressource de connexion conserve le nom de l’abonnement, l’ID subscriptionId et le nom de ressource de certificat.
  * Doit être un coadministrateur sur l’abonnement pour créer ou renouveler

  > [!NOTE]
  > Les abonnements Azure Cloud Solution Provider (Azure CSP) prennent uniquement en charge le modèle Azure Resource Manager ; les services hors Azure Resource Manager ne sont pas disponibles dans le programme. Lorsque vous utilisez un abonnement CSP, le Compte d’identification Azure Classic n’est pas créé. Le compte d’identification Azure est, lui, toujours créé. Pour en savoir plus sur les abonnements CSP, consultez [Services disponibles dans les abonnements CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

  > [!NOTE]
  > Le principal de service d’un compte d’identification ne dispose pas d’autorisations pour lire Azure Active Directory par défaut. Si vous souhaitez ajouter des autorisations pour lire ou gérer Azure Active Directory, vous devrez accorder cette autorisation sur le principal de service sous **Autorisations des API**. Pour en savoir plus, consultez [Ajouter des autorisations pour accéder aux API web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a> Autorisations pour configurer des comptes d’identification

Pour créer ou mettre à jour un compte d’identification, vous devez disposer d’autorisations et de privilèges spécifiques. Un administrateur d’application dans Azure Active Directory et un propriétaire dans un abonnement peuvent effectuer toutes les tâches. Dans une situation où les responsabilités sont partagées, le tableau suivant montre une liste des tâches, le cmdlet équivalent et les autorisations nécessaires :

|Tâche|Applet de commande  |Autorisations minimales  |Où vous définissez les autorisations|
|---|---------|---------|---|
|Créer une application Azure AD|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Rôle de développeur d’applications<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Accueil > Azure Active Directory > Inscriptions des applications |
|Ajoutez les informations d’identification à l’application.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Administrateur d’application ou administrateur général<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Accueil > Azure Active Directory > Inscriptions des applications|
|Créer et obtenir un principal du service Azure AD|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Administrateur d’application ou administrateur général<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Accueil > Azure Active Directory > Inscriptions des applications|
|Attribuer ou obtenir le rôle RBAC pour le principal spécifié|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Vous devez disposer des autorisations suivantes :</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Ou être un :</br></br>Administrateur ou propriétaire de l’accès utilisateur        | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Accueil > Abonnements > \<nom_abonnement\> - Contrôle d’accès (IAM)|
|Créer ou supprimer un certificat Automation|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Contributeur sur le groupe de ressources         |Groupe de ressources du compte Automation|
|Créer ou supprimer une connexion Automation|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Contributeur sur le groupe de ressources |Groupe de ressources du compte Automation|

<sup>1</sup> Les utilisateurs non-administrateurs dans votre locataire Azure AD peuvent [inscrire des applications AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) si l’option **Les utilisateurs peuvent inscrire des applications** du locataire Azure AD dans la page **Paramètres utilisateur** a la valeur **Oui**. Si le paramètre d’inscriptions d’applications est défini sur **Non**, l’utilisateur effectuant cette action doit être celui défini dans la table précédente.

Si vous n’êtes pas membre de l’instance Active Directory de l’abonnement avant d’être ajouté au rôle **Administrateur général** de l’abonnement, vous êtes ajouté en tant qu’invité. Dans ce cas, vous recevez un avertissement `You do not have permissions to create…` sur la page **Ajouter un compte Automation**. Les utilisateurs qui ont d’abord reçu le rôle **Administrateur général** peuvent être supprimés de l’instance Active Directory de l’abonnement, puis rajoutés pour en faire des utilisateurs complets dans Active Directory. Pour vérifier si tel est le cas, dans le volet **Azure Active Directory** du portail Azure, sélectionnez **Utilisateurs et groupes** et **Tous les utilisateurs**, choisissez l’utilisateur concerné, puis sélectionnez **Profil**. La valeur de l’attribut **Type d’utilisateur** sous le profil de l’utilisateur ne doit pas être **Invité**.

## <a name="permissions-classic"></a>Autorisations pour configurer des comptes d’identification Classic

Pour configurer ou renouveler des comptes d’identification Classic, vous devez avoir le rôle **Coadministrateur** au niveau de l’abonnement. Pour en savoir plus sur les autorisations Classic, consultez [Administrateurs d’abonnement Azure Classic](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Créer un compte d’identification dans le portail

Dans cette section, exécutez la procédure ci-après pour mettre à jour votre compte Azure Automation dans le Portail Azure. Vous créez individuellement les comptes d’identification et les comptes d’identification Classic. Si vous n’avez pas besoin de gérer des ressources classiques, vous pouvez simplement créer le compte d’identification Azure.

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
2. Dans le portail Azure, recherchez et sélectionnez **Comptes Automation**.
3. Sur la page **Comptes Automation**, sélectionnez votre compte Automation depuis la liste des comptes Automation.
4. Dans le volet à gauche, sélectionnez **Comptes d’identification** sous la section **Paramètres de compte**.
5. Selon le compte dont vous avez besoin, sélectionnez **Compte d’identification Azure** ou **Compte d’identification Azure Classic**. Une fois que vous avez sélectionné une option, le volet **Ajouter un compte d’identification Azure** ou **Ajouter un compte d’identification Azure Classic** s’affiche. Après avoir consulté les informations correspondantes, cliquez sur **Créer** pour procéder à la création du compte d’identification.
6. Pour suivre la progression de la création du compte d’identification, accédez à l’onglet **Notifications** du menu. Une bannière est également affichée indiquant que le compte est en cours de création. L’exécution de ce processus peut prendre plusieurs minutes.

## <a name="create-run-as-account-using-powershell"></a>Créer un compte d’identification avec PowerShell

## <a name="prerequisites"></a>Prérequis

La liste suivante indique les configurations requises pour créer un compte d’identification dans PowerShell :

* Windows 10 ou Windows Server 2016 avec les modules Azure Resource Manager 3.4.1 et ultérieur. Le script PowerShell ne prend pas en charge les versions antérieures de Windows.
* Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations sur la version PowerShell 1.0, voir [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Un compte Automation, référencé en tant que valeur des paramètres *–AutomationAccountName* et *-ApplicationDisplayName*.
* Autorisations équivalentes à ce qui est indiqué dans [Autorisations nécessaires pour configurer des comptes d’identification](#permissions)

Pour obtenir les valeurs des paramètres *SubscriptionID*, *ResourceGroup* et *AutomationAccountName*, qui sont des paramètres obligatoires pour le script, procédez comme suit :

1. Dans le portail Azure, recherchez et sélectionnez **Comptes Automation**.
1. Dans la page de compte Automation, sélectionnez votre compte Automation, puis sous **Paramètres de compte**, sélectionnez **Propriétés**.
1. Notez les valeurs de **ID d’abonnement**, **Nom** et **Groupe de ressources** sur la page **Propriétés**.

   ![Page « Propriétés » du compte Automation](media/manage-runas-account/automation-account-properties.png)

Ce script PowerShell prend en charge les configurations suivantes :

* Créez un compte d’identification à l’aide d’un certificat auto-signé.
* Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé.
* Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat émis par votre AC d’entreprise.
* Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé dans le cloud Azure Government.

>[!NOTE]
> Si vous sélectionnez l’option permettant de créer un compte d’identification Classic, après l’exécution du script, vous devez charger le certificat public (extension de nom de fichier .cer) dans le magasin de gestion de l’abonnement dans lequel le compte Automation a été créé.

1. Enregistrez le script suivant sur votre ordinateur. Dans cet exemple, enregistrez-le sous le nom de fichier *New-RunAsAccount.ps1*.

   Le script utilise plusieurs applets de commande Azure Resource Manager pour créer des ressources. Le tableau des [autorisations](#permissions) précédent liste les applets de commande et les autorisations qu’elles nécessitent.

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
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

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
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
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

    > [!IMPORTANT]
    > **Add-AzureRmAccount** est désormais un alias de **Connect-AzureRMAccount**. Quand vous effectuez une recherche dans vos éléments de bibliothèque, si vous ne voyez pas **Connect-AzureRMAccount**, vous pouvez utiliser **Add-AzureRmAccount** ou [mettre à jour vos modules](automation-update-azure-modules.md) dans votre compte Automation.

1. Sur votre ordinateur, démarrez **Windows PowerShell** dans l’écran **Démarrer** avec des droits de l’utilisateur élevés.
1. À partir de l’interface de ligne de commande avec élévation de privilèges, accédez au dossier contenant le script que vous avez créé à l’étape 1.
1. Exécutez le script en utilisant les valeurs de paramètre pour la configuration dont vous avez besoin.

    **Créer un compte d’identification à l’aide d’un certificat auto-signé**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat d’entreprise**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé dans le cloud Azure Government**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Connectez-vous avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

Une fois le script exécuté, notez les points suivants :

* Si vous avez créé un compte d’identification Classic avec un certificat public auto-signé (fichier .cer), le script le crée et l’enregistre dans le dossier de fichiers temporaires sur votre ordinateur, sous le profil d’utilisateur *%USERPROFILE%\AppData\Local\Temp* utilisé pour exécuter la session PowerShell.

* Si vous avez créé un compte d’identification Classic avec un certificat public d’entreprise (fichier .cer), utilisez ce certificat. Suivez les instructions pour [Charger un certificat de gestion API vers le portail Azure](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Supprimer un compte d’identification standard ou Classic

Cette section décrit comment supprimer et recréer votre compte d’identification standard ou Classic. Lorsque vous effectuez cette opération, le compte Automation est conservé. Après avoir supprimé le compte d’identification standard ou le compte d’identification Classic, vous pouvez le recréer dans le portail Azure.

1. Dans le portail Azure, ouvrez le compte Automation.

2. Sur la page **Compte Automation**, sélectionnez **Comptes d’identification**.

3. Sur la page de propriétés **Comptes d’identification**, sélectionnez le compte d’identification standard ou le compte d’identification Classic que vous voulez supprimer. Ensuite, dans le volet **Propriétés** du compte sélectionné, cliquez sur **Supprimer**.

   ![Supprimer un compte d’identification](media/manage-runas-account/automation-account-delete-runas.png)

1. Pour suivre la progression de la suppression du compte, accédez à l’onglet **Notifications** du menu.

1. Une fois le compte supprimé, vous pouvez le recréer sur la page de propriétés **Comptes d’identification** en sélectionnant l’option de création **Compte d’identification Azure**.

   ![Recréer le compte d’identification Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Renouvellement de certificat auto-signé

Avant que votre compte Azure d’identification n’expire, vous devez renouveler le certificat. Si vous pensez que le compte d’identification a été compromis, vous pouvez le supprimer et le recréer. Cette section décrit comment effectuer ces opérations.

Le certificat auto-signé que vous avez créé pour le compte d’identification expire 1 an après la date de création. Vous pouvez le renouveler à tout moment avant qu’il n’expire. Lorsque vous le renouvelez, le certificat valide en cours est conservé afin de garantir que les Runbooks en file d’attente ou en cours d’exécution, qui s’authentifient avec le compte d’identification, ne sont pas affectés. Le certificat reste valide jusqu’à sa date d’expiration.

> [!NOTE]
> Si vous avez configuré votre compte d’identification Automation pour utiliser un certificat émis par votre autorité de certification d’entreprise et que vous utilisez cette option, ce certificat est remplacé par un certificat auto-signé.

Pour renouveler le certificat, procédez comme suit :

1. Dans le portail Azure, ouvrez le compte Automation.

1. Sélectionnez **Comptes d’identification** sous **Paramètres du compte**.

    ![Panneau des propriétés du compte Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Dans la page des propriétés des **Comptes d’identification**, sélectionnez le compte d’identification standard ou le compte d’identification Classic pour lequel vous souhaitez renouveler le certificat.

1. Sur le volet **Propriétés** du compte sélectionné, cliquez sur **Renouveler le certificat**.

    ![Renouveler le certificat pour le compte d’identification](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Pour suivre la progression du renouvellement du certificat, accédez à l’onglet **Notifications** du menu.

## <a name="auto-cert-renewal"></a>Configurer le renouvellement automatique de certificat à l’aide d’un runbook Automation

Pour renouveler automatiquement les certificats, vous pouvez utiliser un runbook Automation. Le script suivant sur [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) active cette fonctionnalité dans votre compte Automation.

- Le script `GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1` crée une planification hebdomadaire pour renouveler les certificats de compte d’identification.
- Le script ajoute un runbook **Update-AutomationRunAsCredential** à votre compte Automation.
  - Vous pouvez également afficher le code runbook sur GitHub, dans le script : [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1).
  - Vous pouvez également utiliser le code PowerShell dans le fichier pour renouveler les certificats manuellement en fonction des besoins.

Pour tester immédiatement le processus de renouvellement, procédez comme suit :

1. Modifiez le runbook **Update-AutomationRunAsCredential** et placez un caractère de commentaire (`#`) à la ligne 122, devant la commande `Exit(1)`, comme indiqué ci-dessous.

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

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Après le test, modifiez le runbook et supprimez le caractère de commentaire que vous avez ajouté à **l’étape 1**.
6. **Publier** le runbook.

> [!NOTE]
> Pour exécuter le script, vous devez être **administrateur général** ou **administrateur de la société** dans Azure Active Directory.

## <a name="limiting-run-as-account-permissions"></a>Limitation des autorisations de compte d’identification

Pour contrôler le ciblage de l’automatisation sur des ressources dans Azure, vous pouvez exécuter le script [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) dans PowerShell Gallery pour changer votre principal du service de compte d’identification existant afin de créer et d’utiliser une définition de rôle personnalisée. Ce rôle disposera d’autorisations sur toutes les ressources, à l’exception du service [Key Vault](https://docs.microsoft.com/azure/key-vault/).

> [!IMPORTANT]
> Après l’exécution du script `Update-AutomationRunAsAccountRoleAssignments.ps1`, les runbooks qui accèdent à KeyVault par le biais de l’utilisation de comptes d’identification ne fonctionneront plus. Vous devez examiner les runbooks de votre compte pour détecter les appels à Azure Key Vault.
>
> Pour activer l’accès à Key Vault à partir des runbooks Azure Automation, vous devez [ajouter le compte d’identification aux autorisations de KeyVault](#add-permissions-to-key-vault).

Si vous devez limiter ce que le principal du service de compte d’identification peut faire par la suite, vous pouvez ajouter d’autres types de ressources à l’élément `NotActions` de la définition de rôle personnalisée. L’exemple suivant restreint l’accès à `Microsoft.Compute`. Si vous l’ajoutez à l’élément **NotActions** de la définition de rôle, ce rôle ne sera pas en mesure d’accéder à une ressource de calcul. Pour en savoir plus sur les définitions de rôle, consultez [Comprendre les définitions de rôle relatives aux ressources Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

Pour déterminer si le principal du service utilisé par votre compte d’identification se trouve dans le rôle **Collaborateur** ou dans une définition de rôle personnalisée, accédez à votre compte Automation et sous **Paramètres du compte**, sélectionnez **Comptes d’identification** > **Compte d’identification Azure**. Sous **Rôle**, vous trouverez la définition de rôle en cours d’utilisation.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Pour déterminer la définition de rôle utilisée par les comptes d’identification Automation pour plusieurs abonnements ou comptes Automation, vous pouvez utiliser le script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) dans PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Ajouter des autorisations à Key Vault

Si vous souhaitez autoriser Azure Automation à gérer Key Vault et que votre principal du service de compte d’identification utilise une définition de rôle personnalisée, vous devez prendre des mesures supplémentaires pour autoriser ce comportement :

* Accorder les autorisations à Key Vault
* Définir la stratégie d’accès

Vous pouvez utiliser le script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) dans PowerShell Gallery pour donner à votre compte d’identification des autorisations sur KeyVault, ou consulter [Accorder à des applications l’accès à un coffre de clés](../key-vault/key-vault-group-permissions-for-apps.md) pour plus d’informations sur la définition d’autorisations sur KeyVault.

## <a name="misconfiguration"></a>Configuration incorrecte

Certains éléments de configuration nécessaires pour que le compte d’identification standard ou le compte d’identification Classic fonctionne correctement ont peut-être été supprimés ou n’ont pas été créés correctement lors de l’installation initiale. Par exemple :

* Ressource de certificat
* Ressource de connexion
* Compte d’identification supprimé du rôle Contributeur
* Principal du service ou application dans Azure AD

Dans les cas précédents et dans d’autres instances de configuration incorrecte, le compte Automation détecte les modifications et affiche l’état *Incomplet* dans le volet des propriétés **Comptes d’identification** du compte.

![État Incomplet pour la configuration du compte d’identification](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Lorsque vous sélectionnez le compte d’identification, le volet **Propriétés** du compte affiche le message d’erreur suivant :

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Vous pouvez rapidement résoudre ces problèmes liés au compte d’identification en supprimant et en recréant le compte.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les principaux de service, consultez [Objets principal du service et application](../active-directory/develop/app-objects-and-service-principals.md).
* Pour plus d’informations sur les certificats et les services Azure, consultez [Vue d’ensemble des certificats pour Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
