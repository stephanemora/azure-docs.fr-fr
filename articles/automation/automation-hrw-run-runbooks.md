---
title: Exécuter des runbooks sur Runbook Worker hybride Azure Automation
description: Cet article fournit des informations sur l’exécution de Runbooks sur des machines dans votre fournisseur cloud ou centre de données local avec le rôle Runbook Worker hybride.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 902734ddc7195d643c3aedb4054f57723d1a51c2
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632134"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Exécution de Runbooks sur un Runbook Worker hybride

Les runbooks ciblant un runbook Worker hybride gèrent généralement les ressources sur l’ordinateur local ou par rapport aux ressources de l’environnement local dans lequel le Worker est déployé. Les runbooks dans Azure Automation gèrent généralement les ressources dans le cloud Azure. Même s’ils sont utilisés différemment, les runbooks qui s’exécutent dans Azure Automation et les runbooks qui s’exécutent sur un runbook Worker hybride ont une structure identique.

Quand vous créez un runbook pour l’exécuter sur un runbook Worker hybride, vous devez le modifier et le tester sur la machine qui héberge le Worker. La machine hôte possède tous les modules PowerShell et l’accès réseau dont vous avez besoin pour gérer les ressources locales et y accéder. Une fois que vous avez testé le runbook sur la machine du runbook Worker hybride, vous pouvez le charger dans l’environnement Azure Automation, où il peut être exécuté sur le Worker. 

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorisations des runbooks pour un runbook Worker hybride

