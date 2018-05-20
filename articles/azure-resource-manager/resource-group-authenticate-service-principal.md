---
title: Créer une identité pour l’application Azure avec PowerShell | Microsoft Docs
description: Explique comment utiliser Azure PowerShell pour créer une application et un principal du service Azure Active Directory, et comment accorder à l’application l’accès aux ressources par le biais du contrôle d’accès en fonction du rôle. Il explique comment authentifier l’application avec un certificat.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2018
ms.author: tomfitz
ms.openlocfilehash: 6ad1fd0ab51a93dbab5651ee80f6b6792dd331b9
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Utiliser Azure PowerShell pour créer un principal du service avec un certificat

Lorsque vous avez une application ou un script qui doit pouvoir accéder à des ressources, vous pouvez configurer une identité pour l’application et authentifier l’application avec ses propres informations d’identification. Cette identité est connue en tant que principal de service. Cette approche vous permet d’effectuer les opérations suivantes :

* Affecter à l’identité de l’application des autorisations différentes de vos propres autorisations. En règle générale, ces autorisations sont strictement limitées à ce que l’application doit faire.
* Utilisez un certificat pour l’authentification lors de l’exécution d’un script sans assistance.

> [!IMPORTANT]
> Au lieu de créer un principal du service, envisagez d’utiliser Managed Service Identity Azure AD pour l’identité de votre application. MSI Azure AD est une fonctionnalité de la préversion publique d’Azure Active Directory qui simplifie la création d’une identité pour le code. Si votre code s’exécute sur un service qui prend en charge MSI Azure AD et accède aux ressources qui prennent en charge l’authentification Azure Active Directory, MSI Azure AD correspond bien à vos besoins. Pour en savoir plus sur MSI Azure AD, y compris les services qui prennent actuellement en charge cette fonctionnalité, consultez [Managed Service Identity pour les ressources Azure](../active-directory/managed-service-identity/overview.md).

Cet article explique comment créer un principal du service qui s’authentifie avec un certificat. Pour configurer un principal du service avec un mot de passe, consultez [Créer un principal du service Azure avec Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Pour cet article, vous devez disposer de la [version la plus récente](/powershell/azure/get-started-azureps) de PowerShell.

## <a name="required-permissions"></a>Autorisations requises

Pour réaliser les étapes décrites dans cet article, vous devez disposer des autorisations suffisantes dans Azure Active Directory et votre abonnement Azure. Plus précisément, vous devez être en mesure de créer une application dans l’annuaire Azure Active Directory et d’affecter un rôle au principal du service.

Le moyen le plus simple pour vérifier que votre compte dispose des autorisations adéquates est d’utiliser le portail. Consultez [Vérifier l’autorisation requise](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Créer un principal du service avec un certificat auto-signé

L'exemple suivant aborde un scénario simple. Il utilise [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) pour créer un principal du service avec un certificat auto-signé et utilise [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) pour assigner le rôle de [Contributeur](../role-based-access-control/built-in-roles.md#contributor) au principal du service. L’attribution de rôle est étendue à votre abonnement Azure actuellement sélectionné. Pour sélectionner un autre abonnement, utilisez [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

L’exemple reste en veille pendant 20 secondes pour laisser le temps au nouveau principal du service de se propager dans Azure Active Directory. Si votre script n’attend pas suffisamment longtemps, une erreur indiquant : « Le principal {ID} n’existe pas dans le répertoire {DIR-ID} » s’affiche. Pour résoudre cette erreur, patientez quelques instants puis réexécutez la commande **New-AzureRmRoleAssignment**.

Vous pouvez étendre l’attribution de rôle à un groupe de ressources spécifiques à l’aide du paramètre **ResourceGroupName**. Vous pouvez également l’étendre à une ressource spécifique en utilisant les paramètres **ResourceType** et **ResourceName**. 

Si vous **n’avez pas Windows 10 ou Windows Server 2016**, vous devez télécharger le [générateur de certificats auto-signés](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) depuis le Centre de scripts Microsoft. Extrayez son contenu et importez l’applet de commande dont vous avez besoin.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Dans le script, remplacez les deux lignes suivantes pour générer le certificat.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fournir un certificat via un script PowerShell automatisé

Chaque fois que vous vous connectez en tant que principal de service, vous devez fournir l’ID de locataire du répertoire de votre application AD. Un locataire est une instance d’Azure Active Directory.

```powershell
$TenantId = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzureRmADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Créer un principal du service avec un certificat à partir de l’autorité de certification

L’exemple suivant utilise un certificat émis par une autorité de certification afin de créer le principal du service. L’attribution est étendue à l’abonnement Azure spécifié. Il ajoute le principal du service au rôle [Contributeur](../role-based-access-control/built-in-roles.md#contributor). Si une erreur se produit lors de l’attribution de rôle, il retente l’attribution.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fournir un certificat via un script PowerShell automatisé
Chaque fois que vous vous connectez en tant que principal de service, vous devez fournir l’ID de locataire du répertoire de votre application AD. Un locataire est une instance d’Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

L’ID de l’application et l’ID de l’abonné ne sont pas sensibles, vous pouvez donc les incorporer directement dans votre script. Pour récupérer l’ID client, utilisez :

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Si vous avez besoin extraire l’ID de l’application, utilisez :

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Modifier les informations d’identification

Pour modifier les informations d’identification d’une application Active Directory, en raison d’une faille de sécurité ou de l’expiration des informations d’identification, utilisez les applets de commande [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) et [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential).

Pour supprimer toutes les informations d’identification d’une application, utilisez :

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | Remove-AzureRmADAppCredential
```

Pour ajouter une valeur de certificat, créez un certificat auto-signé, comme indiqué dans cet article. Ensuite, utilisez :

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | New-AzureRmADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Déboguer

Lors de la création d’un principal du service, vous pouvez rencontrer les erreurs suivantes :

* **« Authentication_Unauthorized »** ou **« Aucun abonnement trouvé dans le contexte. »** - Vous voyez cette erreur lorsque votre compte ne possède pas les [autorisations requises](#required-permissions) sur Azure Active Directory pour inscrire une application. En règle générale, vous obtenez cette erreur lorsque seuls des utilisateurs administrateurs dans votre annuaire Azure Active Directory peuvent inscrire des applications et que votre compte n’est pas un compte d’administrateur. Demandez à votre administrateur de vous affecter à un rôle d’administrateur ou d’autoriser les utilisateurs ordinaires à inscrire des applications.

* Le message indique que votre compte **« n’est pas autorisé à effectuer l’action ’Microsoft.Authorization/roleAssignments/write’ sur l’étendue ’/subscriptions/{guid}’ »** - Vous voyez cette erreur lorsque votre compte ne dispose pas d’autorisations suffisantes pour affecter un rôle à une identité. Demandez à votre administrateur d’abonnement de vous ajouter au rôle Administrateur de l’accès utilisateur.

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un principal du service avec un mot de passe, consultez [Créer un principal du service Azure avec Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Pour obtenir des instructions détaillées sur l’intégration d’une application à Azure pour la gestion des ressources, consultez [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).
* Pour obtenir une explication plus détaillée des applications et des principaux du service, consultez la rubrique [Objets principal du service et application](../active-directory/active-directory-application-objects.md). 
* Pour plus d’informations sur l’authentification Azure Active Directory, consultez la rubrique [Scénarios d’authentification pour Azure AD](../active-directory/active-directory-authentication-scenarios.md).
