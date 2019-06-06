---
title: Mettre à jour des modules Azure dans Azure Automation
description: Cet article décrit comment vous pouvez désormais mettre à jour les modules Azure PowerShell courants fournis par défaut dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/30/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c09c9df1b8af7adca5c0169cf31881121ba6cc99
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427486"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Guide de mise à jour des modules Azure PowerShell dans Azure Automation

Pour mettre à jour les modules Azure dans votre compte Automation que vous devez utiliser le [runbook des modules Azure de mettre à jour](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), qui est disponible en open source. Pour commencer à utiliser le runbook **Update-AutomationAzureModulesForAccount** pour mettre à jour vos modules Azure, téléchargez-le à partir du [référentiel de runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) sur GitHub. Vous pouvez ensuite l’importer dans votre compte Automation ou l’exécuter en tant que script. Pour savoir comment importer un runbook dans votre compte Automation, consultez [importer un runbook](manage-runbooks.md#import-a-runbook).

Les modules AzureRM PowerShell courants sont fournis par défaut dans chaque compte Automation. L’équipe Azure met régulièrement à jour les modules Azure, par conséquent, pour tenir à jour vous pouvez utiliser la [AutomationAzureModulesForAccount de mise à jour](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook pour mettre à jour les modules dans vos comptes Automation.

Comme les modules sont régulièrement mis à jour par le groupe de produits, des changements peuvent se produire concernant les applets de commande incluses. Cette action peut impacter négativement vos runbooks selon le type de changement, par exemple, le renommage d’un paramètre ou la dépréciation d’une applet de commande.

Pour éviter toute incidence sur vos runbooks et sur les processus qu’ils automatisent, testez et validez les mises à jour avant de les appliquer. Si vous n’avez pas de compte Automation dédié destiné à cet usage, créez-en un pour pouvoir tester différents scénarios pendant le développement de vos runbooks. Ces tests doivent comprendre des changements itératifs comme la mise à jour de modules PowerShell.

Si vous développez vos scripts localement, il est recommandé d’installer localement les mêmes versions de module que celles que vous avez dans votre compte Automation lors du test pour être assuré d’obtenir les mêmes résultats. Une fois que les résultats sont validés et que vous avez appliqué les changements nécessaires, vous pouvez les passer en production.

> [!NOTE]
> Un nouveau compte Automation peut ne pas contenir les derniers modules.

## <a name="considerations"></a>Considérations

Voici quelques éléments à prendre en compte lors de l’utilisation de ce processus pour mettre à jour vos modules Azure :

* Ce runbook prend en charge la mise à jour uniquement la **Azure** et **AzureRm** modules actuellement. [Les modules Azure PowerShell Az](/powershell/azure/new-azureps-module-az) sont pris en charge dans les comptes Automation, mais ne peut pas être mis à jour avec ce runbook. Il existe des facteurs importants dont vous avez besoin pour prendre en compte lorsque vous utilisez le `Az` modules dans votre compte Automation, pour en savoir plus, consultez [modules Using Az dans votre compte Automation](az-modules.md).

* Évitez de démarrer ce runbook sur les comptes Automation qui contiennent des modules Az.

* Avant de démarrer ce runbook, assurez-vous que votre compte Automation dispose [d’informations d’identification de compte Azure](manage-runas-account.md).

* Vous pouvez utiliser ce code comme un script PowerShell régulière au lieu d’un runbook : se connecter uniquement à Azure à l’aide du [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) commande tout d’abord, puis transmettre `-Login $false` au script.

* Pour utiliser ce runbook sur les clouds souverains, utilisez le paramètre `AzureRmEnvironment` pour transmettre l’environnement approprié au runbook.  Les valeurs acceptables sont **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** et **AzureUSGovernment**. Ces valeurs peuvent être récupérées en utilisant `Get-AzureRmEnvironment | select Name`. Si vous ne transmettez pas de valeur à ce paramètre, le runbook utilise par défaut le cloud public Azure, **AzureCloud**

* Si vous souhaitez utiliser une version spécifique du module Azure PowerShell au lieu de la dernière version disponible sur PowerShell Gallery, transmettez ces versions au paramètre facultatif `ModuleVersionOverrides` du runbook **Update-AutomationAzureModulesForAccount**. Pour obtenir des exemples, consultez le runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Les modules Azure PowerShell qui ne sont pas mentionnés dans le paramètre `ModuleVersionOverrides` sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Si vous ne passez aucune valeur au paramètre `ModuleVersionOverrides`, tous les modules sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Ce comportement est le même que celui du bouton **Mettre à jour les modules Azure**.

## <a name="known-issues"></a>Problèmes connus

Il existe un problème connu avec la mise à jour les modules AzureRM dans un compte Automation qui se trouve dans un groupe de ressources avec un nom numérique qui commence par 0. Pour mettre à jour vos modules Azure dans votre compte Automation, il doit être dans un groupe de ressources qui a un nom d’alphanumériques. Groupes de ressources avec des noms numériques commençant par 0 ne peuvent pas mettre à jour les modules AzureRM pour l’instant.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) open source pour en savoir plus à ce sujet.
