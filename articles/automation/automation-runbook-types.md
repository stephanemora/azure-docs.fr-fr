---
title: Types de Runbooks Azure Automation
description: 'Décrit les différents types de Runbooks que vous pouvez utiliser dans Azure Automation et les considérations à prendre en compte pour déterminer le type à utiliser. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1991b7c227e62efcd8c0b637f3e732d737cabb34
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850684"
---
# <a name="azure-automation-runbook-types"></a>Types de Runbooks Azure Automation

Azure Automation prend en charge plusieurs types de Runbook qui sont brièvement décrits dans le tableau ci-dessous.  Les sections suivantes fournissent de plus amples informations sur chaque type, notamment des considérations sur l'utilisation de chacun de ces types.

| Type | Description |
|:--- |:--- |
| [Graphique](#graphical-runbooks)|Basé sur Windows PowerShell et créé et modifié entièrement dans l'éditeur graphique du portail Azure. |
| [Graphique workflow PowerShell](#graphical-runbooks)|Basé sur le workflow Windows PowerShell et créé et modifié entièrement dans l'éditeur graphique du portail Azure. |
| [PowerShell](#powershell-runbooks) |Runbook texte basé sur le script Windows PowerShell. |
| [Workflow PowerShell](#powershell-workflow-runbooks)|Runbook texte basé sur le workflow Windows PowerShell. |
| [Python](#python-runbooks) |Runbook de texte basé sur Python. |

## <a name="graphical-runbooks"></a>Runbooks graphiques

[Graphique](automation-runbook-types.md#graphical-runbooks) and Graphique PowerShell Workflow runbooks are created and edited with the graphical editor in the Azure portal.  Vous pouvez les exporter vers un fichier puis les importer dans un autre compte Automation. Cependant, vous ne pouvez ni les créer ni les modifier avec un autre outil. Les Runbooks graphiques génèrent un code PowerShell, mais vous ne pouvez pas directement afficher ou modifier ce code. Les runbooks graphiques ne peuvent pas être convertis en [formats texte](automation-runbook-types.md) et un runbook texte ne peut pas être converti en format graphique. Les runbooks graphiques peut être converti en runbooks de flux de travail PowerShell graphique pendant l’importation et vice versa.

### <a name="advantages"></a>Avantages

* Insertion visuelle/lien/configuration d’un modèle de création
* Concentration sur la circulation des flux de données dans le processus
* Représentation visuelle des processus de gestion
* Inclusion d’autres Runbooks en tant que Runbooks enfants pour créer des flux de travail de niveau élevé
* Programmation modulaire favorisée

### <a name="limitations"></a>Limites

* Impossible de modifier le Runbook en dehors du portail Azure.
* Peut nécessiter une activité de code contenant le code PowerShell pour exécuter une logique complexe.
* Impossible d'afficher ou de modifier directement le code PowerShell créé par le workflow graphique. Vous pouvez afficher le code créé dans toute activité de code.
* Ne peut pas être exécuté sur un runbook Worker hybride Linux

## <a name="powershell-runbooks"></a>Runbooks PowerShell

Les Runbooks PowerShell sont basés sur Windows PowerShell.  Vous modifiez directement le code du Runbook à l'aide de l'éditeur de texte du portail Azure.  Vous pouvez également utiliser n'importe quel éditeur de texte hors ligne et [importer le Runbook](manage-runbooks.md) dans Azure Automation.

### <a name="advantages"></a>Avantages

* Implémentez tout type de la logique complexe avec le code PowerShell sans la complexité supplémentaire liée au workflow PowerShell.
* Le Runbook démarre plus rapidement que les Runbooks de workflow PowerShell dans la mesure où il n'a pas besoin d'être compilé avant l'exécution.
* Peut être exécuté dans Azure, ou sur des runbooks Worker hybrides Linux et Windows

### <a name="limitations"></a>Limites

* Doit être familiarisé avec les scripts PowerShell.
* Impossible d'utiliser un [traitement en parallèle](automation-powershell-workflow.md#parallel-processing) pour effectuer plusieurs actions en parallèle.
* Impossible d'utiliser des [points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre le runbook en cas d'erreur.
* Les runbooks de flux de travail PowerShell et les runbooks graphiques peuvent uniquement être inclus en tant que runbooks enfants à l'aide de l'applet de commande Start-AzureAutomationRunbook, ce qui crée un travail.

### <a name="known-issues"></a>Problèmes connus

Voici les problèmes connus actuels rencontrés avec les Runbooks PowerShell.

* Les Runbooks PowerShell ne peuvent pas récupérer une [ressource variable](automation-variables.md) non chiffrée avec une valeur null.
* Les runbooks PowerShell ne peuvent pas récupérer une [ressource variable](automation-variables.md) dont le nom contient le symbole *~* .
* Get-Process dans une boucle d’un Runbook PowerShell peut se bloquer après environ 80 itérations.
* Un runbook PowerShell peut échouer s’il tente d'écrire une grande quantité de données à la fois dans le flux de sortie.   Vous pouvez généralement contourner ce problème en exportant uniquement les informations dont vous avez besoin lorsque vous travaillez avec des objets volumineux.  Par exemple, au lieu d’exporter un contenu de type *Get-Process*, vous pouvez exporter uniquement les champs requis avec *Get-Process | Select ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Runbooks de workflow PowerShell

Les Runbooks de workflow PowerShell sont des Runbooks texte basés sur un [workflow Windows PowerShell](automation-powershell-workflow.md).  Vous modifiez directement le code du Runbook à l'aide de l'éditeur de texte du portail Azure.  Vous pouvez également utiliser n'importe quel éditeur de texte hors ligne et [importer le Runbook](manage-runbooks.md) dans Azure Automation.

### <a name="advantages"></a>Avantages

* Implémentez tout type de logique complexe avec le code de workflow PowerShell.
* Utilisez des [points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre le runbook en cas d'erreur.
* Utilisez un [traitement en parallèle](automation-powershell-workflow.md#parallel-processing) pour effectuer plusieurs actions en parallèle.
* Peut inclure d'autres Runbooks graphiques et Runbooks de workflow PowerShell en tant que Runbooks enfants afin de créer des workflows de haut niveau.

### <a name="limitations"></a>Limites

* L’auteur doit être familiarisé avec les workflows PowerShell.
* Un Runbook doit pouvoir gérer la complexité supplémentaire liée au workflow PowerShell, notamment les [objets désérialisés](automation-powershell-workflow.md#code-changes).
* Un Runbook prend plus de temps à démarrer que les Runbooks PowerShell car il doit être compilé avant l'exécution.
* Les runbooks PowerShell peuvent uniquement être inclus en tant que runbooks enfants à l'aide de l'applet de commande Start-AzureAutomationRunbook, ce qui crée un travail.
* Ne peut pas être exécuté sur un runbook Worker hybride Linux

## <a name="python-runbooks"></a>Runbooks Python

Compilation de runbooks Python sous Python 2.  Vous pouvez modifier directement le code du runbook à l’aide de l’éditeur de texte dans le portail Azure ou avec un éditeur de texte en mode hors connexion, puis [importer le runbook](manage-runbooks.md) dans Azure Automation.

### <a name="advantages"></a>Avantages

* Utilisez les bibliothèques Python robustes.
* Peut être exécuté dans Azure, ou sur des runbooks Worker hybrides Linux. Les runbooks Worker hybrides Windows sont pris en charge si [Python 2.7](https://www.python.org/downloads/release/latest/python2) est installé.

### <a name="limitations"></a>Limites

* Doit être familiarisé avec les scripts Python.
* Seul Python 2 est pris en charge pour le moment, ce qui signifie que les fonctions spécifiques à Python 3 échouent.
* Pour utiliser des bibliothèques tierces, vous devez [importer le package](python-packages.md) dans le compte Automation pour qu’il soit utilisé.

## <a name="considerations"></a>Considérations

Prenez en compte les considérations supplémentaires suivantes pour déterminer le type à utiliser pour un runbook donné.

* Vous ne pouvez pas convertir de runbooks du type graphique au type textuel ou vice versa.
* Il existe des limitations à l’utilisation de Runbooks de différents types comme un Runbook enfant. Pour plus d’informations, consultez la page [Runbooks enfants dans Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création de Runbooks graphiques, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md)
* Pour comprendre les différences entre PowerShell et les workflows PowerShell pour les Runbooks, consultez [Apprentissage du workflow Windows PowerShell](automation-powershell-workflow.md)
* Pour en savoir plus sur la création ou l’importation d’un Runbook, consultez [Création ou importation d’un Runbook](manage-runbooks.md)
* Pour plus d’informations sur PowerShell, notamment le langage de référence et les modules d’apprentissage, consultez la [Documentation PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
