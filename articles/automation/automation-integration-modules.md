---
title: Créer un module d’intégration Azure Automation
description: Ce didacticiel vous guide tout au long des procédures de création et de test de modules d’intégration Azure Automation. Il est complété par un exemple d’utilisation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9122cf5cc908d578d8b781c6fdc49d7b04b0ab58
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990340"
---
# <a name="azure-automation-integration-modules"></a>Modules d’intégration Azure Automation

PowerShell est la technologie fondamentale derrière Azure Automation. Étant donné qu’Azure Automation est basé sur PowerShell, les modules PowerShell sont essentiels pour l’extensibilité d’Azure Automation. Dans cet article, vous allez découvrir les spécificités relatives à l’utilisation dans Azure Automation de modules PowerShell, appelés « modules d’intégration ». Vous allez également explorer les pratiques recommandées qui se rapportent à la création de vos propres modules PowerShell, afin de garantir leur fonctionnement en tant que modules d’intégration au sein d’Azure Automation. 

## <a name="what-is-a-powershell-module"></a>Qu’est-ce qu’un module PowerShell ?

Un module PowerShell est un groupe d’applets de commande PowerShell, comme **Get-Date** ou **Copy-Item**, qui peut être utilisé à partir des éléments suivants :

* Console PowerShell
* Scripts
* Workflows
* Runbooks
* Ressources DSC

