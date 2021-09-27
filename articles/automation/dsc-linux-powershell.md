---
title: Appliquer Azure Automation State Configuration Linux à l’aide de PowerShell
description: Cet article vous explique comment configurer une machine virtuelle Linux à un état souhaité à l’aide d’Azure Automation State Configuration avec PowerShell.
ms.topic: conceptual
services: automation
ms.subservice: dsc
ms.date: 08/31/2021
ms.openlocfilehash: 600fdfafa64e0827ac023ae01810ec54bdaa95fe
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438495"
---
# <a name="configure-linux-desired-state-with-azure-automation-state-configuration-using-powershell"></a>Configurer l’état souhaité Linux avec Azure Automation State Configuration à l’aide de PowerShell

Dans ce tutoriel, vous allez appliquer une configuration d’état Azure Automation avec PowerShell à une machine virtuelle Linux Azure pour vérifier si elle est conforme à l’état souhaité. L’état souhaité consiste à déterminer si le service apache2 est présent sur le nœud.

Azure Automation State Configuration vous permet de spécifier des configurations pour vos ordinateurs et de vérifier que leur état ainsi défini perdure dans le temps. Pour plus d’informations sur la fonctionnalité State Configuration, consultez [Vue d’ensemble d’Azure Automation State Configuration](./automation-dsc-overview.md).

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :
> [!div class="checklist"]
> - Intégrer une machine virtuelle Azure Linux à gérer par Azure Automation DSC
> - Composer une configuration
> - Installer le module PowerShell pour Automation
> - Importer une configuration dans Azure Automation
> - Compiler une configuration dans une configuration de nœud
> - Attribuer une configuration de nœud à un nœud géré
> - Modifier le mappage de configuration de nœud
> - Vérifier l’état de conformité d’un nœud géré

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Un compte Azure Automation. Pour en savoir plus sur les comptes Automation, consultez [Vue d’ensemble de l’authentification du compte Automation](./automation-security-overview.md).
- Une machine virtuelle Azure Resource Manager exécutant Ubuntu 18.04 LTS ou version ultérieure. Pour obtenir des instructions sur la création d’une machine virtuelle Linux Azure, consultez [Créer une machine virtuelle Linux dans Azure avec PowerShell](../virtual-machines/windows/quick-create-powershell.md).
- Le [module Az](/powershell/azure/new-azureps-module-az) PowerShell installé sur l’ordinateur que vous utiliserez pour écrire, compiler et appliquer une configuration d’état à une machine virtuelle Linux Azure cible. Vérifiez que vous avez la version la plus récente. Si nécessaire, exécutez `Update-Module -Name Az`.

## <a name="create-a-configuration"></a>Créer une configuration

Passez en revue le code ci-dessous et notez la présence de deux [configurations](/powershell/scripting/dsc/configurations/configurations) de nœud : `IsPresent` et `IsNotPresent`. Cette configuration appelle une ressource dans chaque bloc de nœuds, la [ressource nxPackage](/powershell/scripting/dsc/reference/resources/linux/lnxpackageresource). Cette ressource gère la présence du package **apache2**. Ensuite, dans un éditeur de texte, copiez le code suivant dans un fichier local que vous nommez `LinuxConfig.ps1` :

```powershell
Configuration LinuxConfig
{
    Import-DscResource -ModuleName 'nx'

    Node IsPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Present'
            PackageManager    = 'Apt'
        }
    }

    Node IsNotPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Absent'
        }
    }
}
```

## <a name="sign-in-to-azure"></a>Connexion à Azure

Depuis votre machine, connectez-vous à votre abonnement Azure avec la cmdlet PowerShell [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) et suivez les instructions qui s’affichent à l’écran.

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

## <a name="initialize-variables"></a>Initialiser les variables

Pour améliorer l’efficacité et réduire les chances d’erreur lors de l’exécution des cmdlets, révisez le code PowerShell ci-dessous en fonction des besoins, puis exécutez-le.

