---
title: Démarrer un Runbook dans Azure Automation
description: Résume les différentes méthodes qui peuvent être utilisées pour démarrer un Runbook dans Azure Automation et fournit des détails sur l'utilisation du portail Azure et de Windows PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 71dd83db02537ed12dc2e711127e32d90603af6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79227521"
---
# <a name="start-a-runbook-in-azure-automation"></a>Démarrer un Runbook dans Azure Automation

Le tableau suivant vous aide à déterminer la méthode de démarrage d'un Runbook dans Azure Automation la plus appropriée à votre scénario. Cet article inclut des informations détaillées sur le démarrage d'un Runbook avec le portail Azure et avec Windows PowerShell. Des informations supplémentaires sur les autres méthodes sont fournies dans différentes documentations accessibles depuis les liens ci-dessous.

| **Méthode** | **Caractéristiques** |
| --- | --- |
| [Azure portal](#start-a-runbook-with-the-azure-portal) |<li>Méthode la plus simple avec une interface utilisateur interactive.<br> <li>Formulaire pour fournir les valeurs de paramètres simples.<br> <li>Suivi aisé de l'état des tâches.<br> <li>Accès authentifié avec connexion à Azure. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Appel à partir de la ligne de commande avec les applets de commande Windows PowerShell.<br> <li>Possibilité d'inclusion dans une solution automatisée à plusieurs étapes.<br> <li>Demande authentifiée avec un certificat ou un principal du service / principal d'utilisateur OAuth.<br> <li>Fourniture de valeurs de paramètres simples et complexes.<br> <li>Suivi de l’état des tâches.<br> <li>Client requis pour prendre en charge les applets de commande PowerShell. |
| [API Azure Automation](/rest/api/automation/) |<li>Méthode la plus souple, mais également la plus complexe.<br> <li>Appel à partir de n'importe quel code personnalisé qui peut envoyer des requêtes HTTP.<br> <li>Requête authentifiée avec un certificat ou un principal du service / principal d'utilisateur Oauth.<br> <li>Fourniture de valeurs de paramètres simples et complexes. *Si vous appelez un Runbook Python à l'aide de l'API, la charge utile JSON doit être sérialisée.*<br> <li>Suivi de l’état des tâches. |
| [Webhooks](automation-webhooks.md) |<li>Démarrage d'un Runbook à partir d'une simple requête HTTP.<br> <li>Authentification avec un jeton de sécurité dans l'URL.<br> <li>Le client ne peut pas remplacer les valeurs de paramètre spécifiées lors de la création du webhook. Le Runbook peut définir un paramètre unique qui est rempli avec les détails de la requête HTTP.<br> <li>Aucune possibilité de suivre l’état des tâches via l’URL du Webhook. |
| [Répondre à une alerte Azure](../log-analytics/log-analytics-alerts.md) |<li>Démarrer un Runbook en réponse à une alerte Azure.<br> <li>Configurer webhook pour Runbook et lien vers l'alerte.<br> <li>Authentification avec un jeton de sécurité dans l'URL. |
| [Planification](automation-schedules.md) |<li>Démarrage automatique du runbook selon une planification horaire, quotidienne, hebdomadaire ou mensuelle.<br> <li>Manipulation de la planification via le portail Azure, les applets de commande PowerShell ou les API Azure.<br> <li>Fourniture des valeurs de paramètres à utiliser avec la planification. |
| [À partir d'un autre Runbook](automation-child-runbooks.md) |<li>Utilisation d’un Runbook en tant qu’activité d’un autre Runbook.<br> <li>Utile pour les fonctionnalités utilisées par plusieurs Runbooks.<br> <li>Fourniture des valeurs de paramètres au Runbook enfant et utilisation de la sortie dans le Runbook parent. |

L’image suivante illustre le processus détaillé du cycle de vie d’un Runbook. Elle illustre différentes formes de démarrage d'un Runbook dans Azure Automation, les composants requis pour que Runbook Worker hybride exécute des Runbooks Azure Automation, ainsi que les interactions entre les différents composants. Pour en savoir plus sur l’exécution des Runbooks Automation dans votre centre de données, consultez l’article [Runbooks Workers hybrides](automation-hybrid-runbook-worker.md)

![Architecture de runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Démarrer un Runbook avec le portail Azure

1. Dans le portail Azure, sélectionnez **Automation**, puis cliquez sur le nom d'un compte Automation.
2. Dans le menu Hub, sélectionnez **Runbooks**.
3. Sur la page **Runbooks**, sélectionnez un runbook, puis cliquez sur **Démarrer**.
4. Si le Runbook possède des paramètres, vous êtes invité à fournir des valeurs, avec une zone de texte pour chaque paramètre. Pour plus d'informations sur les paramètres, consultez [Paramètres du Runbook](#runbook-parameters) .
5. Sur la page **Tâche**, vous pouvez afficher l’état du travail du runbook.

## <a name="start-a-runbook-with-powershell"></a>Démarrer un Runbook avec PowerShell

Vous pouvez utiliser l’applet de commande [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) pour démarrer un Runbook avec Windows PowerShell. L'exemple de code suivant démarre un Runbook appelé Test-Runbook.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook retourne un objet de traitement que vous pouvez utiliser pour suivre son état une fois que le Runbook a démarré. Vous pouvez ensuite utiliser cet objet de travail avec [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) pour déterminer l’état du travail et avec [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) pour obtenir sa sortie. L'exemple de code suivant démarre un Runbook appelé Test-Runbook, attend qu'il ait terminé, puis affiche sa sortie.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Si le Runbook requiert des paramètres, vous devez fournir ceux-ci sous forme de [table de hachage](https://technet.microsoft.com/library/hh847780.aspx). La clé de la table de hachage doit correspondre au nom du paramètre, et la valeur est la valeur du paramètre. L'exemple suivant montre comment démarrer un Runbook avec deux paramètres de chaîne nommés FirstName et LastName, un entier nommé RepeatCount et un paramètre booléen nommé Show. Pour plus d'informations sur les paramètres, consultez [Paramètres du Runbook](#runbook-parameters) .

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Paramètres du Runbook

Quand vous démarrez un Runbook à partir du portail Azure ou de Windows PowerShell, l’instruction est envoyée par le biais du service web Azure Automation. Ce service ne prend pas en charge les paramètres comportant des types de données complexes. Si vous devez fournir une valeur pour un paramètre complexe, vous devez l’appeler en ligne à partir d’un autre Runbook, comme décrit dans [Runbooks enfants dans Azure Automation](automation-child-runbooks.md).

Le service web Azure Automation fournit des fonctionnalités spécifiques pour les paramètres en utilisant certains types de données, comme décrit dans les sections suivantes :

### <a name="named-values"></a>Valeurs nommées

Si le paramètre est un type de données [object], vous pouvez utiliser le format JSON suivant pour lui envoyer une liste de valeurs nommées : *{Nom1:'Valeur1', Nom2:'Valeur2', Nom3:'Valeur3'}* . Ces valeurs doivent avoir des types simples. Le Runbook reçoit le paramètre comme [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) avec des propriétés correspondant à chaque valeur nommée.

Considérez le Runbook de test suivant qui accepte un paramètre nommé user.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Le texte suivant peut être utilisé pour le paramètre user.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Il s'ensuit la sortie suivante :

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tableaux

Si le paramètre est un tableau comme [array] ou [string[]], vous pouvez utiliser le format JSON suivant pour lui envoyer une liste de valeurs : *[Valeur1, Valeur2, Valeur3]* . Ces valeurs doivent avoir des types simples.

Considérez le Runbook de test suivant qui accepte un paramètre nommé *user*.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Le texte suivant peut être utilisé pour le paramètre user.

```input
["Joe","Smith",2,true]
```

Il s'ensuit la sortie suivante :

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Informations d'identification

Si le paramètre est un type de données **PSCredential**, vous pouvez fournir le nom d'une [ressource d'informations d'identification](automation-credentials.md)Azure Automation. Le Runbook récupère la ressource d'informations d'identification portant le nom que vous spécifiez.

Considérez le Runbook de test suivant qui accepte un paramètre nommé credential.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Le texte suivant peut être utilisé pour le paramètre user en supposant qu'il existe une ressource d'informations d'identification nommée *My Credential*.

```input
My Credential
```

En supposant que le nom d'utilisateur des informations d'identification soit *jsmith*, il s'ensuit le résultat suivant :

```output
jsmith
```

## <a name="next-steps"></a>Étapes suivantes

* L’architecture des Runbooks dans cet article fournit une vue d’ensemble globale des ressources de gestion des Runbooks dans Azure et localement avec Runbook Worker hybride. Pour en savoir plus sur l’exécution des Runbooks Automation dans votre centre de données, consultez l’article [Runbooks Workers hybrides](automation-hybrid-runbook-worker.md).
* Pour en savoir plus sur la création de Runbooks modulaires à utiliser par d’autres Runbooks pour des fonctions spécifiques ou communes, consultez [Runbooks enfants](automation-child-runbooks.md).
* Pour plus d’informations sur PowerShell, notamment le langage de référence et les modules d’apprentissage, consultez la [Documentation PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
