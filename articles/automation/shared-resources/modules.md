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
ms.openlocfilehash: 605d1bc72406a9aeecc9273f9bd2d7fd2b30ab11
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618691"
---
# <a name="manage-modules-in-azure-automation"></a>Gérer les modules dans Azure Automation

Vous pouvez importer les modules PowerShell dans Azure Automation afin de rendre disponibles leurs applets de commande dans des Runbooks, et leurs ressources DSC dans des configurations DSC. Azure Automation stocke ces modules en arrière-plan. Lors de l’exécution d’un travail de compilation de DSC ou d’une tâche de Runbook, Automation les charge dans les bacs à sable (sandbox) Azure Automation où les Runbooks s’exécutent et les configurations DSC compilent. Les ressources DSC présentes dans les modules sont également placées automatiquement sur le serveur Pull Automation DSC. Les machines peuvent les tirer lorsqu’elles appliquent des configurations DSC.

Les modules utilisés dans Azure Automation peuvent être des modules personnalisés que vous avez créés à partir de PowerShell Gallery, ou les modules AzureRM et Az pour Azure. Quand vous créez un compte Automation, certains modules sont importés par défaut.

## <a name="default-modules"></a>Modules par défaut

Le tableau suivant liste les modules importés par défaut au moment de créer un compte Automation. Automation peut importer des versions plus récentes de ces modules. Toutefois, vous ne pouvez pas supprimer la version originale de votre compte Automation, même si vous supprimez une version plus récente.

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

## <a name="internal-cmdlets"></a>Applets de commande internes

Le tableau ci-dessous recense les applets de commande du module interne `Orchestrator.AssetManagement.Cmdlets` qui est importé dans chaque compte Automation. Ces applets de commande sont accessibles dans vos Runbooks et les configurations DSC et vous permettent d’interagir avec les ressources de votre compte Automation. Par ailleurs, les applets de commande internes vous permettent de récupérer des secrets à partir des variables chiffrées, des informations d’identification et des connexions chiffrées. Les applets de commande Azure PowerShell ne peuvent pas récupérer ces secrets. Ces applets de commande ne vous obligent pas à vous connecter implicitement à Azure quand vous les utilisez, par exemple lorsque vous utilisez un compte d’identification pour vous authentifier auprès d’Azure.

>[!NOTE]
>Ces applets de commande internes sont disponibles sur un Runbook Worker hybride Windows, mais pas sur un Runbook Worker hybride Linux. Utilisez les applets de commande [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) ou [Module Az](../az-modules.md) correspondants pour les Runbooks exécutés directement sur l’ordinateur ou sur des ressources de votre environnement. 

|Nom|Description|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="importing-modules"></a>Importation des modules

Il existe plusieurs façons d’importer un module dans votre compte Automation. Les sections suivantes décrivent les différentes méthodes d’importation de module.

> [!NOTE]
> Dans un module, la longueur maximale du chemin d’un fichier ne doit pas dépasser 140 caractères. Automation ne peut pas importer un fichier dont la taille de chemin dépasse 140 caractères dans la session PowerShell avec `Import-Module`.

### <a name="import-modules-in-azure-portal"></a>Importer des modules dans le portail Azure

Pour importer un module dans le portail Azure :

1. Accédez à votre compte Automation.
2. Sélectionnez **Modules** sous **Ressources partagées**.
3. Cliquez sur **Ajouter un module**. 
4. Sélectionnez le fichier **.zip** qui contient votre module.
5. Cliquez sur **OK** pour démarrer le processus d’importation.

### <a name="import-modules-using-powershell"></a>Importer des modules à l’aide de PowerShell

