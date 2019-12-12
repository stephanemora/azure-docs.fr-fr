---
title: Utilisation de modules Az dans Azure Automation
description: Cet article fournit des informations sur l’utilisation de modules Az dans Azure Automation
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 23869647b5ad04d24f0b700a1433482d4ae15fd3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850531"
---
# <a name="az-module-support-in-azure-automation"></a>Prise en charge de modules Az dans Azure Automation

Azure automation prend en charge la possibilité d’utiliser le [module Az d’Azure Powershell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) dans vos runbooks. Le module Az n’est pas importé automatiquement dans les comptes Automation, qu’ils soient nouveaux ou existants. Cet article détaille l’utilisation de modules Az avec Azure Automation.

## <a name="considerations"></a>Considérations

Il y a beaucoup d’éléments à prendre en considération lorsque vous utilisez les modules Az dans Azure Automation. Des runbooks et des modules peuvent être utilisés par des solutions de haut niveau dans votre compte Automation. Modifier des runbooks ou mettre à niveau des modules peuvent causer des problèmes avec vos runbooks. Vous devez tester consciencieusement tous les runbooks et les solutions d’un compte Automation distinct avant d’importer les nouveaux modules `Az`. Toutes les modifications apportées aux modules peuvent nuire à la solution de [démarrage/arrêt](automation-solution-vm-management.md). Il est recommandé de ne modifier ni les modules ni les runbooks dans les comptes Automation qui contiennent des solutions. Ce comportement n’est pas spécifique aux modules Az. Il doit être pris en considération lorsque vous introduisez des modifications dans votre compte Automation.

L’importation d’un module `Az` dans votre compte Automation n’importe pas systématiquement le module dans la session PowerShell que les runbooks utilisent. Les modules sont importés dans la session PowerShell, dans les situations suivantes :

* Quand une applet de commande provenant d’un module est appelée à partir d’un runbook
* Quand un runbook l’importe explicitement avec l’applet de commande `Import-Module`
* Quand un autre module dépendant du module est importé dans une session PowerShell

> [!IMPORTANT]
> Il est important de s’assurer que les runbooks d’un compte Automation importent uniquement soit des modules `Az`, soit des modules `AzureRM` dans les sessions PowerShell utilisées par des runbooks, mais pas les deux. Si `Az` est importé avant `AzureRM` dans un runbook, ce runbook s’exécute jusqu’au bout, mais une [erreur lors du référencement de la méthode get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) apparaît dans les flux de travail, et les applets de commande peuvent ne pas avoir été exécutées correctement. Si vous importez `AzureRM` et après `Az`, votre runbook continue de s’exécuter, mais une erreur s’affiche dans les flux de travail en signalant l’impossibilité d’importer ces deux modules `Az` et `AzureRM` dans la même session, ou de les utiliser dans le même runbook.

## <a name="migrating-to-az-modules"></a>Migration vers les modules Az

Il est recommandé de tester la migration en fonction de l’utilisation de modules Az plutôt que de modules AzureRM dans un compte Automation de test. Une fois ce compte Automation créé, vous pouvez utiliser les étapes suivantes pour garantir le bon déroulement de votre migration :

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Arrêter et annuler la planification de tout runbook utilisant les modules AzureRM

Pour être certain de n’exécuter aucun runbook existant qui utilise des applets de commande `AzureRM`, vous devez arrêter et annuler la planification de tous les runbooks employant des modules `AzureRM`. Vous pouvez voir quelles planifications existent et quelles planifications doivent être supprimées en exécutant l’exemple suivant :

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Il est important d’examiner chaque planification séparément, afin de vous assurer que vous pouvez les replanifier chacune ultérieurement pour vos runbooks, si nécessaire.

### <a name="import-the-az-modules"></a>Importer les modules Az

Importez uniquement les modules Az qui sont nécessaires pour vos runbooks. N’importez pas le module `Az` cumulatif, car il comprend tous les modules `Az.*` à importer. Ce conseil est valable pour tous les modules.

Le module [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) est une dépendance pour les autres modules `Az.*`. C’est la raison pour laquelle ce module doit être importé dans votre compte Automation avant de procéder à l’importation de tout autre module.

Dans votre compte Automation, sélectionnez **Modules** sous **Ressources partagées**. Cliquez sur **Parcourir la galerie** pour ouvrir la page **Parcourir la galerie**.  Dans la barre de recherche, entrez le nom du module (par exemple `Az.Accounts`). Dans la page Module PowerShell, cliquez sur **Importer** pour importer le module dans votre compte Automation.

![Importation de modules à partir d’un compte Automation](media/az-modules/import-module.png)

Ce processus d’importation peut également être réalisé par l’intermédiaire de [PowerShell Gallery](https://www.powershellgallery.com) en recherchant le module. Une fois le module trouvé, sélectionnez-le puis, sous l’onglet **Azure Automation**, cliquez sur **Deploy to Azure Automation** (Déployer sur Azure Automation).

![Importation des modules directement à partir de la galerie](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Tester vos runbooks

Dès lors que les modules `Az` sont importés dans votre compte Automation, vous pouvez commencer à modifier vos runbooks pour utiliser le module Az à la place. La plupart des applets de commande portent le même nom, à l’exception de `AzureRM` qui a été remplacé par `Az`. Pour obtenir la liste des modules qui ne suivent pas ce processus, consultez [la liste des exceptions](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Un moyen de tester vos runbooks avant de les modifier pour utiliser les nouvelles applets de commande consiste à utiliser `Enable-AzureRMAlias -Scope Process` au début d’un runbook. Grâce à cet ajout dans votre runbook, celui-ci peut s’exécuter sans modification.

## <a name="after-migration-details"></a>Détails post-migration

Une fois la migration terminée, ne démarrez plus les runbooks à l’aide des modules `AzureRM` sur le compte. Nous vous recommandons également de ne pas importer ni de mettre à jour des modules `AzureRM` sur ce compte. Désormais, considérez que ce compte a migré vers `Az` et qu’il fonctionne uniquement avec des modules `Az`. Lorsqu’un compte Automation est créé, les modules `AzureRM` existants continuent d’être installés, et les runbooks de tutoriel sont toujours créés avec des applets de commande `AzureRM`. Ces runbooks ne doivent pas être exécutés.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation des modules Az, consultez [Bien démarrer avec le module Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
