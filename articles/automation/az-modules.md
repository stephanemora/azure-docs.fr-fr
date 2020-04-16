---
title: Prise en charge de modules Az dans Azure Automation
description: Cet article fournit des informations sur l’utilisation de modules Az dans Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: a8d6d25a2ba7f0040b13982f14f3d6081ac32f15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637990"
---
# <a name="az-module-support-in-azure-automation"></a>Prise en charge de modules Az dans Azure Automation

Azure Automation prend en charge l’utilisation du [module Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) dans vos runbooks. Le module cumulatif Az n’est pas importé automatiquement dans les comptes Automation, qu’ils soient nouveaux ou existants. 

## <a name="considerations"></a>Considérations

Plusieurs éléments doivent être pris en considération au moment d’utiliser les modules Az dans Azure Automation :

* Les solutions de niveau supérieur de votre compte Automation peuvent utiliser des runbooks et des modules. Par conséquent, le fait de modifier des runbooks ou de mettre à niveau des modules peut potentiellement occasionner des problèmes au niveau de vos solutions. Vous devez tester consciencieusement tous les runbooks et les solutions d’un compte Automation distinct avant d’importer de nouveaux modules Az. 

* Toutes les modifications apportées aux modules peuvent nuire à la solution de [démarrage/arrêt](automation-solution-vm-management.md). 

* Le fait d’importer un module Az dans votre compte Automation n’a pas pour effet d’importer automatiquement le module dans la session PowerShell utilisée par les runbooks. Les modules sont importés dans la session PowerShell, dans les situations suivantes :

    * Quand un runbook appelle une applet de commande à partir d’un module
    * Quand un runbook importe le module explicitement avec l’applet de commande `Import-Module`
    * Quand un runbook importe un autre module dépendant du module

> [!NOTE]
> Nous vous déconseillons de modifier les modules et les runbooks dans les comptes Automation qui contiennent des solutions. Cette disposition ne s’applique pas qu’aux modules Az. Vous devez en tenir compte au moment d’apporter des modifications à votre compte Automation.

> [!IMPORTANT]
> Veillez à ce que les runbooks d’un compte Automation importent les modules Az ou les modules [AzureRM](https://www.powershellgallery.com/packages/AzureRM/6.13.1), mais pas les deux, dans une session PowerShell. Si un runbook importe les modules Az avant les modules AzureRM, le runbook s’exécute. Cependant, une erreur faisant référence à l’applet de commande [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) s’affiche dans les flux de tâches et les applets de commande peuvent ne pas s’exécuter correctement. Si le runbook importe les modules AzureRM avant les modules Az, le runbook s’exécute également. Dans ce cas, vous obtenez néanmoins une erreur dans les flux de tâches indiquant que les modules Az et AzureRM ne peuvent pas être importés dans une même session ni utilisés dans un même runbook.

## <a name="migrating-to-az-modules"></a>Migration vers les modules Az

Nous vous recommandons de tester une migration vers les modules Az dans un compte Automation de test. Une fois que vous avez créé le compte, vous pouvez suivre les instructions fournies dans cette section pour utiliser les modules.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Arrêter et annuler la planification de tous les runbooks utilisant des modules AzureRM

Pour être certain de n’exécuter aucun runbook existant qui utilise des modules AzureRM, arrêtez et annulez la planification de tous les runbooks concernés. Vous pouvez identifier les planifications existantes et les planifications à supprimer en exécutant du code similaire à cet exemple :

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Il est important d’examiner chaque planification séparément pour s’assurer qu’elles pourront être replanifiées dans l’avenir, si nécessaire.

### <a name="import-the-az-modules"></a>Importer les modules Az

>[!NOTE]
>Faites en sorte que vos runbooks n’importent que les modules Az nécessaires. N’importez pas le module Az cumulatif, car il comporte tous les modules Az. Ce conseil est valable pour tous les modules.

Le module [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) constitue une dépendance vis-vis des autres modules Az. C’est pour cette raison que vos runbooks doivent importer ce module dans votre compte Automation avant d’importer tout autre module.

Pour importer les modules sur le portail Azure :

1. À partir de votre compte Automation, sélectionnez **Modules** sous **Ressources partagées**. 
2. Cliquez sur **Parcourir la galerie** pour ouvrir la page Parcourir la galerie.  
3. Dans la barre de recherche, entrez le nom du module, par exemple `Az.Accounts`. 
4. Dans la page Module PowerShell, cliquez sur **Importer** pour importer le module dans votre compte Automation.

![Importer les modèles à partir du compte Automation](media/az-modules/import-module.png)

Ce processus d’importation peut aussi être effectué via [PowerShell Gallery](https://www.powershellgallery.com) en recherchant le module à importer. Une fois le module trouvé, sélectionnez-le, choisissez l’onglet **Azure Automation**, puis cliquez sur **Deploy to Azure Automation** (Déployer sur Azure Automation).

![Importation des modules directement à partir de la galerie](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Test de vos runbooks

Une fois que vous avez importé les modules Az dans le compte Automation, vous pouvez commencer à modifier vos runbooks pour qu’ils utilisent les modules. La majorité des applets de commande ont les mêmes noms que pour le module AzureRM, à ceci près que le préfixe AzureRM (ou AzureRm) a été remplacé par Az. Pour obtenir la liste des modules qui ne suivent pas cette convention de nommage, consultez [la liste des exceptions](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Un moyen de tester la modification d’un runbook afin qu’il utilise les nouvelles applets de commande consiste à utiliser `Enable-AzureRmAlias -Scope Process` au début du runbook. En ajoutant cette commande à votre runbook, le script peut s’exécuter sans modification.

## <a name="after-migration-details"></a>Détails après la migration

Une fois la migration terminée, n’essayez pas de démarrer des runbooks utilisant les modules AzureRM sur le compte Automation. De même, il est déconseillé d’importer ou de mettre à jour les modules AzureRM sur ce compte. Envisagez de migrer le compte vers Az et de l’utiliser uniquement avec les modules AZ. 

Quand vous créez un nouveau compte Automation, les modules AzureRM existants sont toujours installés. Vous pouvez toujours mettre à jour les runbooks tutoriels avec les applets de commande AzureRM. Cependant, vous ne devez pas exécuter ces runbooks.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation des modules Az, consultez [Bien démarrer avec le module Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
