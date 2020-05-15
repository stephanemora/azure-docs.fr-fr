---
title: Mettre à jour des modules Azure PowerShell dans Azure Automation
description: Cet article décrit comment vous pouvez désormais mettre à jour les modules Azure PowerShell courants fournis par défaut dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3f2ceda6e87ce16e910f3d215c1fabe81d522f32
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855545"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Mettre à jour des modules Azure PowerShell dans Azure Automation

Pour mettre à jour les modules Azure dans votre compte Automation, vous devez utiliser le [runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), qui est désormais open source. Pour commencer à utiliser le runbook **Update-AutomationAzureModulesForAccount** pour mettre à jour vos modules Azure, téléchargez-le à partir du [référentiel de runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) sur GitHub. Vous pouvez ensuite l’importer dans votre compte Automation ou l’exécuter en tant que script. Pour savoir comment importer un runbook dans votre compte Automation, consultez [Importer un runbook](manage-runbooks.md#import-a-runbook).

Les modules PowerShell les plus courants sont fournis par défaut dans chaque compte Automation. L’équipe Azure met régulièrement à jour les modules Azure. Par conséquent, pour maintenir à jour les modules de vos comptes Automation, vous devez utiliser le runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

Comme les modules sont régulièrement mis à jour par le groupe de produits, des changements peuvent se produire concernant les applets de commande incluses. Ces modifications, par exemple, en renommant un paramètre ou en déconseillant entièrement une cmdlet, peuvent avoir un impact négatif sur vos runbooks.

Pour éviter toute incidence sur vos runbooks et sur les processus qu’ils automatisent, testez et validez les mises à jour avant de les appliquer. Si vous n’avez pas de compte Automation dédié destiné à cet usage, créez-en un pour pouvoir tester différents scénarios pendant le développement de vos runbooks. Ces tests doivent comprendre des changements itératifs, comme la mise à jour des modules PowerShell.

Si vous développez vos scripts localement, il est recommandé d’installer localement les mêmes versions de module que celles de votre compte Automation lors du test pour être assuré d’obtenir les mêmes résultats. Une fois que les résultats sont validés et que vous avez appliqué les changements nécessaires, vous pouvez les passer en production.

> [!NOTE]
> Un nouveau compte Automation peut ne pas contenir les derniers modules.

> [!NOTE]
> Vous ne serez pas en mesure de supprimer des modules globaux, qui sont des modules prêts à l’emploi fournis par Automation.

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Considérations

Voici quelques éléments à prendre en compte lors de l’utilisation de cet article pour mettre à jour vos modules Azure :

* Le runbook décrit dans cet article prend en charge la mise à jour des modules Azure, AzureRM et Az par défaut. Consultez le [fichier Lisez-moi du runbook Mettre à jour les modules Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) pour plus d’informations sur la mise à jour des modules Az.Automation avec ce runbook. Il existe d’autres facteurs importants que vous devez prendre en compte lorsque vous utilisez les modules Az dans votre compte Automation. Pour en savoir plus, consultez [Gérer les modules dans Azure Automation](shared-resources/modules.md).

* Avant de démarrer ce runbook, assurez-vous que votre compte Automation dispose [d’informations d’identification de compte Azure](manage-runas-account.md).

* Vous pouvez utiliser ce code comme un script PowerShell standard au lieu d’un runbook : connectez-vous à Azure à l’aide de la cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0), puis transmettez `-Login $false` au script.

* Pour utiliser ce runbook sur les clouds souverains, utilisez le paramètre `AzEnvironment` pour transmettre l’environnement approprié au runbook.  Les valeurs acceptables sont AzureCloud (cloud public Azure), AzureChinaCloud, AzureGermanCloud et AzureUSGovernment. Ces valeurs peuvent être récupérées en utilisant `Get-AzEnvironment | select Name`. Si vous ne transmettez pas de valeur à cette cmdlet, la valeur par défaut du runbook est AzureCloud.

* Si vous souhaitez utiliser une version spécifique du module Azure PowerShell au lieu du module le plus récent disponible sur PowerShell Gallery, transmettez ces versions au paramètre facultatif `ModuleVersionOverrides` du runbook **Update-AutomationAzureModulesForAccount**. Pour obtenir des exemples, consultez le runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Les modules Azure PowerShell qui ne sont pas mentionnés dans le paramètre `ModuleVersionOverrides` sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Si vous ne passez aucune valeur au paramètre `ModuleVersionOverrides`, tous les modules sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Ce comportement est le même que celui du bouton **Mettre à jour les modules Azure**.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) open source pour en savoir plus à ce sujet.
