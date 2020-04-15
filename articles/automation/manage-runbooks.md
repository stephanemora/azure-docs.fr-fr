---
title: Gérer les runbooks dans Azure Automation
description: Cet article décrit comment gérer des runbooks dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 29ac9239b8dc87b1ed12fc8333bf5201fe8fa204
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617128"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gérer les runbooks dans Azure Automation

Vous pouvez ajouter un runbook à Azure Automation [en en créant un](#creating-a-runbook) ou [en en important un existant](#importing-a-runbook) à partir d’un fichier ou de la [galerie de runbooks](automation-runbook-gallery.md). Cet article fournit des informations sur la création et l’importation des runbooks à partir d’un fichier. Vous pouvez obtenir toutes les informations sur l’accès aux runbooks et modules communautaires dans [Galeries de runbooks et de modules pour Azure Automation](automation-runbook-gallery.md).

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="creating-a-runbook"></a>Créer un runbook

Vous pouvez créer un runbook dans Azure Automation à l’aide de l’un des portails Azure ou de Windows PowerShell. Une fois que le Runbook a été créé, vous pouvez le modifier à l’aide des informations disponibles dans [Apprentissage du workflow Windows PowerShell](automation-powershell-workflow.md) et [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Créer un runbook dans le portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
2. À partir du hub, sélectionnez **Runbooks** sous **Automatisation de processus** pour ouvrir la liste des runbooks.
3. Cliquez sur **Créer un runbook**.
4. Entrez un nom pour le runbook et sélectionnez son [type](automation-runbook-types.md). Le nom du runbook doit commencer par une lettre et peut contenir des lettres, des chiffres, des traits de soulignement et des tirets.
5. Cliquez sur **Créer** pour créer le runbook et ouvrez l’éditeur.

### <a name="create-a-runbook-with-powershell"></a>Créer un Runbook avec PowerShell

Vous pouvez utiliser l’applet de commande [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) pour créer un [runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) vide. Utilisez le paramètre `Type` pour spécifier l’un des types de runbook définis pour `New-AzAutomationRunbook`.

L’exemple suivant montre comment créer un runbook vide.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook"></a>Importer un runbook

Vous pouvez créer un runbook dans Azure Automation en important un script PowerShell ou PowerShell Workflow ( **.ps1**), un runbook graphique exporté ( **.graphrunbook**) ou un script Python2 ( **.py**).  Vous devez spécifier le [type de runbook](automation-runbook-types.md) qui sera créé à lors de l’importation et ce, en tenant compte des considérations suivantes.

* Un fichier **.ps1** qui ne contient pas de workflow peut être importé dans un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) ou un [runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Si vous l’importez dans un runbook PowerShell Workflow, il est converti en workflow. Dans ce cas, les commentaires sont inclus dans le runbook pour décrire les modifications apportées.

* Un fichier **.ps1** contenant un workflow PowerShell peut uniquement être importé dans un runbook [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Si le fichier contient plusieurs workflows PowerShell, l’importation échoue. Vous devez enregistrer chaque workflow dans son propre fichier, puis les importer séparément.

* Un fichier **.ps1** contenant un workflow PowerShell ne doit pas être importé dans un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks), car le moteur de script PowerShell ne peut pas le reconnaître.

* Un fichier **.graphrunbook** peut uniquement être importé dans un nouveau [runbook graphique](automation-runbook-types.md#graphical-runbooks). Notez que vous pouvez uniquement créer un runbook graphique à partir d’un fichier **.graphrunbook**.

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Importer un runbook à partir d’un fichier avec le portail Azure

Vous pouvez utiliser la procédure suivante pour importer un fichier de script dans Azure Automation.

> [!NOTE]
> Vous pouvez uniquement importer un fichier **.ps1** dans un runbook PowerShell Workflow à l’aide du portail.

1. Dans le portail Azure, ouvrez votre compte Automation.
2. À partir du hub, sélectionnez **Runbooks** sous **Automatisation de processus** pour ouvrir la liste des runbooks.
3. Cliquez sur **Importer un runbook**.
4. Cliquez sur **Fichier runbook** et sélectionnez le fichier à importer.
5. Si le champ **Nom** est activé, vous avez la possibilité de modifier le nom du runbook. Celui-ci doit commencer par une lettre et peut contenir des lettres, des chiffres, des traits de soulignement et des tirets.
6. Le [type de runbook](automation-runbook-types.md) est sélectionné automatiquement, mais vous pouvez le modifier après avoir pris en compte les restrictions applicables.
7. Cliquez sur **Créer**. Le nouveau runbook apparaît dans la liste des runbooks pour le compte Automation.
8. Vous devez [Publier le runbook](#publishing-a-runbook) avant de pouvoir l’exécuter.

> [!NOTE]
> Après avoir importé un runbook graphique ou un runbook PowerShell Workflow graphique, vous pouvez en convertir le type. Toutefois, vous ne pouvez pas convertir l’un de ces runbooks graphiques en runbook textuel.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importer un runbook à partir d’un fichier de script avec Windows PowerShell

Utilisez l’applet de commande [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) pour importer un fichier de script en tant que brouillon de runbook PowerShell Workflow. Si le runbook existe déjà, l’importation échoue, sauf si vous utilisez le paramètre `Force` avec l’applet de commande.

L’exemple suivant montre comment importer un fichier de script dans un runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="testing-a-runbook"></a>Tester un runbook

Lorsque vous testez un runbook, la [version Brouillon](#publishing-a-runbook) est exécutée et toutes les actions qu’il effectue sont finalisées. Aucun historique des tâches n’est créé, mais les flux [Résultat](automation-runbook-output-and-messages.md#output-stream) et [Avertissement et Erreur](automation-runbook-output-and-messages.md#message-streams) s’affichent dans le volet de sortie du test. Les messages destinés au [flux de commentaires](automation-runbook-output-and-messages.md#message-streams) ne sont affichés dans le volet des résultats que si la variable [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) est définie sur `Continue`.

Bien que la version brouillon soit exécutée, le runbook s'exécute normalement et effectue des actions sur des ressources dans l’environnement. Pour cette raison, vous devez tester les runbooks uniquement sur des ressources hors production.

La procédure de test de chaque [type de runbook](automation-runbook-types.md) est identique. Il n’y a aucune différence de test entre l’éditeur de texte et l’éditeur graphique du portail Azure.

1. Ouvrez la version Brouillon du runbook dans l’[éditeur de texte](automation-edit-textual-runbook.md) ou l’[éditeur graphique](automation-graphical-authoring-intro.md).
1. Cliquez sur le bouton **Tester** pour ouvrir la page de test.
1. Si le runbook a des paramètres, ils figurent dans le volet gauche dans lequel vous pouvez fournir des valeurs à utiliser pour le test.
1. Si vous souhaitez exécuter le test sur un [runbook Worker hybride](automation-hybrid-runbook-worker.md), définissez les **Paramètres d’exécution** sur **Worker hybride** et sélectionnez le nom du groupe cible.  Dans le cas contraire, conservez la valeur par défaut **Azure** pour exécuter le test dans le cloud.
1. Cliquez sur le bouton **Démarrer** pour démarrer le test.
1. Vous pouvez utiliser les boutons situés sous le volet de sortie pour arrêter ou suspendre un runbook [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) ou [graphique](automation-runbook-types.md#graphical-runbooks) pendant son test. Lorsque vous interrompez le runbook, il termine l’activité en cours avant de s’interrompre. Lorsque le runbook est suspendu, vous pouvez l’arrêter ou le redémarrer.
1. Inspectez la sortie du runbook dans le volet de sortie.

## <a name="publishing-a-runbook"></a>Publication d’un runbook

Lorsque vous créez ou importez un runbook, vous devez le publier avant de pouvoir l’exécuter. Dans Azure Automation, chaque runbook a une version brouillon et une version publiée. Seule la version publiée est disponible à l'exécution, et seul le brouillon peut être modifié. La version publiée n'est pas affectée par les modifications apportées à la version Bouillon. Quand la version brouillon doit être rendue disponible, vous la publiez, ce qui remplace la version publiée actuelle par la version brouillon.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publier un runbook dans le portail Azure

1. Ouvrez le runbook dans le portail Azure.
2. Cliquez sur **Modifier**.
3. Cliquez sur **Publier**, puis sur **Oui** en réponse au message de vérification.

### <a name="publish-a-runbook-using-powershell"></a>Publier un runbook à l’aide de PowerShell

Utilisez l’applet de commande [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) pour publier un runbook avec Windows PowerShell. L’exemple suivant montre comment publier un runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="scheduling-a-runbook-in-the-azure-portal"></a>Planifier un runbook sur le portail Azure

Une fois votre runbook publié, vous pouvez le planifier en vue de l'utiliser.

1. Ouvrez le runbook dans le portail Azure.
2. Sélectionnez **Planifications** sous **Ressources**.
3. Sélectionnez **Ajouter une planification**.
4. Dans le volet Planifier le runbook, sélectionnez **Associer une planification à votre runbook**.
5. Choisissez **Créer une planification** dans le volet Planification.
6. Entrez un nom, une description et d'autres paramètres dans le volet Nouvelle planification. 
7. Une fois la planification créée, mettez-la en surbrillance et cliquez sur **OK**. Elle doit maintenant être associée à votre runbook.
8. Accédez à votre boîte aux lettres et recherchez-y un e-mail qui vous informe de l'état du runbook.

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir comment vous pouvez tirer parti de la galerie de runbooks et de modules PowerShell, consultez [Galeries de runbooks et de modules pour Azure Automation](automation-runbook-gallery.md).
* Pour en savoir plus sur la modification des runbooks PowerShell et PowerShell Workflow avec un éditeur de texte, consultez [Modifier des runbooks textuels dans Azure Automation](automation-edit-textual-runbook.md).
* Pour en savoir plus sur la création de runbooks graphiques, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).
