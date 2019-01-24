---
title: Exécuter des runbooks sur Runbook Worker hybride Azure Automation
description: Cet article fournit des informations sur l’exécution de Runbooks sur des machines dans votre fournisseur cloud ou centre de données local avec le rôle Runbook Worker hybride.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0d622f6f03f9d132f3c57910d8a60c5731ad7c94
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425778"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Exécution de Runbooks sur un Runbook Worker hybride

Il n’existe aucune différence entre la structure des runbooks qui s’exécutent dans Azure Automation et celle des runbooks qui s’exécutent sur un runbook Worker hybride. Il est très possible que les runbooks que vous utilisez avec chaque structure diffèrent considérablement. En effet, les runbooks ciblant un runbook Worker hybride gèrent généralement les ressources sur l’ordinateur local ou par rapport aux ressources de l’environnement local dans lequel il est déployé. Les runbooks dans Azure Automation gèrent généralement les ressources dans le cloud Azure.

Lorsque vous créez des runbooks pour s’exécuter sur un Runbook Worker hybride, vous devez modifier et tester les runbooks dans la machine qui héberge le Worker hybride. La machine hôte possède tous les modules PowerShell et accès réseau dont vous avez besoin pour gérer et accéder aux ressources locales. Une fois qu’un runbook a été testé sur la machine Worker hybride, vous pouvez le charger dans l’environnement Azure Automation où il est disponible pour être exécuté dans le Worker hybride. Il est important de savoir que les travaux s’exécutent sous le compte système local pour Windows ou un compte utilisateur spécial **nxautomation** pour Linux. Ce comportement peut présenter des différences subtiles lors de la création des runbooks pour un runbook Worker hybride. Ces modifications doivent être examinées quand vous écrivez vos runbooks.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Démarrage d’un Runbook sur Runbook Worker hybride

[Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md) décrit différentes méthodes de démarrage d'un Runbook. La fonctionnalité Runbook Worker hybride ajoute une option **RunOn** qui vous permet de spécifier le nom d'un groupe de Runbooks Workers hybrides. Quand un groupe est spécifié, le runbook est récupéré et exécuté par l’un des Workers de ce groupe. Si cette option n’est pas spécifiée, il est exécuté dans Azure Automation comme habituellement.

Quand vous démarrez un runbook dans le portail Azure, une option **Exécuter sur** vous permet de sélectionner **Azure** ou **Worker hybride**. Si vous sélectionnez **Worker hybride**, vous pouvez sélectionner le groupe souhaité dans une liste déroulante.