Étant donné qu’ils accèdent à des ressources non-Azure, les runbooks s’exécutant sur un runbook Worker hybride ne peuvent pas utiliser le mécanisme d’authentification généralement utilisé par runbooks pour s’authentifier auprès des ressources Azure. Un runbook peut fournir sa propre authentification auprès des ressources locales ou en configurer une à l’aide d’[identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Vous pouvez également spécifier un compte d’identification pour fournir un contexte utilisateur pour l’ensemble des runbooks.

### <a name="runbook-authentication"></a>Authentification des Runbooks

Par défaut, les runbooks s’exécutent sur l’ordinateur local. Pour Windows, ils s’exécutent dans le contexte du compte système local. Pour Linux, ils s’exécutent dans le contexte du compte d’utilisateur spécial **nxautomation**. Dans les deux scénarios, les runbooks doivent fournir leur propre authentification auprès des ressources auxquelles ils accèdent.

Vous pouvez utiliser les ressources [Informations d’identification](automation-credentials.md) et [Certificat](automation-certificates.md) dans votre runbook avec des applets de commande qui vous permettent de spécifier des informations d’identification pour que le runbook puisse s’authentifier auprès de différentes ressources. L'exemple suivant présente une partie d'un Runbook qui redémarre un ordinateur. Il récupère des informations d’identification à partir d’une ressource d’informations d’identification, ainsi que le nom de l’ordinateur à partir d’une ressource de variable, puis il utilise ces valeurs avec l’applet de commande `Restart-Computer`.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Vous pouvez également utiliser une activité [InlineScript](automation-powershell-workflow.md#inlinescript). `InlineScript` vous permet d’exécuter des blocs de code sur une autre machine avec les informations d’identification spécifiées par le [paramètre commun PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>compte d'identification

Plutôt que de laisser votre runbook fournir sa propre authentification auprès des ressources locales, vous pouvez spécifier un compte d’identification pour un groupe de runbooks Workers hybrides. Pour cela, vous devez définir une [ressource d’informations d’identification](automation-credentials.md) qui a accès aux ressources locales. Ces ressources incluent des magasins de certificats et tous les runbooks s’exécutent sous ces informations d’identification sur un runbook Worker hybride dans le groupe.

Le nom d’utilisateur doit utiliser l’un des formats suivants :

* domaine\nom d’utilisateur
* username@domain
* nom d’utilisateur (pour les comptes locaux sur l’ordinateur local)

Utilisez la procédure suivante pour spécifier un compte d’identification pour un groupe de runbooks Workers hybrides.

1. Créez une [ressource d’information d’identification](automation-credentials.md) ayant accès aux ressources locales.
2. Dans le portail Azure, ouvrez le compte Automation.
3. Sélectionnez la vignette **Groupes de Workers hybrides** , puis sélectionnez le groupe.
4. Sélectionnez **Tous les paramètres**, puis **Paramètres du groupe Worker hybride**.
5. Remplacez la valeur **Par défaut** du compte d’**identification** par **Personnalisé**.
6. Sélectionnez les informations d’identification et cliquez sur **Enregistrer**.

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Identités managées pour les ressources Azure

Les runbooks Workers hybrides sur des machines virtuelles Azure peuvent utiliser des identités managées de ressources Azure pour s’authentifier auprès des ressources Azure. L’utilisation d’identités managées de ressources Azure au lieu de comptes d’identification offre des avantages, car vous n’avez pas besoin d’effectuer les tâches suivantes :

* Exporter le certificat d’identification et l’importer dans le runbook Worker hybride
* Renouveler le certificat utilisé par le compte d’identification
* Gérer l’objet de connexion d’identification dans le code de votre runbook

Effectuez les étapes suivantes pour utiliser une identité managée pour des ressources Azure sur un runbook Worker hybride.

1. Créez une machine virtuelle Azure.
2. Configurez des identités managées pour les ressources Azure sur la machine virtuelle. Consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant le portail Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Accordez à votre machine virtuelle l’accès à un groupe de ressources dans Resource Manager. Reportez-vous à [Utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder à Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installez le runbook Worker hybride sur la machine virtuelle. Consultez [Déployer un runbook Worker hybride Windows](automation-windows-hrw-install.md).
5. Mettez à jour le runbook pour utiliser l’applet de commande [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) avec le paramètre `Identity` pour l’authentification auprès des ressources Azure. Cette configuration réduit la nécessité d’utiliser un compte d’identification et d’effectuer la gestion de comptes associée.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity` fonctionne pour un runbook Worker hybride avec une identité attribuée par le système et une identité unique attribuée par l’utilisateur. Si vous utilisez plusieurs identités attribuées par l’utilisateur sur le runbook Worker hybride, votre runbook doit spécifier le paramètre *AccountId* pour que `Connect-AzAccount` sélectionne une identité attribuée par un utilisateur.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Compte d’identification Automation

Dans le cadre de votre processus de génération automatisé pour le déploiement de ressources dans Azure, vous pouvez avoir besoin d’accéder aux systèmes locaux afin de prendre en charge une tâche ou un ensemble d’étapes de votre séquence de déploiement. Pour fournir l’authentification auprès d’Azure avec le compte d’identification, vous devez installer le certificat du compte d’identification.

Le runbook PowerShell suivant, appelé **Export-RunAsCertificateToHybridWorker**, exporte le certificat d’identification à partir de votre compte Azure Automation. Le runbook télécharge le certificat et l’importe dans le magasin de certificats de la machine locale sur un runbook Worker hybride connecté au même compte. Une fois cette étape terminée, le runbook vérifie que le Worker peut s’authentifier auprès d’Azure avec le compte d’identification.

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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
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

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Pour les runbooks PowerShell, `Add-AzAccount` et `Add-AzureRMAccount` sont des alias de `Connect-AzAccount`. Quand vous effectuez une recherche dans vos éléments de bibliothèque, si vous ne voyez pas `Connect-AzAccount`, vous pouvez utiliser `Add-AzAccount` ou mettre à jour vos modules dans votre compte Automation.

Pour terminer la préparation du compte d’identification :

1. Enregistrez le runbook **Export-RunAsCertificateToHybridWorker** sur votre ordinateur avec une extension **.ps1**.
2. Importez-le dans votre compte Automation.
3. Modifiez le runbook en changeant la valeur de la variable `Password` de votre propre mot de passe. 
4. Publier le runbook.
5. Exécutez le runbook en ciblant le groupe de runbooks Workers hybrides qui exécute et authentifie les runbooks avec le compte d’identification. 
6. Examinez le flux de travaux pour voir qu’il signale la tentative d’importation du certificat dans le magasin de l’ordinateur local et complète par plusieurs lignes. Ce comportement dépend du nombre de comptes Automation définis dans votre abonnement et du résultat de l’authentification.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Comportement des travaux sur des runbooks Workers hybrides

Azure Automation gère les travaux exécutés sur des runbooks Workers hybrides d’une manière légèrement différente des travaux exécutés dans des bacs à sable Azure. Une différence essentielle est qu’il n’existe aucune limite de durée du travail sur les runbooks Workers. Les runbooks exécutés dans les bacs à sable Azure sont limités à trois heures pour une [répartition de charge équilibrée](automation-runbook-execution.md#fair-share).

Un runbook long a intérêt à être résilient à d’éventuels redémarrages, par exemple si la machine qui héberge le Worker redémarre. Si la machine hôte du runbook Worker hybride redémarre, tous les travaux du runbook en cours d’exécution redémarrent au début ou au dernier point de contrôle pour les runbooks PowerShell Workflow. Quand un travail de runbook a redémarré plus de trois fois, il est suspendu.

N’oubliez pas que les travaux des runbooks Workers hybrides s’exécutent sous le compte système local sur Windows ou le compte **nxautomation** sur Linux. Pour Linux, vous devez vous assurer que le compte **nxautomation** a accès à l’emplacement où sont stockés les modules du runbook. Quand vous utilisez l’applet de commande [Install-Module](/powershell/module/powershellget/install-module), veillez à spécifier AllUsers pour le paramètre `Scope` afin de garantir l’accès du compte **nxautomation**. Pour plus d’informations sur PowerShell sur Linux, consultez [Problèmes connus pour PowerShell sur les plateformes non-Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Démarrage d’un runbook sur un runbook Worker hybride

[Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md) décrit différentes méthodes de démarrage d’un runbook. Le démarrage d’un runbook sur un runbook Worker hybride utilise une option **Exécuter sur** qui vous permet de spécifier le nom d’un groupe de runbooks Workers hybrides. Quand un groupe est spécifié, l’un des Workers de ce groupe récupère et exécute le runbook. Si votre runbook ne spécifie pas cette option, Azure Automation exécute le runbook comme d’habitude.

Quand vous démarrez un runbook dans le portail Azure, une option **Exécuter sur** vous permet de sélectionner **Azure** ou **Worker hybride**. Si vous sélectionnez **Worker hybride**, vous pouvez choisir le groupe de runbooks Workers hybrides dans une liste déroulante.

Utilisez le paramètre `RunOn` avec l’applet de commande `Start-AzureAutomationRunbook`. L’exemple suivant utilise Windows PowerShell pour démarrer un runbook nommé **Test-Runbook** sur un groupe de runbooks Workers hybrides nommé MyHybridGroup.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Le paramètre `RunOn` a été ajouté à `Start-AzureAutomationRunbook` dans la version 0.9.1 de Microsoft Azure PowerShell. Vous devez [télécharger la version la plus récente](https://azure.microsoft.com/downloads/) si une version antérieure est installée. Installez uniquement cette version sur une station de travail sur laquelle vous démarrez le runbook à partir de PowerShell. Il n’est pas nécessaire de l’installer sur l’ordinateur du runbook Worker hybride, sauf si vous avez l’intention de démarrer des runbooks à partir de cet ordinateur.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Utilisation de runbooks signés sur un runbook Worker hybride Windows

Vous pouvez configurer un runbook Worker hybride Windows pour exécuter uniquement des runbooks signés.

> [!IMPORTANT]
> Une fois que vous avez configuré un Worker hybride de runbook pour exécuter seulement des runbooks signés, les runbooks qui n’ont pas été signés n’arrivent pas à s’exécuter sur le Worker.

### <a name="create-signing-certificate"></a>Créer un certificat de signature

L’exemple suivant crée un certificat auto-signé qui peut être utilisé pour signer des runbooks. Ce code crée le certificat et l’exporte afin que le runbook Worker hybride puisse l’importer ultérieurement. L’empreinte est également retournée à des fins d’utilisation ultérieure pour référencer le certificat.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importer un certificat et configurer des Workers pour la validation de signature

Copiez le certificat que vous avez créé sur chaque runbook Worker hybride dans un groupe. Exécutez le script suivant pour importer le certificat et configurer les Workers de façon à utiliser la validation de signature sur les runbooks.

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

Si les runbooks Workers hybrides sont configurés pour utiliser uniquement des runbooks signés, vous devez signer les runbooks qui doivent être utilisés par les runbooks Workers hybrides. Utilisez l’exemple de code PowerShell suivant pour signer ces runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quand un runbook a été signé, vous devez l’importer dans votre compte Automation et le publier avec le bloc de signature. Pour savoir comment importer des runbooks, consultez [Importation d’un runbook à partir d’un fichier dans Azure Automation](manage-runbooks.md#importing-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Utilisation de runbooks signés sur un runbook Worker hybride Linux

Pour pouvoir utiliser des runbooks signés, un runbook Worker hybride Linux doit avoir l’exécutable [GPG](https://gnupg.org/index.html) exécutable sur la machine locale.

> [!IMPORTANT]
> Une fois que vous avez configuré un runbook Worker hybride pour exécuter seulement des runbooks signés, les runbooks qui n’ont pas été signés n’arrivent pas à s’exécuter sur le Worker.

### <a name="create-a-gpg-keyring-and-keypair"></a>Créer un porte-clés et une paire de clés GPG

Pour créer un porte-clés et une paire de clés GPG, utilisez le compte **nxautomation** du runbook Worker hybride.

1. Utilisez l’application sudo pour vous connecter en tant que compte **nxautomation**.

    ```bash
    sudo su – nxautomation
    ```

2. Une fois que vous utilisez **nxautomation**, générez la paire de clés GPG. GPG vous guide tout au long des étapes. Vous devez fournir un nom, une adresse e-mail, un délai d’expiration et une phrase secrète. Ensuite, patientez jusqu’à ce que l’entropie soit suffisante sur la machine pour que la clé soit générée.

    ```bash
    sudo gpg --generate-key
    ```

3. Étant donné que le répertoire GPG a été généré avec sudo, vous devez remplacer son propriétaire par **nxautomation** à l’aide de la commande suivante.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Rendre le porte-clés disponible pour le runbook Worker hybride

Une fois le porte-clés créé, vous devez le rendre disponible au runbook Worker hybride. Modifiez le fichier de paramètres **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** en y ajoutant l’exemple de code suivant dans la section `[worker-optional]`.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Vérifier que la validation de signature est activée

Si la validation de signature a été désactivée sur la machine, vous devez l’activer en exécutant la commande sudo suivante. Remplacez `<LogAnalyticsworkspaceId>` par l’ID de votre espace de travail.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Signer un runbook

Une fois que vous avez configuré la validation de signature, utilisez la commande GPG suivante pour signer un runbook.

```bash
gpg –-clear-sign <runbook name>
```

Le runbook signé est appelé `<runbook name>.asc`.

Vous pouvez maintenant charger le runbook signé sur Azure Automation et l’exécuter comme un runbook normal.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les méthodes de démarrage d’un runbook, consultez [Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md).
* Pour comprendre comment utiliser l’éditeur de texte pour travailler avec des runbooks PowerShell dans Azure Automation, consultez [Modification d’un runbook dans Azure Automation](automation-edit-textual-runbook.md).
* Si l’exécution de vos runbooks ne se termine pas correctement, consultez le guide de résolution des problèmes liés aux [échecs d’exécution des runbooks](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Pour plus d’informations sur PowerShell, notamment le langage de référence et les modules d’apprentissage, consultez la [Documentation PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
