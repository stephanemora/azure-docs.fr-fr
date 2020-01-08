---
title: Compilation de configurations dans Azure Automation State Configuration
description: Cet article explique comment compiler des configurations d’état souhaité (DSC) pour Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: 9d8efce09c2981e4ee256228d4792bcf97172e6c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75366411"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Compilation de configurations DSC dans Azure Automation State Configuration

Dans Azure Automation State Configuration, vous pouvez compiler des configurations Desired State Configuration (DSC) de deux manières : dans Azure et dans Windows PowerShell. Le tableau suivant vous aide à déterminer quand utiliser chaque méthode en fonction des caractéristiques de chacune :

- Service de compilation Azure State Configuration
  - Méthode pour débutant avec interface utilisateur interactive
   - Suivi aisé de l’état des tâches

- Windows PowerShell
  - Appel à partir de Windows PowerShell sur la station de travail locale ou le service de build
  - Intégration avec le pipeline de test de développement
  - Fourniture de valeurs de paramètres complexes
  - Utilisation de données de nœud et non-nœud à l’échelle
  - Amélioration significative des performances

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compilation d’une configuration DSC dans Azure State Configuration

### <a name="portal"></a>Portail

1. Dans votre compte Automation, cliquez sur **Configuration d’état (DSC)** .
1. Cliquez sur l’onglet **Configurations**, puis cliquez sur le nom de la configuration à compiler.
1. Cliquez sur **Compiler**.
1. Si la configuration n’a aucun paramètre, vous devez confirmer si vous souhaitez la compiler. Si la configuration a des paramètres, le panneau **Compiler la configuration** s’ouvre et vous permet de fournir la valeur des paramètres. Pour plus d’informations sur les paramètres, consultez la section [**Paramètres de base**](#basic-parameters) suivante.
1. La page **Tâche de compilation** est ouverte de sorte que vous pouvez suivre l’état de la tâche de compilation, ainsi que les configurations de nœud (documents de configuration MOF) qui ont dû être placés sur le serveur collecteur Azure Automation State Configuration.

### <a name="azure-powershell"></a>Azure PowerShell

Vous pouvez utiliser [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) pour démarrer la compilation avec Windows PowerShell. L’exemple de code suivant commence la compilation d’une configuration DSC appelée **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` renvoie un objet de tâche de compilation que vous pouvez utiliser pour suivre son état. Vous pouvez ensuite utiliser cet objet de tâche de compilation avec [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)
pour déterminer l’état de la tâche de compilation, et [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
pour afficher ses flux (sortie). L’exemple de code suivant démarre la compilation de la configuration **SampleConfig** , attend qu’elle soit terminée, puis affiche ses flux.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>Paramètres de base

La déclaration de paramètres dans les configurations DSC, y compris les types de paramètres et les propriétés, fonctionne de la même manière que pour les runbooks Azure Automation. Consultez [Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md) pour en savoir plus sur les paramètres de runbooks.

L’exemple suivant utilise deux paramètres appelés **FeatureName** et **IsPresent** pour déterminer les valeurs des propriétés dans la configuration du nœud **ParametersExample.sample**, générée pendant la compilation.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Vous pouvez compiler les configurations DSC qui utilisent des paramètres de base dans le portail Azure Automation State Configuration ou avec Azure PowerShell :

#### <a name="portal"></a>Portail

Dans le portail, vous pouvez entrer des valeurs de paramètre après avoir cliqué sur **Compiler**.

![Paramètres de compilation de configuration](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell requiert des paramètres dans une [table de hachage](/powershell/module/microsoft.powershell.core/about/about_hash_tables) où la clé correspond au nom de paramètre et la valeur est égale à la valeur du paramètre.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Pour plus d’informations sur la transmission d’informations d’identification PowerShell en tant que paramètres, consultez [Ressources d’informations d’identification](#credential-assets) ci-dessous.

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Compilation de configurations dans Azure Automation qui contiennent des ressources composites

Les **ressources composites** vous permettent d’utiliser des configurations DSC en tant que ressources imbriquées à l’intérieur d’une configuration. Cela vous permet d’appliquer plusieurs configurations à une seule ressource. Consultez [Ressources composites : Utilisation d’une configuration DSC en tant que ressource](/powershell/scripting/dsc/resources/authoringresourcecomposite) pour en savoir plus sur les **ressources composites**.

> [!NOTE]
> Pour que les configurations contenant des **ressources composites** se compilent correctement, vous devez en premier lieu vous assurer que toutes les ressources DSC sur lesquelles le composite s’appuie sont d’abord importées dans Azure Automation.

L’ajout d’une **ressource composite** DSC n’est pas différent de l’ajout d’un module PowerShell à Azure Automation.
Les instructions pas à pas pour ce processus sont documentées dans l’article [Gérer les modules dans Azure Automation](/azure/automation/shared-resources/modules).

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Gestion de ConfigurationData lors de la compilation de la configuration dans Azure Automation

**ConfigurationData** vous permet de séparer la configuration structurelle de toute configuration spécifique de l’environnement lors de l’utilisation de la configuration de l’état souhaité PowerShell. Consultez [Distinguer « objet » et « emplacement » dans la configuration de l’état souhaité PowerShell](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) pour en savoir plus sur **ConfigurationData**.

> [!NOTE]
> Vous pouvez utiliser **ConfigurationData** lors de la compilation dans Azure Automation State Configuration à l’aide d’Azure PowerShell, mais pas dans le portail Azure.

L’exemple de configuration DSC suivant utilise **ConfigurationData** via les mots-clés **$ConfigurationData** et **$AllNodes**. Vous avez également besoin du module [**xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/) pour cet exemple :

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Vous pouvez compiler la configuration DSC précédente avec Windows PowerShell. Le script suivant ajoute deux configurations de nœud au serveur Pull Azure Automation State Configuration : **ConfigurationDataSample.MyVM1** et **ConfigurationDataSample.MyVM3** :

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Utilisation des ressources dans Azure Automation pendant la compilation

Les références de ressources sont les mêmes dans Azure Automation State Configuration et les runbooks. Pour plus d’informations, consultez les rubriques suivantes :

- [Certificates](automation-certificates.md)
- [Connexions](automation-connections.md)
- [Informations d'identification](automation-credentials.md)
- [Variables](automation-variables.md)

#### <a name="credential-assets"></a>Ressources d’informations d’identification

Les configurations DSC dans Azure Automation peuvent référencer des ressources d’informations d’identification Automation avec la cmdlet `Get-AutomationPSCredential`. Si une configuration a un paramètre de type **PSCredential**, vous pouvez utiliser l’applet de commande `Get-AutomationPSCredential` en passant le nom de la chaîne d’une ressource d’informations d’identification Azure Automation à l’applet de commande pour récupérer les informations d’identification. Vous pouvez ensuite utiliser cet objet pour le paramètre nécessitant l’objet **PSCredential**. En arrière-plan, la ressource d’informations d’identification Azure Automation portant le même nom est récupérée et transmise à la configuration. L’exemple ci-dessous montre ceci en action.

Conserver les informations d’identification en sûreté dans les configurations de nœud (documents de configuration MOF) nécessite le chiffrement des informations d’identification dans le fichier MOF de configuration de nœud. Cependant, actuellement, vous devez indiquer à la configuration de l’état souhaité PowerShell que vous êtes d’accord pour que les informations d’identification soient extraites en texte brut lors de la génération du fichier MOF de configuration de nœud, parce que la configuration de l’état souhaité PowerShell ne sait pas qu’Azure Automation va chiffrer l’intégralité du fichier MOF après sa génération via une tâche de compilation.

Vous pouvez indiquer à la configuration DSC PowerShell que vous êtes d’accord pour que les informations d’identification soient extraites en texte brut dans les fichiers MOF de configuration de nœud générés en utilisant ConfigurationData. Vous devez transmettre `PSDscAllowPlainTextPassword = $true` par le biais de **ConfigurationData** pour chaque nom de bloc de nœuds qui apparaît dans la configuration DSC et utilise les informations d’identification.

L’exemple suivant montre une configuration de l’état souhaité qui utilise une ressource d’informations d’identification Automation.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Vous pouvez compiler la configuration DSC précédente avec PowerShell. La commande PowerShell suivante ajoute deux configurations de nœud au serveur Pull Azure Automation State Configuration : **CredentialSample.MyVM1** et **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> À la fin de la compilation, l’erreur suivante peut s’afficher : **Le module « Microsoft.PowerShell.Management » n’a pas été importé, car le composant logiciel enfichable « Microsoft.PowerShell.Management » avait déjà été importé.** Vous pouvez ignorer cet avertissement sans problème.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Compilation des configurations dans Windows PowerShell et publication sur Azure Automation

Vous pouvez également importer les configurations de nœud (MOF) qui ont été compilées hors d’Azure.
Cela comprend la compilation à partir d’une station de travail de développeur ou dans un service comme [Azure DevOps](https://dev.azure.com).
Cette approche présente plusieurs avantages, notamment en termes de performances et de fiabilité.
La compilation dans Windows PowerShell offre également la possibilité de signer le contenu de configuration.
Une configuration de nœuds signée est vérifiée localement sur un nœud géré par l’agent DSC, garantissant ainsi que la configuration appliquée au nœud provient d’une source autorisée.

> [!NOTE]
> Un fichier de configuration de nœuds ne doit pas être supérieur à 1 Mo pour pouvoir être importé dans Azure Automation.

Pour plus d’informations sur la façon de signer des configurations de nœud, consultez [Améliorations apportées à WMF 5.1 - Comment signer la configuration et le module](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Compilation d’une configuration dans Windows PowerShell

Le processus de compilation des configurations DSC dans Windows PowerShell est inclus dans la documentation DSC PowerShell [Écrire, compiler et appliquer une configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Vous pouvez l’exécuter à partir d’une station de travail de développeur ou dans un service de build tel qu’[Azure DevOps](https://dev.azure.com).

Les fichiers MOF générés par la compilation de la configuration peuvent ensuite être importés directement dans le service Azure State Configuration.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importation d’une configuration de nœuds dans le portail Azure

1. À partir de votre compte Automation, cliquez sur **State configuration (DSC)** sous **Gestion de la configuration**.
1. Dans la page **State Configuration (DSC)** , cliquez sur l’onglet **Configurations**, puis sur **+ Ajouter**.
1. Dans la page **Importer**, cliquez sur l’icône de dossier en regard de la zone de texte **Fichier de configuration de nœuds** pour rechercher un fichier de configuration de nœuds (MOF) sur votre ordinateur local.

   ![Rechercher un fichier local](./media/automation-dsc-compile/import-browse.png)

1. Entrez un nom dans la zone de texte **Nom de la configuration**. Ce nom doit correspondre au nom de la configuration à partir de laquelle la configuration de nœuds a été compilée.
1. Cliquez sur **OK**.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Importation d’une configuration de nœuds avec Azure PowerShell

Vous pouvez utiliser l’applet de commande [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) pour importer une configuration de nœuds dans votre compte Automation.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer, consultez [Prise en main d’Azure Automation State Configuration](automation-dsc-getting-started.md)
- Pour savoir comment compiler des configurations DSC pour les affecter à des nœuds cibles, consultez [Compilation de configurations dans Azure Automation State Configuration](automation-dsc-compile.md)
- Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Applets de commande d’Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Pour obtenir des informations sur les prix, consultez [Tarification d’Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Pour voir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md)
