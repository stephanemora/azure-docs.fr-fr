---
title: Gestion des erreurs dans les runbooks graphiques Azure Automation
description: Cet article décrit comment implémenter une logique de gestion des erreurs dans les runbooks graphiques Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: dec715ec6741f4429d8b1d4f620ef3cb82d4c1d3
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649974"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Gestion des erreurs dans les runbooks graphiques Azure Automation

Un principe de conception clé à prendre en compte pour votre runbook graphique Azure Automation est l’identification des problèmes que le runbook peut rencontrer en cours d’exécution. Ces problèmes peuvent être liés à la réussite, aux états d’erreur attendus et aux conditions d’erreur inattendue.

Souvent, si une erreur sans fin d’exécution se produit avec une activité runbook, Windows PowerShell gère l’activité en traitant toute activité qui suit, quelle que soit l’erreur. L’erreur est susceptible de générer une exception, mais l’activité suivante peut toujours s’exécuter.

Votre runbook graphique doit inclure un code de gestion des erreurs pour traiter les problèmes d’exécution. Pour valider la sortie d’une activité ou gérer une erreur, vous pouvez utiliser une activité de code PowerShell, définir une logique conditionnelle sur le lien de sortie de l’activité, ou appliquer une autre méthode.

Les runbooks graphiques Azure Automation ont été améliorés avec la possibilité d’inclure une gestion des erreurs. Vous pouvez maintenant convertir les exceptions en erreurs sans fin d’exécution et créer des liens d’erreur entre les activités. Ce processus amélioré permet à votre runbook d’intercepter des erreurs et de gérer des conditions réalisées ou inattendues. 

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="powershell-error-types"></a>Types d’erreurs PowerShell

Les erreurs PowerShell qui peuvent survenir lors de l’exécution du runbook sont des erreurs avec ou sans fin d’exécution.
 
### <a name="terminating-error"></a>Erreur avec fin d’exécution

Une erreur avec fin d’exécution est erreur grave survenant en cours d’exécution, qui arrête entièrement l’exécution d’une ou d’un script. Il peut s’agir de cmdlets inexistantes, d’erreurs de syntaxe qui empêchent une cmdlet de s’exécuter ou d’autres erreurs irrécupérables.

### <a name="non-terminating-error"></a>Erreur sans fin d’exécution

Une erreur sans fin d’exécution est une erreur sans gravité n’empêche pas la poursuite de l’exécution en dépit de la condition d’erreur. Il peut s’agit d’erreurs opérationnelles comme un fichier introuvable et de problèmes d’autorisations.

## <a name="when-to-use-error-handling"></a>Quand utiliser la gestion des erreurs

Utilisez la gestion des erreurs dans votre runbook quand une activité critique génère une erreur ou une exception. Il est important d’empêcher le traitement de l’activité suivante dans le runbook et de gérer l’erreur de façon appropriée. Le traitement de l’erreur est particulièrement vital lorsque vos runbooks prennent en charge un processus d’entreprise ou de service.

Pour chaque activité susceptible de générer une erreur, vous pouvez ajouter un lien d’erreur pointant vers une autre activité. L’activité de destination peut être de tout type : activité de code, appel de cmdlet, appel d’un autre runbook, etc. L’activité de destination peut également avoir des liens sortants, qu’il s’agisse de liens ordinaires ou de liens d’erreur. Les liens permettent au runbook d’implémenter une logique complexe de gestion des erreurs sans avoir recours à une activité de code.

La pratique recommandée consiste à créer un runbook de gestion des erreurs dédié avec des fonctionnalités communes, mais cela n’a rien d’obligatoire. Par exemple, envisagez un runbook qui tente de démarrer une machine virtuelle et d’y installer une application. Si la machine virtuelle ne démarre pas correctement :

1. Elle envoie une notification concernant le problème.
2. Elle démarre un autre runbook qui configure automatiquement une nouvelle machine virtuelle.

Une solution consiste à avoir un lien d’erreur dans le runbook, qui pointe vers une activité qui gère l’étape une. Par exemple, le runbook peut connecter la cmdlet **Write-Warning** à une activité pour l’étape deux, telle que la cmdlet [Start-AzureAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0).

Vous pouvez également généraliser ce comportement dans de nombreux runbooks en intégrant ces deux activités dans un runbook de gestion des erreurs séparé, conformément aux instructions suggérées précédemment. Avant que votre runbook d’origine appelle ce runbook de gestion des erreurs, il peut créer message personnalisé à partir de ses données, puis le transmettre en tant que paramètre au runbook de gestion des erreurs.

## <a name="how-to-use-error-handling"></a>Quand utiliser la gestion des erreurs

Chaque activité de votre runbook possède un paramètre de configuration permettant de transformer les exceptions en erreurs sans fin d’exécution. Par défaut, ce paramètre est désactivé. Nous vous recommandons d’activer ce paramètre sur toute activité où votre runbook gère des erreurs. L’activation de cette configuration garantit que le runbook gère les erreurs tant avec fin que sans fin d’exécution dans l’activité à l’aide d’un lien d’erreur.  

Une fois que vous avez activé le paramètre de configuration, configurez votre runbook pour créer une activité qui gère l’erreur. Si l’activité génère une erreur, les liens d’erreur sortants sont suivis. Les liens standard ne sont pas suivis, même si l’activité produit également une sortie ordinaire.<br><br> ![Exemple de lien d’erreur d’un runbook Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

Dans l’exemple suivant, un runbook récupère une variable contenant le nom d’ordinateur d’une machine virtuelle. Il tente ensuite de démarrer la machine virtuelle avec l’activité suivante.<br><br> ![Exemple de gestion des erreurs d’un runbook Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

L’activité **Get-AutomationVariable** et la cmdlet [AzureRmVm-Start](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) sont configurées pour convertir des exceptions en erreurs. Si l’obtention de la variable ou le démarrage de la machine virtuelle posent problème, le code génère des erreurs.<br><br> ![Paramètres d’activité de gestion des erreurs d’un runbook Automation](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png).

Les liens d’erreur conduisent de ces activités vers une seule activité de code de **gestion des erreurs**. Cette activité est configurée avec une simple expression PowerShell qui utilise le mot clé *Throw* pour arrêter le traitement, ainsi qu’avec `$Error.Exception.Message` pour obtenir le message décrivant l’exception actuelle.<br><br> ![Exemple de code de gestion d’erreurs d’un runbook Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les liens et les types de lien dans les runbooks graphiques, voir [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow).

* Pour en savoir plus sur l’exécution d’un runbook, la surveillance des tâches de runbook et d’autres détails techniques, voir [Exécution d’un Runbook dans Azure Automation](automation-runbook-execution.md).