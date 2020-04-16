---
title: Modifier des runbooks textuels dans Azure Automation
description: Cet article fournit différentes procédures pour travailler avec des runbooks PowerShell et de workflow PowerShell dans Azure Automation à l’aide de l’éditeur de texte.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ef7db244057bc8b3b2e4d938b9f3bdd11c7940a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406022"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Modifier des runbooks textuels dans Azure Automation

L’éditeur de texte dans Azure Automation peut être utilisé pour modifier des [Runbooks PowerShell](automation-runbook-types.md#powershell-runbooks) et des [Runbooks de flux de travail PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Cet éditeur offre les caractéristiques standard des autres éditeurs de code, par exemple IntelliSense. Il est également doté d'un code couleur et de fonctionnalités spéciales supplémentaires pour vous aider à accéder aux ressources communes aux runbooks. 

L’éditeur de texte inclut une fonctionnalité permettant d’insérer du code pour les cmdlets, les ressources et les runbooks enfants dans un runbook. Plutôt que de taper le code vous-même, vous pouvez sélectionner le code approprié dans la liste des ressources disponibles, et l’éditeur l’insère dans le runbook.

Dans Azure Automation, chaque runbook existe en deux versions : un brouillon et une version publiée. Vous devez modifier le brouillon du runbook, puis le publier afin qu’il puisse être exécuté. La version publiée ne peut pas être modifiée. Pour plus d'informations, consultez [Publication d'un runbook](manage-runbooks.md#publishing-a-runbook).

Cet article fournit des instructions détaillées pour effectuer différentes fonctions avec cet éditeur. Elles ne s’appliquent pas aux [runbooks graphiques](automation-runbook-types.md#graphical-runbooks). Pour utiliser ces runbooks, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version à l’aide de la rubrique [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Édition d'un Runbook avec le portail Azure

Utilisez la procédure suivante pour ouvrir un runbook afin de le modifier dans l’éditeur de texte.

1. Dans le portail Azure, sélectionnez votre compte Automation.
2. Sous **AUTOMATISATION DE PROCESSUS**, cliquez sur **Runbooks** pour ouvrir la liste des runbooks.
3. Choisissez le runbook à modifier, puis cliquez sur **Modifier**.
4. Modifiez le runbook.
5. Cliquez sur **Enregistrer** lorsque vos modifications sont terminées.
6. Cliquez sur **Publier** si vous souhaitez que le dernier brouillon du runbook soit publié.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Insérer une applet de commande dans un runbook

1. Dans le canevas de l’éditeur de texte, placez le curseur à l’emplacement où vous souhaitez placer l’applet de commande.
2. Développez le nœud **Applets de commande** dans le contrôle Bibliothèque.
3. Développez le module contenant l’applet de commande à utiliser.
4. Cliquez avec le bouton droit sur le nom de la cmdlet à insérer, puis sélectionnez **Ajouter au canevas**. Si plusieurs jeux de paramètres sont définis pour l’applet de commande, l’éditeur ajoute le jeu par défaut. Vous pouvez également développer l’applet de commande pour sélectionner un jeu de paramètres différent.
5. Notez que le code de l’applet de commande est inséré avec sa liste entière de paramètres.
6. Indiquez une valeur appropriée à la place de la valeur entourée par des crochets (<>) pour tous les paramètres requis. Supprimez les paramètres dont vous n’avez pas besoin.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Insérer du code pour un runbook enfant dans un runbook

1. Dans le canevas de l’éditeur de texte, placez le curseur à l’emplacement où vous souhaitez placer le code pour le [runbook enfant](automation-child-runbooks.md).
2. Développez le nœud **Runbooks** dans le contrôle Bibliothèque.
3. Cliquez avec le bouton droit sur le runbook à insérer, puis sélectionnez **Ajouter au canevas**.
4. Le code du runbook enfant est inséré avec des espaces réservés pour les paramètres des runbooks.
5. Remplacez les espaces réservés par des valeurs appropriées pour chaque paramètre.

### <a name="insert-an-asset-into-a-runbook"></a>Insérer une ressource dans un runbook

1. Dans le contrôle Canevas de l’éditeur de texte, positionnez le curseur à l’emplacement où vous souhaitez placer le code pour le runbook enfant.
2. Développez le nœud **Ressources** dans le contrôle Bibliothèque.
3. Développez le nœud du type de ressource souhaité.
4. Cliquez avec le bouton droit sur le nom de la ressource à insérer, puis sélectionnez **Ajouter au canevas**. Pour les [ressources variables](automation-variables.md), sélectionnez **Ajouter « Obtenir la variable » au canevas** ou **Ajouter « Définir la variable » au canevas** selon que vous souhaitez obtenir ou définir la variable.
5. Notez que le code de la ressource est inséré dans le runbook.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Édition d’un runbook Azure Automation à l’aide de Windows PowerShell

Pour modifier un runbook avec Windows PowerShell, utilisez l’éditeur de votre choix et enregistrez le runbook dans un fichier **.ps1**. Vous pouvez utiliser l’applet de commande [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) pour récupérer le contenu du runbook. Vous pouvez utiliser l’applet de commande [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) pour remplacer le brouillon de runbook existant par le runbook modifié.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Récupérer le contenu d’un runbook à l’aide de Windows PowerShell

Les exemples de commandes suivants montrent comment récupérer le script d’un runbook et l’enregistrer dans un fichier de script. Dans cet exemple, le brouillon est récupéré. Il est également possible de récupérer la version publiée du runbook, bien que cette version ne puisse pas être modifiée.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Modifier le contenu d’un runbook à l’aide de Windows PowerShell

Les exemples de commandes suivants montrent comment remplacer le contenu existant d’un runbook par le contenu d’un fichier de script. Il s'agit du même exemple de procédure que celui présenté dans [Pour importer un runbook à partir d'un fichier de script avec Windows PowerShell](manage-runbooks.md#importing-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Articles connexes

* [Gérer les runbooks dans Azure Automation](manage-runbooks.md)
* [Apprentissage du workflow PowerShell](automation-powershell-workflow.md)
* [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md)
* [Certificates](automation-certificates.md)
* [Connexions](automation-connections.md)
* [Informations d'identification](automation-credentials.md)
* [Planifications](automation-schedules.md)
* [Variables](automation-variables.md)
* [Référence sur les applets de commande PowerShell](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
