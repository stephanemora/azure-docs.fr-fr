---
title: Gérer les modules dans Azure Automation
description: Cet article explique comment gérer les modules dans Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930436"
---
# <a name="manage-modules-in-azure-automation"></a>Gérer les modules dans Azure Automation

Azure Automation permet d’importer les modules PowerShell dans votre compte Automation que les runbooks PowerShell doivent utiliser. Ces modules peuvent être des modules personnalisés que vous avez créés (à partir de PowerShell Gallery) ou les modules AzureRM et Az pour Azure. Quand vous créez un compte Automation, certains modules sont importés par défaut.

## <a name="import-modules"></a>Modules d’importation

Il existe plusieurs façons d’importer un module dans votre compte Automation. Les sections suivantes décrivent les différentes méthodes d’importation de module.

> [!NOTE]
> Dans un module, le chemin d’un fichier à utiliser dans Azure Automation ne doit pas dépasser 140 caractères. Tout chemin de plus de 140 caractères ne pourra pas être importé dans la session PowerShell avec `Import-Module`.

### <a name="powershell"></a>PowerShell

Vous pouvez utiliser [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) pour importer un module dans votre compte Automation. L’applet de commande prend l’URL d’un package zip de module.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Vous pouvez également utiliser la même cmdlet pour importer directement un module à partir de PowerShell Gallery. Veillez à obtenir **ModuleName** et **ModuleVersion** à partir de [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Portail Azure

Sur le portail Azure, accédez à votre compte Automation et sélectionnez **Modules** sous **Ressources partagées**. Cliquez sur **+ Ajouter un module**. Sélectionnez le fichier **.zip** qui contient votre module, puis cliquez sur **OK** pour lancer l’importation.

### <a name="powershell-gallery"></a>PowerShell Gallery

Les modules issus de PowerShell Gallery peuvent être importés directement à partir de [PowerShell Gallery](https://www.powershellgallery.com) ou de votre compte Automation.

Pour importer un module à partir de PowerShell Gallery, accédez à https://www.powershellgallery.com et recherchez le module que vous voulez importer. Cliquez sur **Déployer sur Azure Automation** sous l’onglet **Azure Automation** sous **Options d’installation**. Cette action ouvre le portail Azure. Dans la page **Importer**, sélectionnez votre compte Automation, puis cliquez sur **OK**.

![Importation de module PowerShell Gallery](../media/modules/powershell-gallery.png)

Vous pouvez aussi importer des modules PowerShell Gallery directement à partir de votre compte Automation. Dans votre compte Automation, sélectionnez **Modules** sous **Ressources partagées**. Dans la page des modules, cliquez sur **Parcourir la galerie**, puis recherchez un module dans PowerShell Gallery. Sélectionnez le module que vous souhaitez importer, puis cliquez sur **Importer**. Dans la page **Importer**, cliquez sur **OK** pour lancer l’importation.

![Importation PowerShell Gallery à partir du portail Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Supprimer des modules

Si vous rencontrez des difficultés avec un module ou si vous avez besoin de revenir à une version précédente d’un module, vous pouvez le supprimer de votre compte Automation. Vous ne pouvez pas supprimer la version d’origine des [modules par défaut](#default-modules) qui sont importés lors de la création d'un compte Automation. Si le module que vous souhaitez supprimer est une version plus récente de l’un des [modules par défaut](#default-modules) installés, la version installée avec votre compte Automation est restaurée. Sinon, tout module que vous supprimez de votre compte Automation est bel et bien supprimé.

### <a name="azure-portal"></a>Portail Azure

Sur le portail Azure, accédez à votre compte Automation et sélectionnez **Modules** sous **Ressources partagées**. Sélectionnez le module que vous souhaitez supprimer. Dans la page **Module**, sélectionnez **Supprimer**. Si ce module fait partie des [modules par défaut](#default-modules), la version qui était présente au moment où le compte Automation a été créé est restaurée.

### <a name="powershell"></a>PowerShell

Pour supprimer un module via PowerShell, exécutez la commande suivante :

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Applets de commande internes

La liste ci-dessous recense les applets de commande du module interne `Orchestrator.AssetManagement.Cmdlets` qui est importé dans chaque compte Automation. Ces applets de commande sont accessibles dans vos runbooks et les configurations DSC et vous permettent d’interagir avec les ressources de votre compte Automation. Par ailleurs, les applets de commande internes vous permettent de récupérer des secrets à partir des valeurs de **Variable** chiffrées, des **informations d’identification** et des champs **Connexion** chiffrés. Les applets de commande Azure PowerShell ne peuvent pas récupérer ces secrets. Ces applets de commande ne vous obligent pas à vous connecter implicitement à Azure quand vous les utilisez, par exemple à utiliser un compte d’identification pour vous authentifier auprès d’Azure.

>[!NOTE]
>Ces cmdlets internes sont disponibles sur un Runbook Worker hybride Windows, mais pas sur un Runbook Worker hybride Linux. Utilisez les modules [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) ou [Az](../az-modules.md) correspondants pour les runbooks exécutés directement sur l’ordinateur ou sur des ressources de votre environnement. 
>

|Name|Description|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Ajouter un type de connexion à votre module

Vous pouvez indiquer un [type de connexion](../automation-connections.md) personnalisé à utiliser dans votre compte Automation en ajoutant un fichier facultatif à votre module. Il s’agit d’un fichier de métadonnées qui spécifie le type de connexion Azure Automation à utiliser avec les applets de commande du module dans votre compte Automation. Pour cela, vous devez dans un premier temps savoir comment créer un module PowerShell. Pour plus d’informations sur la création de modules, consultez [Guide pratique pour écrire un module de script PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Utiliser une connexion personnalisée sur le portail Azure](../media/modules/connection-create-new.png)

Pour ajouter un type de connexion Azure Automation, votre module doit contenir un fichier nommé `<ModuleName>-Automation.json` qui spécifie les propriétés du type de connexion. Le fichier json est placé dans le dossier module de votre fichier .zip compressé. Ce fichier contient les champs d’une connexion nécessaire pour se connecter au système ou au service représenté par le module. La configuration se termine en créant un type de connexion dans Azure Automation. Avec ce fichier, vous pouvez définir les noms de champ, les types et indiquer si les champs doivent être chiffrés ou facultatifs, pour le type de connexion du module. L’exemple suivant est un modèle au format de fichier json qui définit une propriété username et password :

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="module-best-practices"></a>Bonnes pratiques en matière de modules

Ces modules PowerShell peuvent être importés dans Azure Automation afin de rendre disponibles leurs applets de commande et leurs ressources DSC, les premières pour une utilisation dans des runbooks, les secondes, pour une utilisation au sein des configurations DSC. En coulisse, Azure Automation stocke ces modules et, lors de l’exécution d’un travail de compilation de DSC ou d’une tâche de runbook, les charge dans les bacs à sable (sandbox) Azure Automation où les runbooks s’exécutent et les configurations DSC compilent. Les ressources DSC présentes dans les modules sont également placées automatiquement sur le serveur Pull Automation DSC. Elles peuvent être extraites par des ordinateurs lorsqu’elles appliquent des configurations DSC.

Nous vous recommandons de tenir compte des points suivants quand vous créez un module PowerShell en vue de l’utiliser dans Azure Automation :

* N’incluez PAS de dossier version dans le package. zip.  Si cette question s’avère moins problématique dans le cas des runbooks, elle peut occasionner un problème avec le service State Configuration.  En effet, Azure Automation créera automatiquement le dossier version quand le module sera distribué aux nœuds gérés par DSC, et s’il existe un dossier version, vous vous retrouverez avec deux instances.  Exemple de structure de dossiers pour un module DSC :

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Incluez un résumé, une description et une URI d’aide pour chaque applet de commande dans le module. Dans PowerShell, vous pouvez définir certaines informations d’aide pour les applets de commande, pour que l’utilisateur puisse obtenir de l’aide quant à leur utilisation, avec l’applet de commande **Get-Help** . L’exemple suivant montre comment définir un résumé et un URI d’aide dans un fichier de module .psm1 :

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Fournir ces informations affichent cette aide à l’aide de l’applet de commande **Get-Help** dans la console PowerShell. Cette description s’affiche aussi sur le portail Azure.

  ![Aide du module d’intégration](../media/modules/module-activity-description.png)

* Si le module se connecte à un service externe, il doit contenir un [type de connexion](#add-a-connection-type-to-your-module). Chaque applet de commande du module doit pouvoir accueillir un objet de connexion (une instance de ce type de connexion) en tant que paramètre. Les utilisateurs mappent les paramètres de la ressource de connexion aux paramètres correspondants de l’applet de commande chaque fois qu’ils appellent une applet de commande. Reposant sur l’exemple de runbook ci-dessus, il utilise un exemple de ressource de connexion Contoso appelée ContosoConnection pour accéder aux ressources Contoso et retourner des données du service externe.

  Dans l’exemple suivant, les champs sont mappés aux propriétés UserName et Password d’un objet `PSCredential` et sont transmis à l’applet de commande.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Un moyen plus simple et plus efficace de se rapprocher de ce comportement consiste à transmettre directement l’objet de connexion à l’applet de commande :

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Vous pouvez activer un comportement, tel que celui de l’exemple précédent pour vos applets de commande, en les autorisant à accepter un objet de connexion directement en tant que paramètre, au lieu d’accepter uniquement des champs de connexion en tant que paramètres. Généralement, vous voudrez disposer d’un jeu de paramètres pour chaque élément, pour qu’un utilisateur n’utilisant pas Azure Automation puisse appeler vos applets de commande sans créer une table de hachage agissant en tant qu’objet de connexion. Le jeu de paramètres `UserAccount` permet de transmettre les propriétés de champ de connexion. `ConnectionObject` permet de transmettre directement la connexion.

* Définissez le type de sortie pour toutes les applets de commande du module. La définition d’un type de sortie pour une applet de commande permet à IntelliSense (au moment de la conception) de vous aider à déterminer les propriétés de sortie de l’applet de commande, à utiliser lors de la création. Cela est particulièrement utile lors de la création graphique d’un runbook Azure Automation, où les connaissances au moment de la conception sont essentielles pour une expérience utilisateur conviviale de votre module.

Ajoutez `[OutputType([<MyOutputType>])]` là où MyOutputType est un type valide. Pour en savoir plus sur OutputType, consultez [À propos des fonctions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Le code suivant montre comment ajouter `OutputType` à une applet de commande :

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Type de sortie de Runbook graphique](../media/modules/runbook-graphical-module-output-type.png)

  Ce comportement ressemble à la fonctionnalité de « frappe au kilomètre » de la sortie d’une applet de commande dans PowerShell ISE, sans avoir à exécuter celle-ci.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* Rendez toutes les applets de commande dans le module sans état. Plusieurs tâches de runbook peuvent s’exécuter en même temps dans le même domaine d’application et les mêmes processus et bac à sable. Si un état est partagé sur ces niveaux, les tâches peuvent s’affecter mutuellement. Ce comportement peut occasionner des problèmes intermittents et difficiles à diagnostiquer.  Voici un exemple de procédure à ne pas suivre.

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

* Le module doit être entièrement contenu dans un package pouvant faire l’objet d’une copie xcopy. Les modules Azure Automation sont distribués aux bacs à sable Automation quand des runbooks doivent s’exécuter. Les modules doivent fonctionner indépendamment de l’hôte sur lequel ils s’exécutent. Vous devez être en mesure de compresser un package de module pour le déplacer et le faire fonctionner normalement lorsqu’il est importé dans l’environnement PowerShell d’un autre hôte. Pour ce faire, le module ne doit dépendre d’aucun fichier situé en dehors du dossier de module. Ce dossier est le dossier qui est compressé pendant l’importation du module dans Azure Automation. Le module ne doit pas dépendre non plus de paramètres de Registre uniques sur un hôte, tels que les paramètres définis à l’installation d’un produit. Tous les fichiers du module doivent avoir un chemin de moins de 140 caractères. Tout chemin de plus de 140 caractères entraînera des problèmes au moment d’importer votre runbook. Si cette bonne pratique n’est pas respectée, le module ne sera pas utilisable dans Azure Automation.  

* Si vous référencez [les modules Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) dans votre module, vérifiez que vous ne référencez pas également `AzureRM`. Le module `Az` ne peut pas être utilisé conjointement avec les modules `AzureRM`. `Az` est pris en charge dans les runbooks, mais n’est pas importé par défaut. Pour en savoir plus sur les modules `Az` et les points à prendre en considération, consultez [Prise en charge de modules Az dans Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Modules par défaut

Le tableau suivant liste les modules qui sont importés par défaut au moment de créer un compte Automation. Des versions plus récentes des modules listés ci-dessous peuvent être importées, mais la version d’origine ne peut pas être supprimée de votre compte Automation même si vous supprimez une version plus récente de ces modules.

|Nom du module|Version|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création de modules PowerShell, consultez [Writing a Windows PowerShell Module (Écriture d’un module Windows PowerShell)](/powershell/scripting/developer/windows-powershell)
