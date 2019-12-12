---
title: Gérer les runbooks dans Azure Automation
description: Cet article décrit comment gérer des runbooks dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 42b973ce8a25abff60211afb34ef719d42366175
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850497"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gérer les runbooks dans Azure Automation

Vous pouvez ajouter un runbook à Azure Automation [en créant un](#create-a-runbook) ou en important un Runbook à partir d’un fichier ou de la [galerie de runbooks](automation-runbook-gallery.md). Cet article fournit des informations sur la création et l’importation des runbooks à partir d’un fichier.  Vous pouvez obtenir toutes les informations sur l’accès aux runbooks et modules communautaires dans [Galeries de runbooks et de modules pour Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Créer un runbook

Vous pouvez créer un runbook dans Azure Automation à l’aide de l’un des portails Azure ou de Windows PowerShell. Une fois que le Runbook a été créé, vous pouvez le modifier à l’aide des informations disponibles dans [Apprentissage du workflow Windows PowerShell](automation-powershell-workflow.md) et [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Créer un runbook dans le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
2. À partir du Hub, sélectionnez **Runbooks** pour ouvrir la liste des runbooks.
3. Cliquez sur le bouton **Ajouter un Runbook**, puis sur **Créer un Runbook**.
4. Saisissez un **Nom** pour le runbook et sélectionnez son [Type](automation-runbook-types.md). Le nom du runbook doit commencer par une lettre et peut contenir des lettres, des chiffres, des traits de soulignement et des tirets.
5. Cliquez sur **Créer** pour créer le runbook et ouvrez l’éditeur.

### <a name="create-a-runbook-with-powershell"></a>Créer un Runbook avec PowerShell

Vous pouvez utiliser l’applet de commande [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) pour créer un [Runbook de flux de travail PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) vide. Utilisez le paramètre **Type** spécifier l’un des quatre types de runbook.

Les exemples de commandes suivants montrent comment créer un runbook vide.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importer un runbook

Vous pouvez créer un runbook dans Azure Automation en important un script PowerShell, un workflow PowerShell (extension .ps1), un runbook graphique exporté (.graphrunbook) ou un script Python 2 (extension .py).  Vous devez spécifier le [type de runbook](automation-runbook-types.md) qui sera créé à lors de l’importation et ce, en tenant compte des considérations suivantes.

* Un fichier `.graphrunbook` peut uniquement être importé dans un nouveau [runbook graphique](automation-runbook-types.md#graphical-runbooks), et les runbooks graphiques ne peuvent être créés qu’à partir d’un fichier `.graphrunbook`.
* Un fichier `.ps1` contenant un workflow PowerShell peut uniquement être importé dans un [runbook de workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Si le fichier contient plusieurs workflows PowerShell, l’importation échoue. Vous devez enregistrer chaque workflow dans son propre fichier, puis les importer séparément.
* Un fichier `.ps1` contenant un workflow PowerShell ne doit pas être importé dans un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks), car il ne peut pas être reconnu par le moteur de script PowerShell.
* Un fichier `.ps1` qui ne contient pas de flux de travail peut être importé dans un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) ou un [runbook de flux de travail PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  S’il est importé dans un runbook de workflow PowerShell, il est ensuite converti en workflow, et les commentaires sont inclus dans le runbook spécifiant les modifications qui ont été apportées.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Pour importer un runbook à partir d’un fichier avec le portail Azure

Vous pouvez utiliser la procédure suivante pour importer un fichier de script dans Azure Automation.

> [!NOTE]
> Notez que vous pouvez uniquement importer un fichier .ps1 dans un runbook de workflow PowerShell à l’aide de ce portail.

1. Dans le portail Azure, ouvrez votre compte Automation.
2. À partir du Hub, sélectionnez **Runbooks** pour ouvrir la liste des runbooks.
3. Cliquez sur le bouton **Ajouter un Runbook**, puis sur **Importer**.
4. Cliquez sur **Fichier runbook** pour sélectionner le fichier à importer.
5. Si le champ **Nom** est activé, vous avez la possibilité de modifier le nom.  Le nom du runbook doit commencer par une lettre et peut contenir des lettres, des chiffres, des traits de soulignement et des tirets.
6. Le [type de runbook](automation-runbook-types.md) est sélectionné automatiquement, mais vous pouvez le modifier après avoir pris en compte les restrictions applicables.
7. Le nouveau runbook apparaît dans la liste des runbooks du compte Automation.
8. Vous devez [Publier le runbook](#publish-a-runbook) avant de pouvoir l’exécuter.

> [!NOTE]
> Après avoir importé un runbook graphique ou un runbook de workflow PowerShell graphique, vous pouvez effectuer une conversion dans l’autre type le cas échéant. Vous ne pouvez pas convertir un runbook au format textuel.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Pour importer un runbook à partir d’un fichier de script avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) pour importer un fichier de script en tant que brouillon de runbook de workflow PowerShell. Si le runbook existe déjà, l’importation échoue, sauf si vous utilisez le paramètre *-Force*.

Les exemples de commandes suivants montrent comment importer un fichier de script dans un runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Tester un runbook

Lorsque vous testez un runbook, la [version Brouillon](#publish-a-runbook) est exécutée et toutes les actions qu’il effectue sont finalisées. Aucun historique des tâches n’est créé, mais les flux [Résultat](automation-runbook-output-and-messages.md#output-stream) et [Avertissement et Erreur](automation-runbook-output-and-messages.md#message-streams) s’affichent dans le panneau de sortie du test. Les messages dirigés vers le [flux de messages](automation-runbook-output-and-messages.md#message-streams) s’affiche dans le panneau de résultat uniquement si la variable [$VerbosePreference variable](automation-runbook-output-and-messages.md#preference-variables) a pour valeur Continue.

Bien que la version brouillon soit exécutée, le runbook s'exécute normalement et effectue des actions sur des ressources dans l’environnement. Pour cette raison, vous devez tester les runbooks uniquement sur des ressources hors production.

La procédure de chaque [type de runbook](automation-runbook-types.md) est identique et il n’y a aucune différence de test entre l’éditeur de texte et l’éditeur graphique dans le portail Azure.

1. Ouvrez la version Brouillon du runbook dans l’[éditeur de texte](automation-edit-textual-runbook.md) ou l’[éditeur graphique](automation-graphical-authoring-intro.md).
1. Cliquez sur le bouton **Tester** pour ouvrir la page de test.
1. Si le runbook possède des paramètres, ils figureront dans le volet gauche dans lequel vous pourrez fournir des valeurs à utiliser pour le test.
1. Si vous souhaitez exécuter le test sur [Runbook Worker hybride](automation-hybrid-runbook-worker.md), définissez les **Paramètres d’exécution** sur **Worker hybride** et sélectionnez le nom du groupe cible.  Dans le cas contraire, conservez la valeur par défaut **Azure** pour exécuter le test dans le cloud.
1. Cliquez sur le bouton **Démarrer** pour démarrer le test.
1. Si le runbook correspond à [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) ou [Graphique](automation-runbook-types.md#graphical-runbooks), vous pouvez l’arrêter ou le suspendre lorsqu'il est en cours de test en vous aidant des boutons situés sous le panneau de résultat. Lorsque vous interrompez le runbook, il termine l’activité en cours avant de s’interrompre. Lorsque le runbook est suspendu, vous pouvez l’arrêter ou le redémarrer.
1. Inspectez la sortie du runbook dans le panneau de résultat.

## <a name="publish-a-runbook"></a>Publier un runbook

Lorsque vous créez ou importez un runbook, vous devez le publier avant de pouvoir l’exécuter.  Dans Automation, chaque Runbook a un brouillon et une version publiée. Seule la version publiée est disponible à l'exécution, et seul le brouillon peut être modifié. La version publiée n'est pas affectée par les modifications apportées à la version Bouillon. Quand le brouillon doit être rendu disponible, vous le publiez, ce qui remplace la version publiée par le brouillon.

### <a name="azure-portal"></a>Portail Azure

1. Ouvrez le runbook dans le portail Azure.
2. Cliquez sur le bouton **Edit** .
3. Cliquez sur le bouton **Publier**, puis sur **Oui** pour le message de vérification.

### <a name="powershell"></a>PowerShell

Vous pouvez utiliser l’applet de commande [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) pour publier un runbook avec Windows PowerShell. Les exemples de commandes suivants montrent comment publier un exemple de runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir comment vous pouvez tirer parti de la galerie de Runbooks et de modules PowerShell, consultez [Galeries de runbooks et de modules pour Azure Automation](automation-runbook-gallery.md)
* Pour en savoir plus sur la modification des runbooks PowerShell et de workflow PowerShell avec un éditeur de texte, consultez [Modifier des runbooks textuels dans Azure Automation](automation-edit-textual-runbook.md)
* Pour en savoir plus sur la création de Runbooks graphiques, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md)
