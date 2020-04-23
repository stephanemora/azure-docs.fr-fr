---
title: Types de Runbooks Azure Automation
description: Décrit les différents types de runbooks que vous pouvez utiliser dans Azure Automation et les considérations pour déterminer le type à utiliser.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535517"
---
# <a name="azure-automation-runbook-types"></a>Types de Runbooks Azure Automation

Le service d’automatisation des processus Azure Automation prend en charge plusieurs types de runbooks, conformément à la définition du tableau suivant. Pour en savoir plus sur l’environnement d’automatisation des processus, consultez [Exécution d’un runbook dans Azure Automation](automation-runbook-execution.md).

| Type | Description |
|:--- |:--- |
| [Graphique](#graphical-runbooks)|Runbook graphique basé sur Windows PowerShell et créé et modifié entièrement dans l'éditeur graphique du portail Azure. |
| [Graphique workflow PowerShell](#graphical-runbooks)|Runbook graphique basé sur le workflow Windows PowerShell et créé et modifié entièrement dans l'éditeur graphique du portail Azure. |
| [PowerShell](#powershell-runbooks) |Runbook texte basé sur les scripts Windows PowerShell. |
| [Workflow PowerShell](#powershell-workflow-runbooks)|Runbook texte basé sur le workflow de scripts Windows PowerShell. |
| [Python](#python-runbooks) |Runbook de texte basé sur les scripts Python. |

Prenez en compte les considérations suivantes pour déterminer le type à utiliser pour un runbook donné.

* Vous ne pouvez pas convertir de runbooks du type graphique au type texte ou vice versa.
* Il existe des limitations lorsque des runbooks de différents types sont utilisés comme runbooks enfants. Pour plus d’informations, consultez la page [Runbooks enfants dans Azure Automation](automation-child-runbooks.md).

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version à l’aide de la rubrique [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Runbooks graphiques

Vous pouvez créer et modifier des runbooks graphiques de workflow PowerShell avec l’éditeur graphique du portail Azure. Vous ne pouvez cependant ni créer ni modifier ce type de runbook avec un autre outil. Principales fonctionnalités des runbooks graphiques :

* Ils peuvent être exportés vers des fichiers de votre compte Automation, puis importés dans un autre compte Automation. 
* Générer du code PowerShell. 
* Les runbooks graphiques peuvent être convertis en runbooks de workflow PowerShell pendant l’importation et vice versa. 

### <a name="advantages"></a>Avantages

* Utilisation d’un modèle de création visuel insert-link-configure.
* Concentration sur la circulation des flux de données dans tout le processus.
* Représentez visuellement les processus de gestion.
* Inclusion d’autres runbooks en tant que runbooks enfants pour créer des workflows de niveau élevé.
* Programmation modulaire favorisée.

### <a name="limitations"></a>Limites

* Impossible de créer ou de modifier en dehors du portail Azure.
* Peut nécessiter une activité de code contenant du code PowerShell pour exécuter une logique complexe.
* Impossible d’effectuer une conversion vers l’un des [formats de texte](automation-runbook-types.md) ou de convertir un runbook de texte au format graphique. 
* Impossible d'afficher ou de modifier directement du code PowerShell créé par le workflow graphique. Vous pouvez afficher le code créé dans toute activité de code.
* Impossible d’exécuter des runbooks sur un Runbook Worker hybride. Consultez [Automatiser les ressources de votre centre de données ou de votre cloud à l’aide d’un Runbook Worker hybride](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Runbooks PowerShell

Les Runbooks PowerShell sont basés sur Windows PowerShell. Vous modifiez directement le code du Runbook à l'aide de l'éditeur de texte du portail Azure.  Vous pouvez également utiliser n'importe quel éditeur de texte hors ligne et [importer le Runbook](manage-runbooks.md) dans Azure Automation.

### <a name="advantages"></a>Avantages

* Implémentez tout type de la logique complexe avec le code PowerShell sans la complexité supplémentaire liée au workflow PowerShell.
* Démarrez plus rapidement que les runbooks de workflow PowerShell dans la mesure où ils n’ont pas besoin d'être compilés avant l'exécution.
* Exécutez dans Azure et sur des Runbook Workers hybrides pour Windows et Linux.

### <a name="limitations"></a>Limites

* Vous devez être familiarisé avec les scripts PowerShell.
* Les runbooks ne peuvent pas utiliser un [traitement en parallèle](automation-powershell-workflow.md#parallel-processing) pour effectuer plusieurs actions en parallèle.
* Les runbooks ne peuvent pas utiliser de [points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre le runbook en cas d'erreur.
* Vous pouvez inclure uniquement les runbooks de workflow PowerShell et les runbooks graphiques comme runbooks enfants à l'aide de l'applet de commande [Start-AzureAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0), ce qui crée un travail.

### <a name="known-issues"></a>Problèmes connus

Voici les problèmes connus actuels rencontrés avec les runbooks PowerShell :

* Les runbooks PowerShell ne peuvent pas récupérer une [ressource variable](automation-variables.md) non chiffrée avec une valeur null.
* Les runbooks PowerShell ne peuvent pas récupérer une ressource variable dont le nom contient le symbole `*~*`.
* Une opération [Get-Process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) dans une boucle d’un runbook PowerShell peut se bloquer après environ 80 itérations.
* Un runbook PowerShell peut échouer s’il essaie d'écrire une grande quantité de données à la fois dans le flux de sortie. Vous pouvez généralement contourner ce problème. Pour ce faire, le runbook doit sortir uniquement les informations nécessaires pour travailler avec des objets volumineux. Par exemple, au lieu d’utiliser `Get-Process` sans aucune limitation, vous pouvez faire en sorte que l’applet de commande génère uniquement les paramètres requis, comme dans `Get-Process | Select ProcessName, CPU`.

## <a name="powershell-workflow-runbooks"></a>Runbooks de workflow PowerShell

Les Runbooks de workflow PowerShell sont des Runbooks texte basés sur un [workflow Windows PowerShell](automation-powershell-workflow.md). Vous modifiez directement le code du Runbook à l'aide de l'éditeur de texte du portail Azure. Vous pouvez également utiliser n'importe quel éditeur de texte hors ligne et [importer le Runbook](manage-runbooks.md) dans Azure Automation.

### <a name="advantages"></a>Avantages

* Implémentez tout type de logique complexe avec le code de workflow PowerShell.
* Utilisez des [points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre l’opération en cas d'erreur.
* Utilisez un [traitement en parallèle](automation-powershell-workflow.md#parallel-processing) pour mener plusieurs actions en parallèle.
* Peut inclure d'autres runbooks graphiques et des runbooks de workflow PowerShell en tant que runbooks enfants afin de créer des workflows de haut niveau.

### <a name="limitations"></a>Limites

* Vous devez être familiarisé avec les workflows PowerShell.
* Les runbooks doivent pouvoir gérer la complexité supplémentaire liée au workflow PowerShell, notamment les [objets désérialisés](automation-powershell-workflow.md#code-changes).
* Les runbooks prennent plus de temps à démarrer que les runbooks PowerShell, car il doivent être compilés avant l'exécution.
* Vous pouvez inclure uniquement des runbooks PowerShell en tant que runbooks enfants à l’aide de l’applet de commande `Start-AzAutomationRunbook`.
* Les runbooks ne peuvent pas être exécutés sur un Runbook Worker hybride.

## <a name="python-runbooks"></a>Runbooks Python

Compilation de runbooks Python sous Python 2. Vous pouvez modifierz directement le code du runbook à l'aide de l'éditeur de texte du portail Azure. Vous pouvez également utiliser un éditeur de texte hors ligne et [importer le runbook](manage-runbooks.md) dans Azure Automation.

### <a name="advantages"></a>Avantages

* Utilisation des bibliothèques Python robustes.
* Possibilité d’exécution dans Azure ou sur Runbook Worker hybride. Les runbooks Worker hybrides Windows sont pris en charge si [Python 2.7](https://www.python.org/downloads/release/latest/python2) est installé.

### <a name="limitations"></a>Limites

* Vous devez être familiarisé avec les scripts Python.
* Seul Python 2 est actuellement pris en charge. Toutes les fonctions spécifiques à Python 3 échouent.
* Pour utiliser des bibliothèques tierces, vous devez [importer les packages](python-packages.md) dans le compte Automation.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création de runbooks graphiques, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).
* Pour comprendre les différences entre PowerShell et les workflows PowerShell pour les runbooks, consultez [Apprentissage du workflow Windows PowerShell](automation-powershell-workflow.md).
* Pour en savoir plus sur la création ou l’importation d’un runbook, consultez [Gérer des runbooks dans Azure Automation](manage-runbooks.md).
* Pour en savoir plus sur PowerShell, notamment le langage de référence et les modules d’apprentissage, consultez la [Documentation PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
