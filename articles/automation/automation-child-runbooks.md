---
title: Runbooks enfants dans Azure Automation
description: Décrit les différentes méthodes permettant le démarrage d’un Runbook à partir d’un autre Runbook dans Azure Automation et le partage d’informations entre eux.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e7341a8c270d16497430a70c2a1b21354a775787
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850446"
---
# <a name="child-runbooks-in-azure-automation"></a>Runbooks enfants dans Azure Automation

Dans Azure Automation, il est recommandé d’écrire des runbooks réutilisables et modulaires avec une fonction discrète qui est utilisée par d’autres runbooks. Un runbook parent appelle souvent un ou plusieurs runbooks enfants pour exécuter la fonctionnalité requise. Il existe deux méthodes pour appeler un runbook enfant. Vous devez comprendre leurs spécificités afin de déterminer celle répondant le mieux aux exigences de chacun de vos scénarios.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Appeler un Runbook enfant à l’aide de l’exécution en ligne

Pour appeler un Runbook en ligne à partir d’un autre Runbook, vous utilisez le nom du Runbook et définissez des valeurs pour ses paramètres de la même façon qu’avec une activité ou une applet de commande.  Tous les Runbooks d’un même compte Automation peuvent être utilisés ainsi par les autres Runbooks. Le Runbook parent attend la fin de l’exécution du Runbook enfant avant de passer à la ligne suivante, et toute sortie est retournée directement au parent.

Lorsque vous appelez un Runbook en ligne, il est exécuté dans la même tâche que le Runbook parent. L’historique des travaux du runbook enfant n’indique pas qu’il a été exécuté. Toutes les exceptions et sorties de flux issues du runbook enfant sont associées au parent. Ce comportement réduit le nombre de travaux et simplifie leur suivi ainsi que la résolution des problèmes. En effet, toutes les exceptions lancées par le runbook enfant et toutes les sorties de flux correspondantes sont associées au travail du parent.

Lorsqu’un Runbook est publié, les Runbooks enfants qu’il appelle doivent déjà être publiés. En effet, Azure Automation crée une association avec tous les Runbooks enfants lorsqu’un Runbook est compilé. Si ce n’est pas le cas, la publication du runbook parent semble correcte, mais le runbook génère une exception au démarrage. Dans ce cas, vous pouvez republier le runbook parent pour référencer correctement les runbooks enfants. Il est inutile de republier le runbook parent après la modification des runbooks enfants, car l’association a déjà été créée.