Utilisez le paramètre **Exécuter sur**. Vous pouvez utiliser la commande suivante pour démarrer un Runbook nommé Test-Runbook sur un groupe de Runbooks Workers hybrides nommé MyHybridGroup avec Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Le paramètre **RunOn** a été ajouté à l’applet de commande **Start-AzureAutomationRunbook** dans la version 0.9.1 de Microsoft Azure PowerShell. Vous devez [télécharger la version la plus récente](https://azure.microsoft.com/downloads/) si une version antérieure est installée. Il vous suffit d’installer cette version sur une station de travail sur laquelle vous démarrez le Runbook à partir de PowerShell. Il n'est pas nécessaire de l'installer sur l'ordinateur de travail, sauf si vous avez l'intention de démarrer les Runbooks dessus"

## <a name="runbook-permissions"></a>Autorisations de Runbook

Les runbooks exécutés sur un runbook Worker hybride ne peuvent pas utiliser la méthode généralement utilisée pour l’authentification des runbooks auprès des ressources Azure, car ils accèdent à des ressources extérieures à Azure. Le runbook peut fournir sa propre authentification aux ressources locales ou configurer l’authentification à l’aide des [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
). Vous pouvez également spécifier un compte d’identification pour fournir un contexte utilisateur pour l’ensemble des runbooks.

### <a name="runbook-authentication"></a>Authentification des Runbooks

Par défaut, les runbooks s’exécutent dans le contexte du compte système local de l’ordinateur local pour Windows et un compte utilisateur spécial (**nxautomation**) pour Linux. De ce fait, ils doivent fournir leur propre authentification auprès des ressources auxquelles ils accèdent.

Vous pouvez utiliser les ressources [Informations d’identification](automation-credentials.md) et [Certificat](automation-certificates.md) dans votre Runbook avec des applets de commande qui vous permettent de spécifier des informations d’identification pour que vous puissiez vous authentifier auprès de différentes ressources. L'exemple suivant présente une partie d'un Runbook qui redémarre un ordinateur. Il récupère des informations d'identification à partir d'une ressource d'informations d'identification et le nom de l'ordinateur à partir d'une ressource de variable, puis il utilise ces valeurs avec l'applet de commande Restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Vous pouvez également utiliser [InlineScript](automation-powershell-workflow.md#inlinescript), qui vous permet d’exécuter des blocs de code sur une autre machine avec les informations d’identification spécifiées par le [paramètre commun PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Compte RunAs

Par défaut, le runbook worker hybride utilise le système local pour Windows et un compte utilisateur spécial (**nxautomation**) pour Linux afin d’exécuter des procédures opérationnelles. Plutôt que de laisser les Runbooks fournir leur propre authentification aux ressources locales, vous pouvez spécifier un compte **RunAs** pour un groupe de Workers hybrides. Vous spécifiez une [ressource d’information d’identification](automation-credentials.md) ayant accès aux ressources locales, après quoi tous les Runbooks utilisent ces informations d’identification au moment de leur exécution sur un Runbook Worker hybride du groupe.

Le nom d’utilisateur doit utiliser l’un des formats suivants :

* domaine\nom d’utilisateur
* username@domain
* nom d’utilisateur (pour les comptes locaux sur l’ordinateur local)

Utilisez la procédure suivante pour spécifier un compte RunAs pour un groupe de Workers hybrides :

1. Créez une [ressource d’information d’identification](automation-credentials.md) ayant accès aux ressources locales.
2. Dans le portail Azure, ouvrez le compte Automation.
3. Sélectionnez la vignette **Groupes de Workers hybrides** , puis sélectionnez le groupe.
4. Sélectionnez **Tous les paramètres**, puis **Paramètres du groupe Worker hybride**.
5. Sous **Exécuter en tant que**, remplacez **Par défaut** par **Personnalisé**.
6. Sélectionnez les informations d’identification et cliquez sur **Enregistrer**.

### <a name="managed-identities-for-azure-resources"></a>Identités managées pour les ressources Azure

Les Runbooks Workers hybrides s’exécutant sur des machines virtuelles Azure peuvent utiliser des identités managées pour les ressources Azure pour s’authentifier auprès des ressources Azure. L’utilisation d’identités managées pour les ressources Azure sur des comptes d’identification offre de nombreux avantages.

* Il n’est pas nécessaire d’exporter le certificat d’identification et de l’importer dans le Runbook Worker hybride.
* Il n’est pas nécessaire de renouveler le certificat utilisé par le compte d’identification.
* Il n’est pas nécessaire de gérer l’objet de connexion d’identification dans votre code de runbook.

Pour utiliser une identité managée pour les ressources Azure sur un runbook Worker hybride, vous devez effectuer les étapes suivantes :

1. Création d’une machine virtuelle Azure
2. [Configurez des identités managées pour les ressources Azure sur votre machine virtuelle](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. [Accordez à votre machine virtuelle l’accès à un groupe de ressources dans Azure Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. [Obtenir un jeton d’accès par l’identité managée attribuée par le système de machine virtuelle](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [Installez le Runbook Worker hybride Windows](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) sur la machine virtuelle.

Une fois les étapes précédentes effectuées, vous pouvez utiliser `Connect-AzureRmAccount -Identity` dans le runbook pour effectuer l’authentification auprès des ressources Azure. Cette configuration réduit la nécessité d’utiliser un compte d’identification et de gérer le certificat pour ce compte.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Compte d’identification Automation

Dans le cadre de votre processus de génération automatisé pour le déploiement de ressources dans Azure, vous pouvez avoir besoin d’accéder aux systèmes locaux afin de prendre en charge une tâche ou un ensemble d’étapes de votre séquence de déploiement. Pour prendre en charge l’authentification auprès d’Azure avec le compte d’identification, vous devez installer le certificat du compte d’identification.

Le runbook PowerShell suivant, **Export-RunAsCertificateToHybridWorker**, exporte le certificat d’identification de votre compte Azure Automation, le télécharge et l’importe dans le magasin de certificats de l’ordinateur local sur un Worker hybride connecté au même compte. Une fois cette étape terminée, il vérifie que le Worker peut s’authentifier auprès d’Azure avec le compte d’identification.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount** est désormais un alias de **Connect-AzureRMAccount**. Quand vous effectuez une recherche dans vos éléments de bibliothèque, si vous ne voyez pas **Connect-AzureRMAccount**, vous pouvez utiliser **Add-AzureRmAccount** ou mettre à jour vos modules dans votre compte Automation.

Enregistrez le Runbook *Export-RunAsCertificateToHybridWorker* sur votre ordinateur avec une extension `.ps1`. Importez-le dans votre compte Automation et modifiez le Runbook, en remplaçant la valeur de la variable `$Password` par votre mot de passe. Publiez, puis exécutez le runbook. Ciblez le groupe de Workers hybrides qui exécute et authentifie les runbooks avec le compte d’identification. Le flux de travaux signale la tentative d’importation du certificat dans le magasin de l’ordinateur local et complète par plusieurs lignes. Ce comportement dépend du nombre de comptes Automation définis dans votre abonnement et du résultat de l’authentification.

## <a name="job-behavior"></a>Comportement du travail

Les travaux sont gérés de façon légèrement différente sur des runbooks Workers hybrides que quand ils s’exécutent dans des bacs à sable Azure. Une différence essentielle est qu’il n’existe aucune limite sur la durée du travail sur des runbooks Workers hybrides. Les runbooks exécutés dans les bacs à sable Azure sont limités à 3 heures pour une [répartition de charge équilibrée](automation-runbook-execution.md#fair-share). Pour un runbook de longue durée, vous voulez vous assurer qu’il est résilient aux possibles redémarrages, par exemple si la machine qui héberge le Worker hybride redémarre. Si la machine hôte du Worker hybride redémarre, tous les travaux du runbook en cours d’exécution redémarrent du début ou à partir du dernier point de contrôle pour les runbooks PowerShell Workflow. Une fois qu’un travail de runbook est redémarré plus de 3 fois, il est suspendu.

## <a name="run-only-signed-runbooks"></a>Exécuter seulement des runbooks signés

Les workers hybrides de runbook peuvent être configurés pour exécuter seulement des runbooks signés avec une certaine configuration. La section suivante décrit comment configurer vos runbooks Workers hybrides pour exécuter des runbooks signés et comment signer vos runbooks.

> [!NOTE]
> Une fois que vous avez configuré un worker hybride de runbook pour exécuter seulement des runbooks signés, les runbooks qui n’ont **pas** été signés n’arrivent pas à s’exécuter sur le worker.

### <a name="create-signing-certificate"></a>Créer un certificat de signature

L’exemple suivant crée un certificat auto-signé qui peut être utilisé pour signer des runbooks. L’exemple crée le certificat et l’exporte. Le certificat est importé ultérieurement dans les workers hybrides de runbook. L’empreinte numérique est également retournée : elle est utilisée plus tard pour référencer le certificat.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="configure-the-hybrid-runbook-workers"></a>Configurer les workers hybrides de runbook

Copiez le certificat créé sur chaque worker hybride de runbook d’un groupe. Exécutez le script suivant pour importer le certificat et configurer le worker hybride de façon à utiliser la validation de signature sur les runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Signer vos runbooks avec le certificat

Si les Runbooks Workers hybrides sont configurés pour utiliser uniquement des runbooks signés, vous devez signer les runbooks qui doivent être utilisés par les Runbooks Workers hybrides. Utilisez l’exemple PowerShell suivant pour signer vos runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Une fois que le runbook a été signé, il doit être importé dans votre compte Automation et publié avec le bloc de signature. Pour savoir comment importer des runbooks, consultez [Importation d’un runbook à partir d’un fichier dans Azure Automation](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).

## <a name="troubleshoot"></a>Résolution des problèmes

Si l’exécution de vos runbooks ne se termine pas correctement, consultez le guide de dépannage sur les [échecs d’exécution des runbooks](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les différentes méthodes qui peuvent être utilisées pour démarrer un Runbook, consultez [Démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md).
* Pour comprendre les différentes manières d’utiliser des runbooks PowerShell dans Azure Automation à l’aide de l’éditeur de texte, consultez [Modification d’un runbook dans Azure Automation](automation-edit-textual-runbook.md)

