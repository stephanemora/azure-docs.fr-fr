---
title: Résoudre des erreurs en lien avec des ressources partagées d’Azure Automation
description: Découvrez comment résoudre des problèmes en lien avec des ressources partagées d’Azure Automation
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263414"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Résoudre des erreurs en lien avec des ressources partagées

Cet article présente des solutions pour résoudre des problèmes que vous pouvez rencontrer lors de l’utilisation des ressources partagées dans Azure Automation.

## <a name="modules"></a>Modules

### <a name="module-stuck-importing"></a>Scénario : module dont l’importation est bloquée

#### <a name="issue"></a>Problème

Lorsque vous importez ou mettez à jour vos modules dans Azure Automation, vous trouvez un module bloqué dans l’état **Importation**.

#### <a name="error"></a>Erreur

L’importation de modules PowerShell est un processus complexe en plusieurs étapes. Ce processus introduit la possibilité qu’un module ne s’importe pas correctement. Si cela se produit, il se peut que le module que vous importez soit bloqué dans un état transitoire. Pour en savoir plus sur ce processus, voir [Importation d’un module PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Résolution

Pour résoudre ce problème, vous devez supprimer le module bloqué dans l’état **Importation** à l’aide de la cmdlet [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). Vous pouvez ensuite réessayer d’importer le module.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.