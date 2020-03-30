---
title: Planifications dans Azure Automation
description: Les planifications Automation permettent de planifier les Runbooks dans Azure pour qu'ils démarrent automatiquement. Décrit comment créer et gérer une planification afin de pouvoir démarrer automatiquement un Runbook à un instant donné ou selon une planification périodique.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79227525"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Planification d'un Runbook dans Azure Automation

Pour planifier le démarrage d'un Runbook dans Azure Automation à une heure spécifiée, liez-le à une ou plusieurs planifications. Une planification peut être configurée pour une exécution des runbooks ponctuelle ou régulière à des heures et jours précis dans le portail Azure. Vous pouvez également planifier une exécution hebdomadaire ou mensuelle, ou des jours spécifiques de la semaine ou du mois, ou encore un jour particulier du mois. Un Runbook peut être lié à plusieurs planifications et une planification peut avoir plusieurs Runbooks qui lui sont liés.

> [!NOTE]
> Les planifications ne prennent pas en charge les configurations Azure Automation DSC pour le moment.

## <a name="powershell-cmdlets"></a>Applets de commande Powershell

Les applets de commande du tableau suivant permettent de créer et de gérer les planifications avec PowerShell dans Azure Automation. Elles sont fournies dans le cadre du [module Azure PowerShell](/powershell/azure/overview).

| Applets de commande | Description |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Récupère une planification. |
| [Nouvelle AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Crée une planification. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Supprime une planification. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Définit les propriétés d'une planification existante. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Récupère les Runbooks planifiés. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associe un Runbook à une planification. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissocie un Runbook d'une planification. |

## <a name="creating-a-schedule"></a>Création d'une planification

Vous pouvez utiliser le portail Azure ou PowerShell pour créer une planification de runbooks.

> [!NOTE]
> Azure Automation utilise les modules les plus récents de votre compte Automation durant l’exécution d’un nouveau travail planifié.  Pour éviter d’affecter vos runbooks et les processus qu’ils automatisent, vous devez tout d’abord tester les runbooks ayant des planifications associées avec un compte Automation dédié au test.  Cela permet de vérifier que vos runbooks planifiés continuent de fonctionner correctement et, dans le cas contraire, vous pouvez résoudre les problèmes et appliquer les modifications nécessaires avant de migrer la version mise à jour du runbook en production.
> Votre compte Automation n’obtient pas automatiquement les nouvelles versions des modules, sauf si vous les avez mis à jour manuellement en sélectionnant l’option [Mettre à jour les modules Azure](../automation-update-azure-modules.md) du panneau **Modules**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Pour créer une planification à l’aide du portail Azure

1. Dans le portail Azure, accédez à votre compte Automation et sélectionnez **Planifications** dans la section **Ressources partagées** à gauche.
2. Cliquez sur **Ajouter une planification** en haut de la page.
3. Dans le volet **Nouvelle planification**, tapez un **Nom** et éventuellement une **Description** pour la nouvelle planification.
4. Indiquez si la planification doit s’exécuter une seule fois ou selon un calendrier récurrent en sélectionnant **Une fois** ou **Récurrent**. Si vous sélectionnez **Une fois**, indiquez une **Heure de début**, puis cliquez sur **Créer**. Si vous sélectionnez **Récurrent**, spécifiez une **Heure de début** et pour **Tous les**, sélectionnez la fréquence à laquelle vous voulez répéter le runbook : **heure**, **jour**, **semaine** ou **mois**.
    1. Si vous sélectionnez **semaine**, une liste des jours de la semaine s’affiche. Sélectionnez autant de jours que vous le voulez. La première exécution de votre planification a lieu le premier jour sélectionné, après l’heure de début. Par exemple, si vous planifiez des runbooks pour le week-end, choisissez **samedi** et **dimanche**.

       ![Configuration d’une planification récurrente pour le week-end](../media/schedules/week-end-weekly-recurrence.png)

    2. Si vous sélectionnez **mois**, vous disposez de différentes options. Pour l’option **Occurrences mensuelles**, sélectionnez **Jours du mois** ou **Jours de la semaine**. Si vous choisissez **Jours du mois**, un calendrier s’affiche vous permettant de choisir autant de jours que vous le voulez. Si par exemple vous choisissez comme date le 31, et que le mois en cours ne dispose pas de 31e jour, la planification ne sera pas exécutée. Si vous souhaitez que la planification s’exécute le dernier jour, choisissez **Oui** sous **Run on last day of month** (Exécution le dernier jour du mois). Si vous choisissez **Jours de la semaine**, l’option **Tous les** est proposée. Choisissez **Premier**, **Deuxième**, **Troisième**, **Quatrième** ou **Dernier**. Enfin, choisissez un jour pour la répétition.

       ![Planification mensuelle le premier, le quinzième et le dernier jours du mois](../media/schedules/monthly-first-fifteenth-last.png)

5. Une fois que vous avez terminé, cliquez sur **Créer**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Pour créer une planification avec PowerShell

Vous utilisez l’applet de commande [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) pour créer des planifications. Spécifiez l’heure de début de la planification et indiquez sa fréquence d’exécution. Les exemples suivants montrent comment créer de nombreux scénarios de planification différents.

#### <a name="create-a-one-time-schedule"></a>Créer une planification unique

Les exemples de commandes suivants permettent de créer une planification unique.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Créer une planification périodique

Les exemples de commandes suivants montrent comment créer une planification périodique qui s’exécute tous les jours à 13 h 00 pendant un an.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Créer une planification périodique hebdomadaire

Les exemples de commandes suivants montrent comment créer une planification hebdomadaire qui est exécutée uniquement en semaine.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Créer une planification périodique hebdomadaire pour le week-end

Les exemples de commandes suivants montrent comment créer une planification hebdomadaire qui est exécutée uniquement durant le week-end.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Créer une planification périodique pour le premier, le quinzième et le dernier jours du mois

Les exemples de commandes suivants montrent comment créer une planification périodique qui s’exécute le premier, le quinzième et le dernier jours du mois.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Liaison d'une planification à un Runbook

Un Runbook peut être lié à plusieurs planifications et une planification peut avoir plusieurs Runbooks qui lui sont liés. Si un Runbook possède des paramètres, vous pouvez leur fournir des valeurs. Vous devez fournir des valeurs pour tous les paramètres obligatoires et vous pouvez fournir des valeurs pour tous les paramètres facultatifs. Ces valeurs sont utilisées à chaque démarrage du Runbook par cette planification. Vous pouvez attacher le même Runbook à une autre planification et spécifier différentes valeurs de paramètre.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Pour lier une planification à un Runbook avec le portail Azure

1. Dans le portail Azure, accédez à votre compte Automation et sélectionnez **Runbooks** dans la section **Automatisation de processus** à gauche.
2. Cliquez sur le nom du Runbook à planifier.
3. Si le runbook n’est pas lié à une planification, vous avez la possibilité de créer une planification ou de le lier à une planification existante.
4. Si le runbook comprend des paramètres, vous pouvez sélectionner l’option **Modifier les paramètres d’exécution (par défaut : Azure)** pour accéder au volet **Paramètres**, dans lequel vous pouvez saisir les informations correspondantes.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Pour lier une planification à un runbook avec PowerShell

Vous pouvez utiliser l’applet de commande [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) pour lier une planification. Vous pouvez spécifier les valeurs des paramètres du Runbook avec le paramètre Parameters. Pour plus d'informations sur la spécification des valeurs des paramètres, consultez [Démarrage d'un Runbook dans Azure Automation](../automation-starting-a-runbook.md) .
Les exemples de commandes suivants montrent comment lier une planification à un Runbook à l’aide d’une applet de commande Azure Resource Manager avec des paramètres.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Planification plus fréquente des runbooks

