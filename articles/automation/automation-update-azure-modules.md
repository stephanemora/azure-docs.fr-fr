---
title: Mettre à jour des modules Azure dans Azure Automation
description: Cet article décrit comment vous pouvez désormais mettre à jour les modules Azure PowerShell courants fournis par défaut dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 76514e620f044b78b992db2b88733e69dbabf135
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850633"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Guide de mise à jour des modules Azure PowerShell dans Azure Automation

Pour mettre à jour les modules Azure dans votre compte Automation, vous devez utiliser le [runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), qui est désormais open source. Pour commencer à utiliser le runbook **Update-AutomationAzureModulesForAccount** pour mettre à jour vos modules Azure, téléchargez-le à partir du [référentiel de runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) sur GitHub. Vous pouvez ensuite l’importer dans votre compte Automation ou l’exécuter en tant que script. Pour savoir comment importer un runbook dans votre compte Automation, consultez [Importer un runbook](manage-runbooks.md#import-a-runbook).

Les modules AzureRM PowerShell courants sont fournis par défaut dans chaque compte Automation. L’équipe Azure met régulièrement à jour les modules Azure. Pour vous tenir à jour vous pouvez utiliser le runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) pour mettre à jour les modules dans vos comptes Automation.

Comme les modules sont régulièrement mis à jour par le groupe de produits, des changements peuvent se produire concernant les applets de commande incluses. Cette action peut impacter négativement vos runbooks selon le type de changement, par exemple, le renommage d’un paramètre ou la dépréciation d’une applet de commande.

Pour éviter toute incidence sur vos runbooks et sur les processus qu’ils automatisent, testez et validez les mises à jour avant de les appliquer. Si vous n’avez pas de compte Automation dédié destiné à cet usage, créez-en un pour pouvoir tester différents scénarios pendant le développement de vos runbooks. Ces tests doivent comprendre des changements itératifs comme la mise à jour de modules PowerShell.

Si vous développez vos scripts localement, il est recommandé d’installer localement les mêmes versions de module que celles que vous avez dans votre compte Automation lors du test pour être assuré d’obtenir les mêmes résultats. Une fois que les résultats sont validés et que vous avez appliqué les changements nécessaires, vous pouvez les passer en production.

> [!NOTE]
> Un nouveau compte Automation peut ne pas contenir les derniers modules.

> [!NOTE]
> Vous ne serez pas en mesure de supprimer des modules globaux (modules prêts à l’emploi fournis par Automation).

## <a name="considerations"></a>Considérations

Voici quelques éléments à prendre en compte lors de l’utilisation de ce processus pour mettre à jour vos modules Azure :

* Ce runbook prend en charge la mise à jour des modules **Azure** et **AzureRm** par défaut. Ce runbook prend aussi en charge la mise à jour des modules **Az**. Consultez le [Fichier Lisez-moi de mise à jour du runbook des modules Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) pour plus d’informations sur la mise à jour des modules `Az` avec ce runbook. Il existe d’autres facteurs importants que vous devez prendre en compte lorsque vous utilisez les modules `Az` dans votre compte Automation ; pour en savoir plus, consultez [Utilisation de modules Az dans votre compte Automation](az-modules.md).

* Avant de démarrer ce runbook, assurez-vous que votre compte Automation dispose [d’informations d’identification de compte Azure](manage-runas-account.md).

* Vous pouvez utiliser ce code comme un script PowerShell standard au lieu d’un runbook : connectez-vous à Azure à l’aide de la commande [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount), puis transmettez `-Login $false` au script.

* Pour utiliser ce runbook sur les clouds souverains, utilisez le paramètre `AzureRmEnvironment` pour transmettre l’environnement approprié au runbook.  Les valeurs acceptables sont **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** et **AzureUSGovernment**. Ces valeurs peuvent être récupérées en utilisant `Get-AzureRmEnvironment | select Name`. Si vous ne transmettez pas de valeur à ce paramètre, le runbook utilise par défaut le cloud public Azure, **AzureCloud**

* Si vous souhaitez utiliser une version spécifique du module Azure PowerShell au lieu de la dernière version disponible sur PowerShell Gallery, transmettez ces versions au paramètre facultatif `ModuleVersionOverrides` du runbook **Update-AutomationAzureModulesForAccount**. Pour obtenir des exemples, consultez le runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Les modules Azure PowerShell qui ne sont pas mentionnés dans le paramètre `ModuleVersionOverrides` sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Si vous ne passez aucune valeur au paramètre `ModuleVersionOverrides`, tous les modules sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Ce comportement est le même que celui du bouton **Mettre à jour les modules Azure**.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) open source pour en savoir plus à ce sujet.
