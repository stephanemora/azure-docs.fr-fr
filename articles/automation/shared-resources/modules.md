---
title: Gérer les Modules dans Azure Automation
description: Cet article explique comment gérer les modules dans Azure Automation
services: automation
ms.service: automation
ms.subservice: shared-resources
author: georgewallace
ms.author: gwallace
ms.date: 03/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa7f5d3fb38eb1dbca51dec9b73dca3c998436aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60500348"
---
# <a name="manage-modules-in-azure-automation"></a>Gérer les Modules dans Azure Automation

Azure Automation fournit la capacité d’importer des modules PowerShell dans votre compte Automation à utiliser par les runbooks basé sur PowerShell. Ces modules peuvent être des modules personnalisés que vous avez créé à partir de PowerShell Gallery, ou les modules AzureRM et Az pour Azure.

## <a name="import-modules"></a>Modules d’importation

Il existe plusieurs manières que vous pouvez importer un module dans votre compte Automation. Les sections suivantes montrent les différentes façons d’importer un module.

> [!NOTE]
> Le chemin d’accès maximale d’un fichier dans un module destiné à être utilisé dans Azure Automation est de 140 caractères. N’importe quel chemin d’accès de plus de 140 caractères ne sera pas pu être importés dans la session PowerShell avec `Import-Module`.

### <a name="powershell"></a>PowerShell

Vous pouvez utiliser la [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) pour importer un module dans votre compte Automation. L’applet de commande prend une url à un package zip de module.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Portail Azure

Dans le portail Azure, accédez à votre compte Automation et sélectionnez **Modules** sous **ressources partagées**. Cliquez sur **+ ajouter un module**. Sélectionnez un **.zip** fichier qui contient votre module et cliquez sur **Ok** de démarrage pour le processus d’importation.

### <a name="powershell-gallery"></a>PowerShell Gallery