| Variable | Valeur |
|---|---|
|$resourceGroup| Remplacez `yourResourceGroup` par le nom réel de votre groupe de ressources.|
|$automationAccount| Remplacez `yourAutomationAccount` par le nom réel de votre compte Automation.|
|$VM| Remplacez `yourVM` par le nom réel de votre machine virtuelle Azure Linux.|
|$configurationName| Laissez tel quel : `LinuxConfig`. Nom de la configuration utilisée dans ce tutoriel.|
|$nodeConfigurationName0|Laissez tel quel : `LinuxConfig.IsNotPresent`. Nom d’une configuration de nœud utilisée dans ce tutoriel.|
|$nodeConfigurationName1|Laissez tel quel : `LinuxConfig.IsPresent`. Nom d’une configuration de nœud utilisée dans ce tutoriel.|
|$moduleName|Laissez tel quel : `nx`. Nom du module PowerShell utilisé pour DSC dans ce tutoriel.|
|$moduleVersion| Récupérez le numéro de version le plus récent pour `nx` à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/nx). Ce tutoriel utilise la version `1.0`.|

```powershell
$resourceGroup = "yourResourceGroup"
$automationAccount = "yourAutomationAccount"
$VM = "yourVM"
$configurationName = "LinuxConfig"
$nodeConfigurationName0 = "LinuxConfig.IsNotPresent"
$nodeConfigurationName1 = "LinuxConfig.IsPresent"
$moduleName = "nx"
$moduleVersion = "1.0"
```

## <a name="install-nx-module"></a>Installer le module nx

Azure Automation utilise un certain nombre de modules PowerShell pour activer des cmdlets de runbooks et de ressources DSC dans des configurations DSC. **nx** est le module contenant les ressources DSC pour Linux. Installez le module **nx** avec la cmdlet [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule). Pour en savoir plus sur les modules, consultez [Gérer les modules dans Azure Automation](./shared-resources/modules.md). Exécutez la commande suivante :

```powershell
New-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName `
    -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

La sortie doit ressembler à ce qui suit :

   :::image type="content" source="media/dsc-linux-powershell/new-azautomationmodule-output.png" alt-text="Sortie de la commande New-AzAutomationModule.":::

Vérifiez l’installation en exécutant la commande suivante :

```powershell
Get-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName 
```

## <a name="import-configuration-to-azure-automation"></a>Importer une configuration dans Azure Automation

Appelez la cmdlet [Import-AzAutomationDscConfiguration](/powershell/module/az.automation/import-azautomationdscconfiguration) pour charger la configuration dans votre compte Automation. Modifiez la valeur de `-SourcePath` en indiquant votre chemin d’accès réel, puis exécutez la commande suivante :

```powershell
Import-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -SourcePath "path\LinuxConfig.ps1" `
   -Published
```

La sortie doit ressembler à ce qui suit :

   :::image type="content" source="media/dsc-linux-powershell/import-azautomationdscconfiguration-output.png" alt-text="Sortie de la commande Import-AzAutomationDscConfiguration.":::

Vous pouvez afficher la configuration à partir de votre compte Automation en exécutant la commande suivante :

```powershell
Get-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $configurationName
```

## <a name="compile-configuration-in-azure-automation"></a>Compiler une configuration dans Azure Automation

Avant de pouvoir appliquer un état souhaité à un nœud, vous devez compiler la configuration définissant cet état dans une ou plusieurs configurations de nœuds.  Appelez la cmdlet [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) pour compiler la configuration `LinuxConfig` dans Azure Automation. Pour plus d’informations sur la compilation, consultez [Compiler des configurations DSC](./automation-dsc-compile.md). Exécutez la commande suivante :

```powershell
Start-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

La sortie doit ressembler à ce qui suit :

   :::image type="content" source="media/dsc-linux-powershell/start-azautomationdsccompilationjob-output.png" alt-text="Sortie de la commande Start-AzAutomationDscCompilationJob.":::

Vous pouvez afficher le travail de compilation à partir de votre compte Automation en exécutant la commande suivante :

