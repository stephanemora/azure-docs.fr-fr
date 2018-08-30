---
title: Exécuter des runbooks sur Runbook Worker hybride Azure Automation
description: Cet article fournit des informations sur l’exécution de Runbooks sur des machines dans votre fournisseur cloud ou centre de données local avec le rôle Runbook Worker hybride.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f21457a63470b88e93ead97454f996cea38073a
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43103766"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Exécution de Runbooks sur un Runbook Worker hybride

Il n'existe aucune différence entre la structure des Runbooks qui s'exécutent dans Azure Automation et celle des Runbooks qui s'exécutent sur un Runbook Worker hybride. Toutefois, il est très possible que les Runbooks que vous utilisez avec chaque structure diffèrent considérablement, car les Runbooks ciblant un Runbook Worker hybride gèrent généralement les ressources sur l’ordinateur local ou par rapport aux ressources de l’environnement local dans lequel il est déployé, tandis que les Runbooks dans Azure Automation gèrent généralement les ressources dans le cloud Azure.

Lorsque vous créez des runbooks pour s’exécuter sur un Runbook Worker hybride, vous devez modifier et tester les runbooks dans la machine qui héberge le Worker hybride. La machine hôte possède tous les modules PowerShell et accès réseau dont vous avez besoin pour gérer et accéder aux ressources locales. Une fois qu’un runbook a été modifié et testé sur la machine Worker hybride, vous pouvez ensuite le télécharger dans l’environnement Azure Automation où il est disponible pour être exécuté dans le Worker hybride. Il est important de savoir que les travaux s’exécutent sous le compte système local pour Windows ou un compte utilisateur spécial (**nxautomation**) pour Linux, qui peut présenter des différences subtiles, lors de la création des runbooks de création pour un Runbook Worker hybride, cela doit être pris en considération.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Démarrage d’un Runbook sur Runbook Worker hybride

[Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md) décrit différentes méthodes de démarrage d'un Runbook. La fonctionnalité Runbook Worker hybride ajoute une option **RunOn** qui vous permet de spécifier le nom d'un groupe de Runbooks Workers hybrides. Si un groupe est spécifié, le Runbook est récupéré et exécuté par l’un des workers de ce groupe. Si cette option n'est pas spécifiée, il est exécuté dans Azure Automation comme habituellement.

Quand vous démarrez un Runbook sur le Portail Azure, une option **Exécuter sur** vous permet de sélectionner **Azure** ou **Worker hybride**. Si vous sélectionnez **Worker hybride**, vous pouvez sélectionner le groupe souhaité dans une liste déroulante.