Vous pouvez utiliser l’applet de commande [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) pour importer un module dans votre compte Automation. L’applet de commande prend l’URL d’un package .zip de module.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Vous pouvez également utiliser la même cmdlet pour importer directement un module à partir de PowerShell Gallery. Veillez à obtenir `ModuleName` et `ModuleVersion` à partir de [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzureRmAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```
### <a name="import-modules-from-powershell-gallery"></a>Importer des modules à partir de PowerShell Gallery

Vous pouvez importer des modules [PowerShell Gallery](https://www.powershellgallery.com) directement à partir de la galerie ou de votre compte Automation.

Pour importer un module directement à partir de PowerShell Gallery :

1. Accédez à https://www.powershellgallery.com et recherchez le module à importer.
2. Cliquez sur **Déployer sur Azure Automation** sous l’onglet **Azure Automation** sous **Options d’installation**. Cette action ouvre le portail Azure. 
3. Dans la page Importer, sélectionnez votre compte Automation et cliquez sur **OK**.

![Importation de module PowerShell Gallery](../media/modules/powershell-gallery.png)

Pour importer un module PowerShell Gallery directement à partir de votre compte Automation :

1. Sélectionnez **Modules** sous **Ressources partagées**. 
2. Dans la page Modules, cliquez sur **Parcourir la galerie**, puis recherchez un module dans la galerie. 
3. Sélectionnez le module à importer, puis cliquez sur **Importer**. 
4. Dans la page Importer, cliquez sur **OK** pour lancer l’importation.

![Importation PowerShell Gallery à partir du portail Azure](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Suppression de modules

Si vous rencontrez des difficultés avec un module ou si vous avez besoin de revenir à une version précédente d’un module, vous pouvez le supprimer de votre compte Automation. Vous ne pouvez pas supprimer les versions d’origine des [modules par défaut](#default-modules) qui sont importés au moment de créer un compte Automation. Si le module à supprimer est une version plus récente de l’un des [modules par défaut](#default-modules), la version installée avec votre compte Automation est restaurée. Sinon, tout module que vous supprimez de votre compte Automation est bel et bien supprimé.

### <a name="delete-modules-in-azure-portal"></a>Supprimer des modules dans le portail Azure

Pour supprimer un module dans le portail Azure :

1. Accédez à votre compte Automation et sélectionnez **Modules** sous **Ressources partagées**. 
2. Sélectionnez le module que vous souhaitez supprimer. 
3. Dans la page **Module**, sélectionnez **Supprimer**. Si ce module fait partie des [modules par défaut](#default-modules), la version présente au moment où le compte Automation a été créé est restaurée.

### <a name="delete-modules-using-powershell"></a>Supprimer des modules à l’aide de PowerShell

Pour supprimer un module via PowerShell, exécutez la commande suivante :

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```
## <a name="adding-a-connection-type-to-your-module"></a>Ajout d’un type de connexion à votre module

Vous pouvez indiquer un [type de connexion](../automation-connections.md) personnalisé à utiliser dans votre compte Automation en ajoutant un fichier facultatif de métadonnées à votre module. Il s’agit d’un fichier qui spécifie le type de connexion Azure Automation à utiliser avec les applets de commande du module dans votre compte Automation. Pour cela, vous devez dans un premier temps savoir comment créer un module PowerShell. Voir [Guide pratique pour écrire un module de script PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Utiliser une connexion personnalisée sur le portail Azure](../media/modules/connection-create-new.png)

Le fichier qui spécifie les propriétés du type de connexion est nommé **&lt;ModuleName&gt;-Automation.json** et se trouve dans le dossier du module de votre fichier **.zip** compressé. Ce fichier contient les champs d’une connexion nécessaire pour se connecter au système ou au service représenté par le module. La configuration permet de créer un type de connexion dans Azure Automation. Avec ce fichier, vous pouvez définir les noms de champ, les types et indiquer si les champs sont chiffrés ou facultatifs, pour le type de connexion du module. L’exemple suivant est un modèle au format de fichier **.json** qui définit des propriétés username et password :

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

## <a name="best-practices-for-authoring-modules"></a>Meilleures pratiques pour la création de modules

Nous vous recommandons de tenir compte des points mentionnés dans cette section quand vous créez un module PowerShell en vue de l’utiliser dans Azure Automation.

### <a name="version-folder"></a>Dossier de version

N’incluez PAS de dossier version dans le package **.zip** de votre module.  Si cette question s’avère moins problématique dans le cas des Runbooks, elle peut occasionner un problème avec le service State Configuration. Azure Automation crée automatiquement le dossier de version lorsque le module est distribué aux nœuds gérés par DSC. Si un dossier de version existe, vous obtenez deux instances. Voici un exemple de structure de dossiers pour un module DSC :

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informations d'aide

Incluez un résumé, une description et un URI d’aide pour chaque applet de commande dans votre module. Dans PowerShell, vous pouvez définir des informations d’aide pour les applets de commande à l’aide de l’applet de commande `Get-Help`. L’exemple suivant montre comment définir un résumé et un URI d’aide dans un fichier de module **.psm1** :

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

  Fournir ces informations permet d’afficher le texte d’aide via l’applet de commande `Get-Help` dans la console PowerShell. Ce texte s’affiche aussi sur le portail Azure.

  ![Aide du module d’intégration](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Type de connexion

Si le module se connecte à un service externe, définissez un [type de connexion](#adding-a-connection-type-to-your-module). Chaque applet de commande du module devrait accepter un objet de connexion (une instance de ce type de connexion) en tant que paramètre. Les utilisateurs mappent les paramètres de la ressource de connexion aux paramètres correspondants de l’applet de commande chaque fois qu’ils appellent une applet de commande. Reposant sur l’exemple de Runbook ci-dessus, il utilise un exemple de ressource de connexion Contoso appelée `ContosoConnection` pour accéder aux ressources Contoso et retourner des données du service externe.

  Dans l’exemple suivant, les champs sont mappés aux propriétés `UserName` et `Password` d’un objet `PSCredential`, puis transmis à l’applet de commande.

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

  Vous pouvez activer un comportement similaire pour vos applets de commande en les autorisant à accepter un objet de connexion directement en tant que paramètre, au lieu d’accepter uniquement des champs de connexion en tant que paramètres. Généralement, vous voudrez disposer d’un jeu de paramètres pour chaque élément, pour qu’un utilisateur n’utilisant pas Azure Automation puisse appeler vos applets de commande sans créer une table de hachage agissant en tant qu’objet de connexion. Le jeu de paramètres `UserAccount` permet de transmettre les propriétés de champ de connexion. `ConnectionObject` permet de transmettre directement la connexion.

### <a name="output-type"></a>Type de sortie

Définissez le type de sortie pour toutes les applets de commande de votre module. La définition d’un type de sortie pour une applet de commande permet à IntelliSense (au moment de la conception) de vous aider à déterminer les propriétés de sortie de l’applet de commande, à utiliser lors de la création. Cette approche est particulièrement utile lors de la création graphique d’un Runbook Azure Automation, où les connaissances au moment de la conception sont essentielles pour une expérience utilisateur conviviale de votre module.

Ajoutez `[OutputType([<MyOutputType>])]` où `MyOutputType` est un type valide. Pour en savoir plus sur `OutputType`, consultez [À propos des fonctions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Le code suivant montre comment ajouter `OutputType` à une applet de commande :

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

### <a name="cmdlet-state"></a>État de l’applet de commande

Rendez toutes les applets de commande de votre module sans état. Plusieurs tâches de Runbook peuvent s’exécuter en même temps dans le même domaine `AppDomain` et les mêmes processus et bac à sable. Si un état est partagé sur ces niveaux, les tâches peuvent s’affecter mutuellement. Ce comportement peut occasionner des problèmes intermittents et difficiles à diagnostiquer. Voici un exemple de ce qu’il ne faut PAS faire :

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

### <a name="module-dependency"></a>Dépendance de module

Vérifiez que le module est entièrement contenu dans un package pouvant faire l’objet d’une copie xcopy. Les modules Azure Automation sont distribués aux bacs à sable Automation quand des Runbooks doivent s’exécuter. Les modules doivent fonctionner indépendamment de l’hôte qui les exécute. 

Vous devez être en mesure de compresser un package de module pour le déplacer et le faire fonctionner normalement lorsqu’il est importé dans l’environnement PowerShell d’un autre hôte. Pour cela, assurez-vous que le module ne dépend pas de fichiers situés en dehors du dossier du module compressé lorsque le module est importé dans Azure Automation. 

Votre module ne doit pas dépendre de paramètres de registre uniques sur un hôte, par exemple des paramètres définis lors de l’installation d’un produit. 

Assurez-vous que les chemins d'accès de tous les fichiers du module comportent moins de 140 caractères. Tout chemin de plus de 140 caractères entraîne des problèmes au moment d’importer vos Runbooks. Si vous ne respectez pas cette meilleure pratique, le module n’est pas utilisable dans Azure Automation.  

### <a name="references-to-azurerm-and-az"></a>Références à AzureRM et à Az

Si vous référencez le [module Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) dans votre module, vérifiez que vous ne référencez pas également AzureRM. Vous ne pouvez pas utiliser le module Az conjointement avec le module AzureRM. Az est pris en charge dans les Runbooks, mais n’est pas importé par défaut. Pour en savoir plus sur les modules Az et les points à prendre en considération, consultez [Prise en charge de modules Az dans Azure Automation](../az-modules.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création de modules PowerShell, consultez [Writing a Windows PowerShell Module (Écriture d’un module Windows PowerShell)](/powershell/scripting/developer/windows-powershell).