Toutes les fonctionnalités de PowerShell sont exposées par le biais des applets de commande et des ressources DSC. Chacune de ces applets de commande et de ces ressources DSC s’appuie sur un module PowerShell ; bon nombre d’entre elles sont fournies avec PowerShell. Ainsi, l’applet de commande **Get-Date** fait partie du module PowerShell `Microsoft.PowerShell.Utility`, et l’applet de commande **Copy-Item** du module PowerShell `Microsoft.PowerShell.Management`. La ressource Package DSC, elle, est intégrée au module PowerShell PSDesiredStateConfiguration. Ces deux modules sont fournis avec PowerShell. De nombreux modules PowerShell ne font pas partie de PowerShell. Ces modules sont distribués avec des produits tiers ou internes, ou sur des sites comme PowerShell Gallery. Ces modules sont utiles car ils simplifient des tâches complexes grâce à la fonctionnalité d’encapsulation.  Vous pouvez en savoir plus sur les [modules PowerShell sur MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>Qu’est-ce qu’un module d’intégration Azure Automation ?

Un module d’intégration n’est pas différent d’un module PowerShell. Il s’agit simplement d’un module PowerShell contenant éventuellement un fichier supplémentaire : un fichier de métadonnées spécifiant un type de connexion Azure Automation à utiliser avec les applets de commande du module dans les Runbooks. Ces modules PowerShell peuvent être importés dans Azure Automation afin de rendre disponibles leurs applets de commande et leurs ressources DSC, les premières pour une utilisation dans des runbooks, les secondes, pour une utilisation au sein des configurations DSC. En coulisse, Azure Automation stocke ces modules et, lors de l’exécution d’un travail de compilation de DSC ou d’une tâche de runbook, les charge dans les bacs à sable (sandbox) Azure Automation où les runbooks s’exécutent et les configurations DSC compilent. Les ressources DSC présentes dans les modules sont également placées automatiquement sur le serveur Pull Automation DSC. Elles peuvent être extraites par des ordinateurs lorsqu’elles appliquent des configurations DSC.  

Nous fournissons un certain nombre de modules Azure PowerShell prêts à l’emploi dans Azure Automation. Il n’en reste pas moins que vous pouvez importer des modules PowerShell pour n’importe quel système, service ou outil que vous voulez intégrer.

> [!NOTE]
> Certains modules sont fournis en tant que **modules globaux** dans le service Automation. Ces modules globaux sont disponibles lorsque vous créez un compte Automation. Nous les mettons parfois à jour ce qui les transfère automatiquement en dehors de votre compte Automation. Si vous ne souhaitez pas qu’ils soient mis à jour automatiquement, vous pouvez toujours importer le même module vous-même. Celui-ci primera sur la version du module global fournie dans le service.

Le format dans lequel vous importez un package de module d’intégration est un fichier compressé portant le même nom que le module et une extension .zip. Il contient le module Windows PowerShell et tout fichier de prise en charge, notamment un fichier manifeste (.psd1), le cas échéant.

Si le module doit contenir un type de connexion Azure Automation, il doit également contenir un fichier portant le nom `<ModuleName>-Automation.json` qui spécifie les propriétés de type de connexion. Il s’agit d’un fichier json, qui est placé dans le dossier de module de votre fichier .zip compressé. Ce fichier contient les champs d’une **connexion** qui est nécessaire pour se connecter au système ou au service que le module représente. Cette configuration finit par créer un type de connexion dans Azure Automation. À l’aide de ce fichier, vous pouvez définir les noms de champ et les types, ainsi que si les champs doivent être chiffrés et/ou facultatifs, pour le type de connexion du module. L’exemple suivant est un modèle au format de fichier json :

```json
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
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
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Si vous avez déjà déployé Service Management Automation et créé des packages de modules d’intégration pour vos runbooks d’automation, ce modèle devrait vous paraître familier.

## <a name="authoring-best-practices"></a>Établissement de meilleures pratiques

Même si les modules d’intégration sont des modules PowerShell, nous vous recommandons de prendre en compte certains éléments au moment de la création d’un module PowerShell pour son utilisation dans Azure Automation. Certaines recommandations sont utiles pour vous garantir un bon fonctionnement de vos modules dans PowerShell Workflow.

1. Incluez un résumé, une description et une URI d’aide pour chaque applet de commande dans le module. Dans PowerShell, vous pouvez définir certaines informations d’aide pour les applets de commande, pour que l’utilisateur puisse obtenir de l’aide quant à leur utilisation, avec l’applet de commande **Get-Help** . Par exemple, voici comment définir un synopsis et l’URI d’aide pour un module PowerShell écrit dans un fichier .psm1. 
   
    ```powershell
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='https://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```

   Fournir ces informations affichent cette aide à l’aide de l’applet de commande **Get-Help** dans la console PowerShell. Ces informations sont également exposées dans Azure Automation.  Par exemple, lors de l’insertion d’activités lors de la création de runbook. En cliquant sur « Voir l’aide détaillée », l’URI d’aide s’ouvre dans un autre onglet du navigateur web que vous utilisez pour accéder à Azure Automation.

   ![Aide du module d’intégration](media/automation-integration-modules/automation-integration-module-activitydesc.png)

2. Si le module s’exécute sur un système distant :

   1. Il doit contenir un fichier de métadonnées du module d’intégration qui définit les informations nécessaires pour se connecter à ce système distant, c’est-à-dire le type de connexion.
   2. Chaque applet de commande du module doit pouvoir accueillir un objet de connexion (une instance de ce type de connexion) en tant que paramètre.  

    Les applets de commande du module sont plus faciles à utiliser dans Azure Automation si vous autorisez le passage d’un objet, avec les champs du type de connexion en tant que paramètre, vers l’applet de commande. Les utilisateurs peuvent ainsi mapper les paramètres de la ressource de connexion aux paramètres correspondants de l’applet de commande chaque fois qu’ils appellent une applet de commande.

    Selon l’exemple de Runbook ci-dessus, il utilise une ressource de connexion Twilio, appelée CorpTwilio, pour accéder à Twilio et renvoyer tous les numéros de téléphone du compte.  Remarquez comment il mappe les champs de la connexion aux paramètres de l’applet de commande.

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Une meilleure approche, plus simple, consisterait à passer l’objet de connexion directement à l’applet de commande :

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```

    Vous pouvez activer un comportement, tel que celui de l’exemple précédent pour vos applets de commande, en les autorisant à accepter un objet de connexion directement en tant que paramètre, au lieu d’accepter uniquement des champs de connexion en tant que paramètres. Généralement, vous voudrez disposer d’un jeu de paramètres pour chaque élément, pour qu’un utilisateur n’utilisant pas Azure Automation puisse appeler vos applets de commande sans créer une table de hachage agissant en tant qu’objet de connexion. Le jeu de paramètres **SpecifyConnectionFields** permet de faire passer, une par une, les propriétés de champ de connexion. **UseConnectionObject** vous permet de transmettre directement la connexion. Comme vous pouvez le voir, l’applet de commande Send-TwilioSMS dans le [module Twilio de PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) autorise le passage dans les deux cas :

    ```powershell
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='https://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```

3. Définissez le type de sortie pour toutes les applets de commande dans le module. La définition d’un type de sortie pour une applet de commande permet à IntelliSense (au moment de la conception) de vous aider à déterminer les propriétés de sortie de l’applet de commande, à utiliser lors de la création. Cela est particulièrement utile lors de la création graphique d’un runbook Azure Automation, où les connaissances au moment de la conception sont essentielles pour une expérience utilisateur conviviale de votre module.

   ![Type de sortie de Runbook graphique](media/automation-integration-modules/runbook-graphical-module-output-type.png)

   Ce comportement ressemble à la fonctionnalité de « frappe au kilomètre » de la sortie d’une applet de commande dans PowerShell ISE, sans avoir à exécuter celle-ci.

   ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)