```powershell
Get-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

Attendez la fin du travail de compilation avant de poursuivre. La configuration doit être compilée dans une configuration de nœud avant de pouvoir être affectée à un nœud. Exécutez le code suivant pour vérifier l’état toutes les 5 secondes :

```powershell
while ((Get-AzAutomationDscCompilationJob `
         -ResourceGroupName $resourceGroup `
         -AutomationAccountName $automationAccount `
         -ConfigurationName $configurationName).Status -ne "Completed") 
{
   Write-Output "Wait"
   Start-Sleep -Seconds 5
}
Write-Output "Compilation complete"
```

Une fois le travail de compilation terminé, vous pouvez également afficher les métadonnées de configuration du nœud à l’aide de la commande suivante :

```powershell
Get-AzAutomationDscNodeConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount
```

## <a name="register-the-azure-linux-vm-for-an-automation-account"></a>Inscrire la machine virtuelle Azure Linux pour un compte Automation

Inscrivez la machine virtuelle Linux Azure en tant que nœud Desired State Configuration (DSC) pour le compte Azure Automation. La cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) prend en charge uniquement les machines virtuelles exécutant le système d’exploitation Windows. La machine virtuelle Linux Azure doit d’abord être configurée pour DSC. Pour des étapes détaillées, consultez [Prendre en main la fonctionnalité DSC (Desired State Configuration) pour Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

1. Créez un script Python avec la commande d’inscription à l’aide de PowerShell en vue d’une exécution ultérieure sur votre machine virtuelle Linux Azure en exécutant le code suivant :

   ```powershell
    $primaryKey = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).PrimaryKey
    
    $URL = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).Endpoint
    
    Write-Output "sudo /opt/microsoft/dsc/Scripts/Register.py $primaryKey $URL"
   ```

   Ces commandes obtiennent la clé d’accès principale et l’URL du compte Automation et les concatènent pour former la commande d’inscription. Veillez à supprimer tous les retours chariot de la sortie. Cette commande sera utilisée dans une étape ultérieure.

1. Connectez-vous à votre machine virtuelle Linux Azure. Si vous avez utilisé un mot de passe, vous pouvez utiliser la syntaxe ci-dessous. Si vous avez utilisé une paire de clés publique-privée, consultez [SSH sur Linux](./../virtual-machines/linux/mac-create-ssh-keys.md) pour des étapes détaillées. Les autres commandes récupèrent des informations sur les packages qui peuvent être installés, y compris les mises à jour des packages actuellement installés qui sont disponibles, et installent Python.

   ```cmd
   ssh user@IP

   sudo apt-get update
   sudo apt-get install -y python
   ```

1. Installez Open Management Infrastructure (OMI). Pour plus d’informations sur OMI, consultez [Open Management Infrastructure](https://github.com/Microsoft/omi). Vérifiez la dernière [version](https://github.com/Microsoft/omi/releases). Modifiez la version ci-dessous en fonction des besoins, puis exécutez les commandes dans votre session SSH :

   ```bash
   wget https://github.com/microsoft/omi/releases/download/v1.6.8-0/omi-1.6.8-0.ssl_110.ulinux.x64.deb

   sudo dpkg -i ./omi-1.6.8-0.ssl_110.ulinux.x64.deb
   ```

1. Installez PowerShell Desired State Configuration pour Linux. Pour plus d’informations, consultez [DSC sur Linux](https://github.com/microsoft/PowerShell-DSC-for-Linux). Vérifiez la dernière [version](https://github.com/microsoft/PowerShell-DSC-for-Linux/releases). Modifiez la version ci-dessous en fonction des besoins, puis exécutez les commandes dans votre session SSH :

   ```bash
   wget https://github.com/microsoft/PowerShell-DSC-for-Linux/releases/download/v1.2.1-0/dsc-1.2.1-0.ssl_110.x64.deb

   sudo dpkg -i ./dsc-1.2.1-0.ssl_110.x64.deb
   ```

1. Vous pouvez maintenant inscrire le nœud à l’aide du script Python `sudo /opt/microsoft/dsc/Scripts/Register.py <Primary Access Key> <URL>` créé à l’étape 1. Exécutez les commandes dans votre session SSH. La sortie suivante doit ressembler à ce qui suit :

   ```output
   instance of SendConfigurationApply
   {
        ReturnValue=0
   }
   
   ```

1. Vous pouvez vérifier l’inscription dans PowerShell à l’aide de la commande suivante :

   ```powershell
     Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM  
   ```

   La sortie doit ressembler à ce qui suit :

      :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnode-output.png" alt-text="Sortie de la commande Get-AzAutomationDscNode.":::

## <a name="assign-a-node-configuration"></a>Affecter une configuration de nœud

Appelez la cmdlet [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) pour définir le mappage de configurations de nœuds. Exécutez les commandes suivantes :

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $VM

# Set node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName0 `
   -NodeId $node.Id `
   -Force
```

La sortie doit ressembler à ce qui suit :

   :::image type="content" source="media/dsc-linux-powershell/set-azautomationdscnode-output.png" alt-text="Sortie de la commande Set-AzAutomationDscNode.":::

## <a name="modify-the-node-configuration-mapping"></a>Modifier le mappage de configuration de nœud

Appelez la cmdlet [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) pour modifier le mappage de configurations de nœuds. Ici, vous modifiez le mappage de configuration de nœud actuel `LinuxConfig.IsNotPresent` en le définissant sur `LinuxConfig.IsPresent`. Exécutez la commande suivante :

```powershell
# Modify node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName1 `
   -NodeId $node.Id `
   -Force
```

## <a name="check-the-compliance-status-of-a-managed-node"></a>Vérifier l’état de conformité d’un nœud géré

Chaque fois que State Configuration effectue une vérification de cohérence sur un nœud géré, le nœud renvoie un rapport d’état au serveur Pull. L’exemple suivant utilise la cmdlet [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) pour indiquer l’état de conformité d’un nœud géré.

```powershell
Get-AzAutomationDscNodeReport `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeId $node.Id `
   -Latest
```

La sortie doit ressembler à ce qui suit :

   :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnodereport-output.png" alt-text="Sortie de la commande Get-AzAutomationDscNodeReport.":::

Le premier rapport peut ne pas être disponible immédiatement et son affichage peut prendre jusqu’à 30 minutes après l’activation d’un nœud. Pour plus d’informations sur les données de rapport, consultez la page [Utilisation d’un serveur de rapports DSC](/powershell/scripting/dsc/pull-server/reportserver).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Les étapes suivantes vous aideront à supprimer les ressources créées pour ce tutoriel qui ne sont plus nécessaires.

1. Supprimez le nœud DSC de la gestion par un compte Automation. Bien que vous ne puissiez pas inscrire un nœud par le biais de PowerShell, vous pouvez le désinscrire avec PowerShell. Exécutez les commandes suivantes :

    ```powershell
    # Get the ID of the DSC node
    $NodeID = (Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM).Id
    
    Unregister-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Id $NodeID `
       -Force

    # Verify using the same command from Register the Azure Linux VM for an Automation account. A blank response indicates success.
    Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM
    ```

1. Supprimez les métadonnées des configurations de nœuds DSC dans Automation. Exécutez les commandes suivantes :

    ```powershell
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0 `
       -IgnoreNodeMappings `
       -Force
    
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1 `
       -IgnoreNodeMappings `
       -Force

    # Verify using the same command from Compile configuration in Azure Automation.
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0
    
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1
    ```

      La suppression réussie est indiquée par une sortie similaire à ce qui suit : `Get-AzAutomationDscNodeConfiguration : NodeConfiguration LinuxConfig.IsNotPresent not found`.

1. Supprimez la configuration DSC d’Automation. Exécutez la commande suivante :

    ```powershell
    Remove-AzAutomationDscConfiguration `
       -AutomationAccountName $automationAccount `
       -ResourceGroupName $resourceGroup `
       -Name $configurationName `
       -Force

    # Verify using the same command from Import configuration to Azure Automation.
    Get-AzAutomationDscConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $configurationName
    ```

   La suppression réussie est indiquée par une sortie similaire à ce qui suit : `Get-AzAutomationDscConfiguration : Operation returned an invalid status code 'NotFound'`.

1. Supprime le module nx d’Automation. Exécutez la commande suivante :

    ```powershell
    Remove-AzAutomationModule `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $moduleName -Force

    # Verify using the same command from Install nx module.
    Get-AzAutomationModule `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $moduleName
    ```

   La suppression réussie est indiquée par une sortie similaire à ce qui suit : `Get-AzAutomationModule : The module was not found. Module name: nx.`.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appliqué une configuration d’état Azure Automation avec PowerShell à une machine virtuelle Linux Azure pour vérifier si elle est conforme à l’état souhaité. Pour obtenir une explication plus détaillée de la composition de la configuration, consultez :

> [!div class="nextstepaction"]
> [Composer des configurations DSC](./compose-configurationwithcompositeresources.md)