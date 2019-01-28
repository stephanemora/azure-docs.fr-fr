---
title: Résoudre des erreurs en lien avec des ressources partagées d’Azure Automation
description: Découvrez comment résoudre des problèmes en lien avec des ressources partagées d’Azure Automation
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 911f592c43865ea8bdfe85c1ad1071c7112ae9b6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475439"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Résoudre des erreurs en lien avec des ressources partagées

Cet article présente des solutions pour résoudre des problèmes que vous pouvez rencontrer lors de l’utilisation des ressources partagées dans Azure Automation.

## <a name="modules"></a>Modules

### <a name="module-stuck-importing"></a>Scénario : Module dont l’importation est bloquée

#### <a name="issue"></a>Problème

Un module est bloqué dans l’état **Importation** quand vous importez ou mettez à jour vos modules dans Azure Automation.

#### <a name="cause"></a>Cause :

L’importation de modules PowerShell est un processus complexe en plusieurs étapes. Ce processus introduit la possibilité qu’un module ne s’importe pas correctement. Si ce problème se produit, il se peut que le module que vous importez soit bloqué dans un état transitoire. Pour en savoir plus sur ce processus, voir [Importation d’un module PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Résolution :

Pour résoudre ce problème, vous devez supprimer le module bloqué dans l’état **Importation** à l’aide de la cmdlet [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). Vous pouvez ensuite réessayer d’importer le module.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="module-fails-to-import"></a>Scénario : Le module ne parvient pas à terminer l’importation ou il est impossible d’exécuter des cmdlets après l’importation

#### <a name="issue"></a>Problème

Un module ne parvient pas à importer ou réussit l’importation, mais aucune applet de commande n’est extraite.

#### <a name="cause"></a>Cause :

Voici quelques raisons courantes pour lesquelles l’importation d’un module dans Azure Automation peut échouer :

* La structure ne correspond pas à la structure dont Automation a besoin.
* Le module dépend d’un autre module qui n’a pas été déployé sur votre compte Automation.
* Le module n’a pas de dépendances dans le dossier.
* L’applet de commande `New-AzureRmAutomationModule` est utilisée pour charger le module, et vous n’avez pas spécifié le chemin de stockage complet ou vous n’avez pas chargé le module en utilisant une URL accessible publiquement.

#### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :

* Assurez-vous que le module suit le format suivant : NomModule.zip **->** NomModule ou Numéro de version **->** (ModuleName.psm1, ModuleName.psd1)
* Ouvrez le fichier .psd1 et regardez si le module possède des dépendances. Si c’est le cas, téléchargez ces modules dans le compte Automation.
* Assurez-vous que les fichiers .dll référencés sont présents dans le dossier de module.

### <a name="all-modules-suspended"></a>Scénario : L’exécution d’Update-AzureModule.ps1 est suspendue lors de la mise à jour des modules

#### <a name="issue"></a>Problème

Lorsque vous utilisez le runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) pour mettre à jour vos modules Azure, le processus de mise à jour du module est suspendu.

#### <a name="cause"></a>Cause :

Lors de l’utilisation du script `Update-AzureModule.ps1`, le paramètre par défaut déterminant le nombre de modules mis à jour simultanément est de 10. Le processus de mise à jour est sujet aux erreurs lorsque les modules mis à jour en même temps sont en nombre excessif.

#### <a name="resolution"></a>Résolution :

Il n’est pas courant que tous les modules AzureRM soient requis dans le même compte Automation. Il est recommandé de n’importer que les modules AzureRM dont vous avez besoin.

> [!NOTE]
> Éviter d’importer le module **AzureRM**. L’importation des modules **AzureRM** entraîne l’importation de tous les modules **AzureRM.\***, ce qui est déconseillé.

Si le processus de mise à jour est suspendu, vous devez ajouter le paramètre `SimultaneousModuleImportJobCount` au script `Update-AzureModules.ps1` et fournir une valeur inférieure à la valeur par défaut qui est 10. Si vous implémentez cette logique, il est recommandé de commencer avec une valeur de 3 ou 5. `SimultaneousModuleImportJobCount` est un paramètre du runbook système `Update-AutomationAzureModulesForAccount` qui est utilisé pour mettre à jour les modules Azure. Ce changement allonge la durée du processus, mais il a plus de chances d’aboutir. L’exemple suivant montre le paramètre et où le mettre dans le runbook :

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Compte d’identification

### <a name="unable-create-update"></a>Scénario : Vous ne pouvez pas créer ou mettre à jour un compte d’identification

#### <a name="issue"></a>Problème

Quand vous tentez de créer ou de mettre à jour un compte d’identification, vous recevez une erreur similaire au message d’erreur suivant :

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Cause :

Vous ne disposez pas des autorisations nécessaires pour créer ou mettre à jour le compte d’identification, ou la ressource est verrouillée au niveau du groupe de ressources.

#### <a name="resolution"></a>Résolution :

Pour créer ou mettre à jour d’un compte d’identification, vous devez disposer des autorisations appropriées pour les diverses ressources utilisées par le compte d’identification. Pour en savoir plus sur les autorisations nécessaires pour créer ou mettre à jour un compte d’identification, consultez [Autorisations pour les comptes d’identification](../manage-runas-account.md#permissions).

Si le problème est dû à un verrou, vérifiez que le verrou peut être supprimé. Accédez ensuite à la ressource verrouillée, cliquez sur le verrou, puis choisissez **Supprimer** pour supprimer le verrou.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.