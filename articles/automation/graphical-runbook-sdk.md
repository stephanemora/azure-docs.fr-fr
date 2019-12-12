---
title: Vue d’ensemble du kit de développement logiciel (SDK) de runbook graphique Azure Automation
description: Cet article explique comment utiliser le kit de développement logiciel (SDK) de runbook graphique Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a06c190931fdd0f49132f815b153c08ece68c9f3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849545"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Utiliser le kit de développement logiciel (SDK) de runbook graphique Azure Automation

Les [runbooks graphiques](automation-graphical-authoring-intro.md) sont des runbooks qui aident à gérer la complexité du code Windows PowerShell ou PowerShell Workflow sous-jacent. Le kit de développement logiciel (SDK) de création graphique Microsoft Azure Automation permet aux développeurs de créer et de modifier des runbooks graphiques en vue d’une utilisation avec le service Azure Automation. Les extraits de code suivants montrent le flux de base pour la création d’un runbook graphique à partir de votre code.

## <a name="pre-requisites"></a>Conditions préalables

Pour commencer, importez le package `Microsoft.Azure.Automation.GraphicalRunbook.Model` dans votre projet.

## <a name="create-a-runbook-object-instance"></a>Créer une instance d’objet de runbook

Référencez l’assembly `Orchestrator.GraphRunbook.Model` et créez une instance de la classe `Orchestrator.GraphRunbook.Model.GraphRunbook` :

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Ajouter des paramètres de runbook

Instanciez les objets `Orchestrator.GraphRunbook.Model.Parameter` et ajoutez-les au runbook :

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Ajouter des activités et des liens

Instanciez des activités de types appropriés et ajoutez-les au runbook :

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Les activités sont implémentées par les classes suivantes dans l’espace de noms `Orchestrator.GraphRunbook.Model` :

|Classe  |Activité  |
|---------|---------|
|CommandActivity     | Appelle une commande PowerShell (cmdlet, fonction, etc.).        |
|InvokeRunbookActivity     | Appelle un autre runbook en ligne.        |
|JunctionActivity     | Attend que toutes les branches entrantes aient terminé.        |
|WorkflowScriptActivity     | Exécute un bloc de code PowerShell ou PowerShell Workflow (selon le type de runbook) dans le contexte du runbook. Il s’agit d’un outil puissant, mais n’en abusez pas : l’interface utilisateur affiche ce bloc de script en tant que texte ; le moteur d’exécution traite le bloc fourni comme une boîte noire et n’essaie pas d’analyser son contenu, à l’exception d’une vérification basique de la syntaxe. Si vous devez seulement appeler une commande PowerShell, préférez CommandActivity.        |

> [!NOTE]
> Ne dérivez pas vos propres activités des classes fournies : Azure Automation ne peut pas utiliser des runbooks avec des types d’activités personnalisées.

Les paramètres CommandActivity et InvokeRunbookActivity doivent être fournis en tant que descripteurs de valeur, et pas directement comme valeurs. Les descripteurs de valeur spécifient la façon dont les valeurs de paramètre réelles doivent être produites. Les descripteurs de valeur suivants sont actuellement disponibles :


|Descripteur  |Définition  |
|---------|---------|
|ConstantValueDescriptor     | Fait référence à une valeur constante codées en dur.        |
|RunbookParameterValueDescriptor     | Fait référence à un paramètre de runbook par son nom.        |
|ActivityOutputValueDescriptor     | Fait référence à une sortie d’activité en amont, ce qui permet à une activité de « s’abonner » aux données produites par une autre activité.        |
|AutomationVariableValueDescriptor     | Fait référence à une ressource de variable Automation par son nom.         |
|AutomationCredentialValueDescriptor     | Fait référence à une ressource de certificat Automation par son nom.        |
|AutomationConnectionValueDescriptor     | Fait référence à une ressource de connexion Automation par son nom.        |
|PowerShellExpressionValueDescriptor     | Spécifie une expression PowerShell de forme libre qui sera évaluée juste avant l’appel de l’activité.  <br/>Il s’agit d’un outil puissant, mais n’en abusez pas : l’interface utilisateur affiche cette expression en tant que texte ; le moteur d’exécution traite le bloc fourni comme une boîte noire et n’essaie pas d’analyser son contenu, à l’exception d’une vérification basique de la syntaxe. Dans la mesure du possible, préférez les descripteurs de valeur plus spécifiques.      |

> [!NOTE]
> Ne dérivez pas vos propres descripteurs de valeur des classes fournies : Azure Automation ne peut pas utiliser des runbooks avec des types de descripteurs de valeur personnalisés.

Instanciez les liens connectant les activités et ajoutez-les au runbook :

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Enregistrer le runbook dans un fichier

Utilisez `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` pour sérialiser un runbook vers une chaîne :

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Cette chaîne peut être enregistrée dans un fichier avec l’extension **.graphrunbook**, et ce fichier peuvent être importé dans Azure Automation.
Le format sérialisé peut changer dans les prochaines versions de `Orchestrator.GraphRunbook.Model.dll`. Nous vous garantissons une compatibilité descendante : n’importe quel runbook sérialisé avec une version antérieure de `Orchestrator.GraphRunbook.Model.dll` peut être désérialisé par une version plus récente. La compatibilité ascendante n’est pas garantie : un runbook sérialisé avec une version plus récente n’est peut-être pas désérialisable par les versions antérieures.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les runbooks graphiques dans Azure Automation, consultez l’[introduction à la création de graphiques](automation-graphical-authoring-intro.md).

