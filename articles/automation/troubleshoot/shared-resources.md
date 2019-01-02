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
ms.openlocfilehash: ce78c86cdae9a06100fd17d00e0229805e42983b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848457"
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