Utilisez le paramètre **Exécuter sur**. Vous pouvez utiliser la commande suivante pour démarrer un Runbook nommé Test-Runbook sur un groupe de Runbooks Workers hybrides nommé MyHybridGroup avec Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Le paramètre **RunOn** a été ajouté à l’applet de commande **Start-AzureAutomationRunbook** dans la version 0.9.1 de Microsoft Azure PowerShell. Vous devez [télécharger la version la plus récente](https://azure.microsoft.com/downloads/) si une version antérieure est installée. Il vous suffit d’installer cette version sur une station de travail sur laquelle vous démarrez le Runbook à partir de PowerShell. Il n'est pas nécessaire de l'installer sur l'ordinateur de travail, sauf si vous avez l'intention de démarrer les Runbooks dessus"

## <a name="runbook-permissions"></a>Autorisations de Runbook

Les Runbooks exécutés sur un Runbook Worker hybride ne peuvent pas utiliser la méthode généralement utilisée pour l’authentification des Runbooks auprès des ressources Azure, car ils accèdent à des ressources extérieures à Azure. Le Runbook peut fournir sa propre authentification aux ressources locales, mais vous pouvez également spécifier un compte RunAs pour fournir un contexte utilisateur pour l’ensemble des Runbooks.

### <a name="runbook-authentication"></a>Authentification des Runbooks

Par défaut, les runbooks s’exécutent dans le contexte du compte système local de l’ordinateur local pour Windows et un compte utilisateur spécial (**nxautomation**) pour Linux. De ce fait, ils doivent fournir leur propre authentification auprès des ressources auxquelles ils accèdent.

Vous pouvez utiliser les ressources [Informations d’identification](automation-credentials.md) et [Certificat](automation-certificates.md) dans votre Runbook avec des applets de commande qui vous permettent de spécifier des informations d’identification pour que vous puissiez vous authentifier auprès de différentes ressources. L'exemple suivant présente une partie d'un Runbook qui redémarre un ordinateur. Il récupère des informations d'identification à partir d'une ressource d'informations d'identification et le nom de l'ordinateur à partir d'une ressource de variable, puis il utilise ces valeurs avec l'applet de commande Restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Vous pouvez également exploiter [InlineScript](automation-powershell-workflow.md#inlinescript), qui vous permet d’exécuter des blocs de code sur une autre machine avec les informations d’identification spécifiées par le [paramètre commun PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

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

### <a name="automation-run-as-account"></a>Compte d’identification Automation

Dans le cadre de votre processus de génération automatisé pour le déploiement de ressources dans Azure, vous pouvez avoir besoin d’accéder aux systèmes locaux afin de prendre en charge une tâche ou un ensemble d’étapes de votre séquence de déploiement. Pour prendre en charge l’authentification auprès d’Azure avec le compte d’identification, vous devez installer le certificat du compte d’identification.

Le Runbook PowerShell suivant, *RunAsCertificateToHybridWorker-Export*, exporte le certificat d’identification de votre compte Azure Automation, le télécharge et l’importe dans le magasin de certificats de l’ordinateur local sur un Worker hybride connecté au même compte. Une fois cette étape terminée, il vérifie que le Worker peut s’authentifier auprès d’Azure avec le compte d’identification.

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

Enregistrez le Runbook *Export-RunAsCertificateToHybridWorker* sur votre ordinateur avec une extension `.ps1`. Importez-le dans votre compte Automation et modifiez le Runbook, en remplaçant la valeur de la variable `$Password` par votre mot de passe. Publiez, puis exécutez le Runbook ciblant le groupe de Workers hybrides qui exécute et authentifie les Runbooks avec le compte d’identification. Le flux de travaux signale la tentative d’importer le certificat dans le magasin de l’ordinateur local et complète par plusieurs lignes, en fonction du nombre de comptes Automation définis dans votre abonnement et du résultat de l’authentification.

## <a name="job-behavior"></a>Comportement du travail

Les travaux sont gérés de façon légèrement différente sur des Runbooks Workers hybrides que lorsqu’ils s’exécutent sur des bacs à sable Azure. Une différence essentielle est qu’il n’existe aucune limite sur la durée du travail sur des Runbooks Workers hybrides. Les Runbooks exécutés dans les bacs à sable Azure sont limités à 3 heures, pour une [répartition de charge équilibrée](automation-runbook-execution.md#fair-share). Si vous disposez d’un runbook à long terme que vous souhaitez être résilient aux possibles redémarrages, par exemple si la machine qui héberge le Runbook Worker hybride redémarre. Si la machine hôte du Worker hybride redémarre, tous les travaux du runbook en cours d’exécution redémarrent du début ou à partir du dernier point de contrôle pour les runbooks PowerShell Workflow. Si un travail de runbook est redémarré plus de 3 fois, il est suspendu.

## <a name="run-only-signed-runbooks"></a>Exécuter seulement des runbooks signés

Les workers hybrides de runbook peuvent être configurés pour exécuter seulement des runbooks signés avec une certaine configuration. La section suivante décrit comment configurer vos workers hybrides de runbook pour exécuter des runbooks signés et comment signer vos runbooks.

> [!NOTE]
> Une fois que vous avez configuré un worker hybride de runbook pour exécuter seulement des runbooks signés, les runbooks qui n’ont **pas** été signés n’arrivent pas à s’exécuter sur le worker.

### <a name="create-signing-certificate"></a>Créer un certificat de signature

L’exemple suivant crée un certificat auto-signé qui peut être utilisé pour signer des runbooks. L’exemple crée le certificat et l’exporte. Le certificat est importé ultérieurement dans les workers hybrides de runbook. L’empreinte numérique est également retournée : elle est utilisée plus tard pour référencer le certificat.

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
* Pour comprendre les différentes procédures pour utiliser des Runbooks PowerShell et Workflow PowerShell dans Azure Automation à l’aide de l’éditeur de texte, consultez [Modifier des Runbooks textuels dans Azure Automation](automation-edit-textual-runbook.md)