Modules à partir de la galerie PowerShell peuvent être importés à partir de la [PowerShell Gallery](https://www.powershellgallery.com) directement ou à partir de votre compte Automation.

Pour importer un module à partir de PowerShell Gallery, accédez à https://www.powershellgallery.com et recherchez le module que vous souhaitez importer. Cliquez sur **déployer sur Azure Automation** sur le **Azure Automation** onglet sous **Options d’Installation**. Cette action ouvre le portail Azure. Sur le **importation** page, sélectionnez votre compte Automation, puis cliquez sur **OK**.

![Module d’importation de PowerShell Gallery](../media/modules/powershell-gallery.png)

Vous pouvez également importer des modules à partir de la galerie PowerShell directement à partir de votre compte Automation. Dans votre compte Automation, sélectionnez **Modules** sous **ressources partagées**. Dans la page modules, cliquez sur **parcourir la galerie**. Cela ouvre le **parcourir la galerie** page. Vous pouvez utiliser cette page pour rechercher dans la galerie PowerShell pour un module. Sélectionnez le module que vous souhaitez importer, cliquez sur **importer**. Sur le **importer** , cliquez sur **OK** pour démarrer le processus d’importation.

![Importation de PowerShell Gallery à partir du portail Azure](../media/modules/gallery-azure-portal.png)

## <a name="internal-cmdlets"></a>Applets de commande interne

Voici une liste des applets de commande interne `Orchestrator.AssetManagement.Cmdlets` module est importé dans chaque compte Automation. Ces applets de commande sont accessibles dans vos runbooks et les configurations DSC et vous permettent d’interagir avec vos ressources au sein de votre compte Automation. En outre, les applets de commande internes vous permettent de récupérer des secrets à partir de chiffré **Variable** valeurs, **informations d’identification**et chiffrées **connexion** champs. Les applets de commande Azure PowerShell ne sont pas en mesure de récupérer ces informations secrètes. Ces applets de commande ne nécessitent pas à se connecter à Azure implicitement lors de leur utilisation. Cela est utile pour les scénarios où vous avez une connexion, comme un compte d’identification que vous devez utiliser pour s’authentifier auprès d’Azure.

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

Vous pouvez fournir un personnalisé [type de connexion](../automation-connections.md) que vous pouvez utiliser dans votre compte Automation en ajoutant un fichier facultatif à votre module. Ce fichier est un fichier de métadonnées spécifiant un type de connexion Azure Automation à utiliser avec les applets de commande du module dans votre compte Automation. Pour ce faire, vous devez tout d’abord connaître la création d’un module PowerShell. Pour plus d’informations sur la création de module, consultez [comment écrire un Module de Script PowerShell](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Utiliser une connexion personnalisée dans le portail Azure](../media/modules/connection-create-new.png)

Pour ajouter un type de connexion Azure Automation, votre module doit contenir un fichier portant le nom `<ModuleName>-Automation.json` qui spécifie les propriétés de type de connexion. Il s’agit d’un fichier json, qui est placé dans le dossier de module de votre fichier .zip compressé. Ce fichier contient les champs d’une connexion qui est nécessaire pour se connecter au système ou au service qui que représente le module. Cette configuration finit par créer un type de connexion dans Azure Automation. À l’aide de ce fichier, vous pouvez définir les noms de champs, types, et indique si les champs doivent être chiffrées ou facultatif, pour le type de connexion du module. L’exemple suivant est un modèle dans le format de fichier json qui définit une propriété de nom d’utilisateur et mot de passe :

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

## <a name="module-best-practices"></a>Meilleures pratiques du module

Ces modules PowerShell peuvent être importés dans Azure Automation afin de rendre disponibles leurs applets de commande et leurs ressources DSC, les premières pour une utilisation dans des runbooks, les secondes, pour une utilisation au sein des configurations DSC. En coulisse, Azure Automation stocke ces modules et, lors de l’exécution d’un travail de compilation de DSC ou d’une tâche de runbook, les charge dans les bacs à sable (sandbox) Azure Automation où les runbooks s’exécutent et les configurations DSC compilent. Les ressources DSC présentes dans les modules sont également placées automatiquement sur le serveur Pull Automation DSC. Elles peuvent être extraites par des ordinateurs lorsqu’elles appliquent des configurations DSC.

Nous vous recommandons de que prendre en compte les éléments suivants lorsque vous créez un module PowerShell pour une utilisation dans Azure Automation :

* Incluez un résumé, une description et une URI d’aide pour chaque applet de commande dans le module. Dans PowerShell, vous pouvez définir certaines informations d’aide pour les applets de commande, pour que l’utilisateur puisse obtenir de l’aide quant à leur utilisation, avec l’applet de commande **Get-Help** . L’exemple suivant montre comment définir un synopsis et l’URI pour dans un fichier de module .psm1 d’aide :

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

  Fournir ces informations affichent cette aide à l’aide de l’applet de commande **Get-Help** dans la console PowerShell. Cette description s’affiche également dans le portail Azure.

  ![Aide du module d’intégration](../media/modules/module-activity-description.png)

* Si le module se connecte à un service externe, elle doit contenir un [type de connexion](#add-a-connection-type-to-your-module). Chaque applet de commande du module doit pouvoir accueillir un objet de connexion (une instance de ce type de connexion) en tant que paramètre. Les utilisateurs peuvent ainsi mapper les paramètres de la ressource de connexion aux paramètres correspondants de l’applet de commande chaque fois qu’ils appellent une applet de commande. En fonction de l’exemple de runbook ci-dessus, il utilise une ressource de connexion de Contoso exemple appelée ContosoConnection pour accéder aux ressources de Contoso et renvoyer des données à partir du service externe.

  Dans l’exemple suivant, les champs sont mappés aux propriétés nom d’utilisateur et mot de passe d’un `PSCredential` de l’objet, puis passé à l’applet de commande.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Une plus facile et une meilleure approche consisterait à ce comportement passer directement l’objet de connexion à l’applet de commande :

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Vous pouvez activer un comportement, tel que celui de l’exemple précédent pour vos applets de commande, en les autorisant à accepter un objet de connexion directement en tant que paramètre, au lieu d’accepter uniquement des champs de connexion en tant que paramètres. Généralement, vous voudrez disposer d’un jeu de paramètres pour chaque élément, pour qu’un utilisateur n’utilisant pas Azure Automation puisse appeler vos applets de commande sans créer une table de hachage agissant en tant qu’objet de connexion. Le jeu de paramètres `UserAccount`, est utilisé pour passer des propriétés de champ de la connexion. `ConnectionObject` vous permet de transmettre directement via la connexion.

* Définir le type de sortie pour toutes les applets de commande dans le module. La définition d’un type de sortie pour une applet de commande permet à IntelliSense (au moment de la conception) de vous aider à déterminer les propriétés de sortie de l’applet de commande, à utiliser lors de la création. Cela est particulièrement utile lors de la création graphique d’un runbook Azure Automation, où les connaissances au moment de la conception sont essentielles pour une expérience utilisateur conviviale de votre module.

  Cela est possible en ajoutant `[OutputType([<MyOutputType>])]` où MyOutputType est un type valid. Pour en savoir plus sur OutputType, consultez [sur les fonctions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Le code suivant est un exemple d’ajout `OutputType` à une applet de commande :

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

* Rendez toutes les applets de commande dans le module sans état. Plusieurs tâches de runbook peuvent exécuter simultanément dans le même AppDomain et le même processus et le bac à sable. S’il existe tout état partagé sur ces niveaux, travaux peuvent affecter l’autre. Ce comportement peut entraîner intermittentes et difficiles à diagnostiquer les problèmes.  Voici un exemple de procédure à ne pas suivre.

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

* Le module doit être entièrement contenu dans un package en mesure de xcopy. Les modules Azure Automation sont distribués aux bacs à sable Automation lorsque les runbooks doivent s’exécuter. Les modules doivent fonctionner indépendamment de l’hôte sur lequel ils s’exécutent. Vous devez être en mesure de compresser un package de module pour le déplacer et le faire fonctionner normalement lorsqu’il est importé dans l’environnement PowerShell d’un autre hôte. Pour ce faire, le module ne doit dépendre d’aucun fichier situé en dehors du dossier de module. Ce dossier est le dossier qui est compressé pendant l’importation du module dans Azure Automation. Le module ne doit pas dépendre non plus de paramètres de Registre uniques sur un hôte, tels que les paramètres définis à l’installation d’un produit. Tous les fichiers dans le module doivent avoir un chemin d’accès moins de 140 caractères. Tous les chemins de plus de 140 caractères entraîne des problèmes d’importation de votre runbook. Si cette bonne pratique n’est pas respectée, le module ne sera pas utilisable dans Azure Automation.  

* Si vous référencez [les modules Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) dans votre module, vérifiez que vous ne référencez pas également `AzureRM`. Le module `Az` ne peut pas être utilisé conjointement avec les modules `AzureRM`. `Az` est pris en charge dans les runbooks, mais n’est pas importé par défaut. Pour en savoir plus sur les modules `Az` et les points à prendre en considération, consultez [Prise en charge de modules Az dans Azure Automation](../az-modules.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création de modules PowerShell, consultez [Writing a Windows PowerShell Module (Écriture d’un module Windows PowerShell)](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)