L’intervalle le plus fréquent pour lequel une planification dans Azure Automation peut être configurée est d’une heure. Si vous avez besoin pour exécuter les planifications plus fréquemment, il existe deux options :

* Créer un [webhook](../automation-webhooks.md) de runbook et utiliser [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) pour appeler le webhook. Azure Logic Apps permet une plus grande précision lors de la définition d’une planification.

* Créer quatre planifications éloignées de 15 minutes et exécutées une fois par heure. Ce scénario permet au runbook de s’exécuter toutes les 15 minutes avec différentes planifications.

## <a name="disabling-a-schedule"></a>Désactivation d'une planification

Lorsque vous désactivez une planification, les Runbooks qui y sont liés ne s'exécutent plus sur cette planification. Vous pouvez désactiver manuellement une planification ou affecter un délai d’expiration aux planifications selon une certaine fréquence lors de leur création. Lorsque le délai d’expiration est atteint, la planification est désactivée.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Pour désactiver une planification à partir du portail Azure

1. Dans le portail Azure, accédez à votre compte Automation et sélectionnez **Planifications** dans la section **Ressources partagées** à gauche.
2. Cliquez sur le nom d’une planification pour ouvrir le volet Détails.
3. Remplacez **Activé** par **Non**.

> [!NOTE]
> Si vous souhaitez désactiver une planification dont l’heure de début est située dans le passé, vous devez remplacer sa date de début par une date ultérieure avant de l’enregistrer.

### <a name="to-disable-a-schedule-with-powershell"></a>Pour désactiver une planification avec PowerShell

Vous pouvez utiliser l’applet de commande [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) pour modifier les propriétés d’une planification existante. Pour désactiver la planification, spécifiez la valeur **false** pour le paramètre **IsEnabled**.

Les exemples de commandes suivants montrent comment désactiver une planification pour un Runbook à l’aide d’une applet de commande Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Étapes suivantes

* Pour vous familiariser avec les Runbooks dans Azure Automation, consultez [Démarrage d’un Runbook dans Azure Automation](../automation-starting-a-runbook.md)