Les paramètres d’un runbook enfant appelé en ligne peuvent être n’importe quel type de données, y compris des objets complexes. Il n’y a pas de [sérialisation JSON](start-runbooks.md#runbook-parameters) comme c’est le cas quand vous démarrez le runbook à partir du portail Azure ou à l’aide de l’applet de commande Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Types de runbook

Types pouvant s’appeler mutuellement :

* Un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) et des [runbooks graphiques](automation-runbook-types.md#graphical-runbooks) peuvent s’appeler mutuellement en ligne (les deux sont basés sur PowerShell).
* Un [runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) et des runbooks PowerShell Workflow graphiques peuvent s’appeler mutuellement en ligne (les deux sont basé sur PowerShell)
* Les types PowerShell et PowerShell Workflow ne peuvent pas s’appeler mutuellement en ligne doivent utiliser Start-AzureRmAutomationRunbook.

Pertinence de l’ordre de publication :

* L’ordre de publication de runbooks n’est important que pour les runbooks PowerShell Workflow et les runbooks graphiques PowerShell.

Quand vous appelez un runbook graphique ou PowerShell Workflow enfant à l’aide d’une exécution incluse, vous utilisez le nom du runbook.  Quand vous appelez un runbook enfant PowerShell, vous devez commencer son nom par *.\\* pour spécifier que le script se trouve dans le répertoire local.

### <a name="example"></a>Exemples

L’exemple suivant démarre un runbook enfant de test qui accepte trois paramètres : un objet complexe, un entier et une valeur booléenne. La sortie du Runbook enfant est affectée à une variable.  Dans ce cas, le runbook enfant est un runbook de workflow PowerShell.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Voici le même exemple utilisant un runbook PowerShell en tant qu’enfant.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Démarrage d’un Runbook enfant à l’aide d’une applet de commande

> [!IMPORTANT]
> Si vous appelez un runbook enfant à l’aide de l’applet de commande `Start-AzureRmAutomationRunbook` incluant le commutateur `-Wait`, et que le résultat du runbook enfant est un objet, des erreurs peuvent se produire. Pour résoudre le problème, consultez [Runbooks enfants avec une sortie objet](troubleshoot/runbooks.md#child-runbook-object) afin de savoir comment implémenter la logique pour interroger les résultats et utiliser [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

Vous pouvez utiliser l’applet de commande [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) pour démarrer un runbook, comme décrit dans [Démarrage d’un Runbook avec Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Il existe deux modes d’utilisation pour cette applet de commande.  Dans un mode, l’applet de commande retourne l’ID du travail enfant qui est créé pour le runbook enfant.  Dans l’autre mode, que vous activez en spécifiant le paramètre **-wait**, l’applet de commande attend que le travail enfant se termine et retourne la sortie du runbook enfant.

Le travail d’un runbook enfant démarré avec une applet de commande s’exécute dans un travail distinct du runbook parent. Ce comportement génère davantage de travaux que le démarrage de runbook en ligne et rend leur suivi plus complexe. Le parent peut démarrer plusieurs runbooks enfants de façon asynchrone, sans attendre la fin de leur exécution. Pour ce même type d’exécution en parallèle avec appel des runbooks enfants en ligne, le runbook parent doit utiliser le [mot clé parallèle](automation-powershell-workflow.md#parallel-processing).

La sortie des runbooks enfants n’est pas retournée au runbook parent de manière fiable en raison du timing. De plus, certaines variables comme $VerbosePreference, $WarningPreference et d’autres, ne peuvent pas être propagées dans les runbooks enfants. Pour éviter ces problèmes, vous pouvez démarrer les runbooks enfants comme des travaux Automation distincts à l’aide de l’applet de commande `Start-AzureRmAutomationRunbook` incluant le commutateur `-Wait`. Cela bloque le runbook parent jusqu’à ce que l’exécution du runbook enfant soit terminée.

Si vous ne voulez pas que le runbook parent se bloque durant l’attente, vous pouvez démarrer le runbook enfant à l’aide de l’applet de commande `Start-AzureRmAutomationRunbook`, mais sans utiliser le commutateur `-Wait`. Vous devez ensuite utiliser `Get-AzureRmAutomationJob` pour attendre la fin du travail, ainsi que `Get-AzureRmAutomationJobOutput` et `Get-AzureRmAutomationJobOutputRecord` pour récupérer les résultats.

Les paramètres d’un runbook enfant démarré avec une applet de commande sont fournis sous forme de table de hachage, comme décrit dans [Paramètres du runbook](start-runbooks.md#runbook-parameters). Seuls les types de données simples peuvent être utilisés. Si le Runbook possède un paramètre avec un type de données complexe, il doit être appelé en ligne.

Le contexte d’abonnement peut être perdu quand vous démarrez les runbooks enfants comme des travaux distincts. Pour que le runbook enfant exécute des applets de commande Azure RM sur l’abonnement Azure spécifique, il doit s’authentifier dans cet abonnement indépendamment du runbook parent.

Si des travaux au sein du même compte Automation utilisent plusieurs abonnements, la sélection de plus d’un abonnement dans un travail peut changer le contexte d’abonnement actuellement sélectionné pour d’autres travaux. Pour éviter ce problème, utilisez `Disable-AzureRmContextAutosave –Scope Processsave` au début de chaque runbook. Cette action enregistre le contexte uniquement pour l’exécution de ce runbook.

### <a name="example"></a>Exemples

Dans l’exemple suivant, un Runbook enfant avec paramètres est démarré et exécuté avec le paramètre Start-AzureRmAutomationRunbook -wait. À l’issue de l’exécution du Runbook, sa sortie est collectée à partir du runbook enfant. Pour utiliser `Start-AzureRmAutomationRunbook`, vous devez vous authentifier auprès de votre abonnement Azure.

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparaison des méthodes pour l’appel d’un Runbook enfant

Le tableau suivant résume les différences entre les deux méthodes applicables pour appeler un Runbook à partir d’un autre Runbook.

|  | En ligne | Applet de commande |
|:--- |:--- |:--- |
| Travail |Les Runbooks enfants s’exécutent dans la même tâche que le parent. |Une tâche distincte est créée pour le Runbook enfant. |
| Exécution |Le Runbook parent attend la fin de l’exécution du Runbook enfant avant de se poursuivre. |Le runbook parent continue immédiatement après le démarrage du runbook enfant *ou* le runbook parent attend que la tâche enfant se termine. |
| Output |Le Runbook parent peut obtenir directement la sortie du Runbook enfant. |Le runbook parent doit récupérer la sortie à partir de la tâche du runbook enfant *ou* le runbook parent peut obtenir directement la sortie du runbook enfant. |
| parameters |Les valeurs des paramètres du Runbook enfant sont spécifiées séparément et peuvent utiliser n’importe quel type de données. |Les valeurs des paramètres du runbook enfant doivent être combinées dans une table de hachage unique. Cette table de hachage peut uniquement inclure des types de données simples, tableau et objet qui utilisent la sérialisation JSON. |
| Compte Automation |Le Runbook parent peut utiliser uniquement un Runbook enfant du même compte Automation. |Les runbooks parents peuvent utiliser un runbook enfant de n’importe quel compte Automation du même abonnement Azure, ou d’un autre abonnement auquel vous avez accès. |
| Publication |Le Runbook enfant doit être publié avant la publication du Runbook parent. |Le runbook enfant doit être publié avant le démarrage du runbook parent. |

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage d'un Runbook dans Azure Automation](start-runbooks.md)
* [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md)