4. Les applets de commande dans le module ne doivent pas prendre des types d’objets complexes en tant que paramètres. La différence entre PowerShell et le Workflow PowerShell réside dans le fait que ce dernier stocke des types complexes sous forme désérialisée. Les types primitifs restent primitifs, mais les types complexes se convertissent dans leurs versions désérialisées, qui sont avant tout des conteneurs de propriétés. Par exemple, si vous avez utilisé l’applet de commande **Get-Process** dans un Runbook (voire un Workflow PowerShell), elle retourne un objet de type [Deserialized.System.Diagnostic.Process], pas le type [System.Diagnostic.Process] attendu. Ce type a les mêmes propriétés que le type non désérialisé, mais aucune de ses méthodes. Et si vous essayez de passer cette valeur à une applet de commande en tant que paramètre alors que cette dernière attende une valeur [System.Diagnostic.Process] pour ce paramètre, vous obtenez l’erreur suivante : *Impossible de traiter la transformation d’argument sur le paramètre « process ». Error: « Cannot convert the "System.Diagnostics.Process (CcmExec)" value of type "Deserialized.System.Diagnostics.Process" to type "System.Diagnostics.Process « (Erreur : « Impossible de convertir la valeur "System.Diagnostics.Process (CcmExec)" de type "Deserialized.System.Diagnostics.Process" en type "System.Diagnostics.Process" »).*   Cela est dû à l’incompatibilité de type entre le type [System.Diagnostic.Process] attendu et le type [Deserialized.System.Diagnostic.Process] donné. Pour contourner ce problème, assurez-vous que les applets de commande de votre module ne prennent pas de types complexes en tant que paramètres. Voici la mauvaise façon de le faire.

    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ```

    La bonne façon de procéder consiste à prendre un type primitif, qui est utilisé en interne par l’applet de commande, pour récupérer l’objet complexe et l’utiliser. Comme les applets de commande s’exécutent dans le cadre de PowerShell, et non de Workflow PowerShell, $process devient le type [System.Diagnostic.Process] correct au sein de l’applet de commande.  

    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```

   Les ressources de connexion dans les runbooks sont des tables de hachage : un type complexe. Ces tables de hachage semblent cependant pouvoir être transmises sans problème dans les applets de commande pour leur paramètre « Connection », sans aucune exception de transtypage. Techniquement, certains types PowerShell peuvent être désérialisés, et peuvent donc être passés aux applets de commande pour les paramètres acceptant le type non-désérialisé. La table de hachage fait partie de ces types. Il est possible, pour les types définis par l’auteur d’un module, d’être implémentés d’une façon leur permettant de se désérialiser correctement. Certains compromis doivent cependant être pris en compte. Le type doit avoir un constructeur par défaut ainsi qu’un PSTypeConverter et toutes ses propriétés doivent être publiques. Toutefois, il est impossible de « réparer » les types prédéfinis que l’auteur du module ne possède pas, d’où la recommandation d’éviter les types complexes pour l’ensemble des paramètres. Conseil pour la création de runbooks : Si vos applets de commande doivent accepter un paramètre de type complexe, la solution de contournement dans les workflows PowerShell consiste à wrapper dans la même activité InlineScript l’applet de commande qui génère le type complexe avec l’applet de commande qui consomme le type complexe. Étant donné qu’InlineScript exécute son contenu sous forme de PowerShell au lieu de Workflow PowerShell, l’applet de commande qui génère le type complexe produit le type correct, pas le type complexe désérialisé.

5. Rendez toutes les applets de commande dans le module sans état. Le Workflow PowerShell exécute chaque applet de commande appelée dans le flux de travail dans une session distincte. Cela signifie que toute applet de commande, qui dépend de l’état de la session créée / modifiée par d’autres applets de commande dans le même module, ne fonctionnera pas dans les runbooks PowerShell Workflow.  Voici un exemple de procédure à ne pas suivre.
   
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

6. Le module doit être entièrement contenu dans un package pouvant faire l’objet d’une copie Xcopy. Les modules Azure Automation sont distribués dans les bacs à sable Automation lorsque des runbooks doivent s’exécuter. Les modules doivent fonctionner indépendamment de l’hôte sur lequel ils s’exécutent. Vous devez être en mesure de compresser un package de module pour le déplacer et le faire fonctionner normalement lorsqu’il est importé dans l’environnement PowerShell d’un autre hôte. Pour ce faire, le module ne doit dépendre d’aucun fichier situé en dehors du dossier de module. Ce dossier est le dossier qui est compressé pendant l’importation du module dans Azure Automation. Le module ne doit pas dépendre non plus de paramètres de Registre uniques sur un hôte, tels que les paramètres définis à l’installation d’un produit. Si cette bonne pratique n’est pas respectée, le module ne sera pas utilisable dans Azure Automation.  

7. Si vous référencez [les modules Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) dans votre module, vérifiez que vous ne référencez pas également `AzureRM`. Le module `Az` ne peut pas être utilisé conjointement avec les modules `AzureRM`. `Az` est pris en charge dans les runbooks, mais n’est pas importé par défaut. Pour en savoir plus sur les modules `Az` et les points à prendre en considération, consultez [Prise en charge de modules Az dans Azure Automation](az-modules.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md)
* Pour en savoir plus sur la création de modules PowerShell, consultez [Writing a Windows PowerShell Module (Écriture d’un module Windows PowerShell)](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)
