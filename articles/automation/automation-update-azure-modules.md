---
title: Mettre à jour des modules Azure dans Azure Automation
description: Cet article décrit comment vous pouvez désormais mettre à jour les modules Azure PowerShell courants fournis par défaut dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03174e6336589f8aa49a7fc7197da1301ff54400
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304303"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Guide de mise à jour des modules Azure PowerShell dans Azure Automation

Pour mettre à jour les modules Azure dans votre compte Automation, il est recommandé d’utiliser désormais le [runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), qui est désormais en open source. En outre, vous pouvez toujours utiliser le bouton **Mettre à jour les modules Azure** dans le portail pour mettre à jour vos modules Azure. Pour en savoir plus sur l’utilisation du runbook open source, consultez [Mettre à jour les modules Azure avec le runbook open source](#open-source).

Les modules Azure PowerShell courants sont fournis par défaut dans chaque compte Automation. L’équipe Azure met régulièrement à jour les modules Azure. Dans votre compte Automation, vous avez un moyen de mettre à jour les modules quand de nouvelles versions sont disponibles dans le portail.

Comme les modules sont régulièrement mis à jour par le groupe de produits, des changements peuvent se produire concernant les applets de commande incluses. Cette action peut impacter négativement vos runbooks selon le type de changement, par exemple, le renommage d’un paramètre ou la dépréciation d’une applet de commande.

Pour éviter toute incidence sur vos runbooks et sur les processus qu’ils automatisent, testez et validez les mises à jour avant de les appliquer. Si vous n’avez pas de compte Automation dédié destiné à cet usage, créez-en un pour pouvoir tester différents scénarios pendant le développement de vos runbooks. Ces tests doivent comprendre des changements itératifs comme la mise à jour de modules PowerShell. 

Si vous développez vos scripts localement, il est recommandé d’installer localement les mêmes versions de module que celles que vous avez dans votre compte Automation lors du test pour être assuré d’obtenir les mêmes résultats. Une fois que les résultats sont validés et que vous avez appliqué les changements nécessaires, vous pouvez les passer en production.

> [!NOTE]
> Un nouveau compte Automation peut ne pas contenir les derniers modules.

## <a name="open-source"></a>Mettre à jour les modules Azure avec le runbook open source

Pour commencer à utiliser le runbook **Update-AutomationAzureModulesForAccount** pour mettre à jour vos modules Azure, téléchargez-le à partir du [référentiel de runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) sur GitHub. Vous pouvez ensuite l’importer dans votre compte Automation ou l’exécuter en tant que script. Vous trouverez les instructions pour effectuer cette opération dans le [référentiel de runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

### <a name="considerations"></a>Considérations

Voici quelques éléments à prendre en compte lors de l’utilisation de ce processus pour mettre à jour vos modules Azure :

* Si vous importez ce runbook avec le nom d’origine `Update-AutomationAzureModulesForAccount`, il remplace le runbook interne portant ce nom. Par conséquent, le runbook importé s’exécutera lorsque le bouton **Mettre à jour les modules Azure** est actionné ou lorsque ce runbook est appelé directement par le biais de l’API Azure Resource Manager pour ce compte Automation.

* Ce runbook prend en charge la mise à jour uniquement la **Azure** et **AzureRm** modules actuellement. [Les modules Azure PowerShell Az](/powershell/azure/new-azureps-module-az) sont pris en charge dans les comptes Automation, mais ne peut pas être mis à jour avec ce runbook.

* Évitez de démarrer ce runbook sur les comptes Automation qui contiennent des modules Az.

* Avant de démarrer ce runbook, assurez-vous que votre compte Automation dispose [d’informations d’identification de compte Azure](manage-runas-account.md).

* Vous pouvez utiliser ce code comme un script PowerShell standard au lieu d’un runbook : connectez-vous à Azure à l’aide de la commande [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount), puis transmettez `-Login $false` au script.

* Pour utiliser ce runbook sur les clouds souverains, utilisez le paramètre `AzureRmEnvironment` pour transmettre l’environnement approprié au runbook.  Les valeurs acceptables sont **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** et **AzureUSGovernment**. Ces valeurs peuvent être récupérées en utilisant `Get-AzureRmEnvironment | select Name`. Si vous ne transmettez pas de valeur à ce paramètre, le runbook utilise par défaut le cloud public Azure, **AzureCloud**

* Si vous souhaitez utiliser une version spécifique du module Azure PowerShell au lieu de la dernière version disponible sur PowerShell Gallery, transmettez ces versions au paramètre facultatif `ModuleVersionOverrides` du runbook **Update-AutomationAzureModulesForAccount**. Pour obtenir des exemples, consultez le runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Les modules Azure PowerShell qui ne sont pas mentionnés dans le paramètre `ModuleVersionOverrides` sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Si vous ne passez aucune valeur au paramètre `ModuleVersionOverrides`, tous les modules sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Ce comportement est le même que celui du bouton **Mettre à jour les modules Azure**.

## <a name="update-azure-modules-in-the-azure-portal"></a>Mettre à jour des modules Azure dans le portail Azure

1. Dans la page Modules de votre compte Automation, vous avez une option appelée **Mettre à jour les modules Azure**. Elle est toujours activée.<br><br> ![Option Mettre à jour les modules Azure dans la page Modules](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

   > [!NOTE]
   > Avant de mettre à jour vos modules Azure, nous vous recommandons de le faire dans un compte Automation de test pour vérifier que vos scripts existants fonctionnent comme prévu.
   >
   > Le bouton **Mettre à jour les modules Azure** est uniquement disponible dans le cloud public. Il n’est pas disponible dans les [régions souveraines](https://azure.microsoft.com/global-infrastructure/). Utilisez le runbook  **Update-AutomationAzureModulesForAccount** pour mettre à jour vos modules Azure. Vous pouvez le télécharger à partir du [référentiel de runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update). Pour en savoir plus sur l’utilisation du runbook open source, consultez [Mettre à jour les modules Azure avec le runbook open source](#open-source).

2. Cliquez sur **Mettre à jour les modules Azure**, un message de confirmation s’affiche pour vous demander si vous souhaitez continuer.<br><br> ![Notification de mise à jour des modules Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Cliquez sur **Oui** et le processus de mise à jour du module commence. Le processus de mise à jour prend environ 15 à 20 minutes pour les modules suivants :

   * Microsoft Azure
   * Azure.Storage
   * AzureRm.Automation
   * AzureRm.Compute
   * AzureRm.Profile
   * AzureRm.Resources
   * AzureRm.Sql
   * AzureRm.Storage

     Si les modules sont déjà à jour, le processus se termine en quelques secondes. Vous êtes averti quand le processus de mise à jour est effectué.<br><br> ![Mise à jour d’état de mise à jour des modules Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

     Les modules AzureRm .NET Core (AzureRm.*.Core) ne sont pas pris en charge dans Azure Automation et ne peuvent pas être importés.

> [!NOTE]
> Azure Automation utilise les modules les plus récents de votre compte Automation durant l’exécution d’un nouveau travail planifié.  

Si vous utilisez des cmdlets à partir de ces modules Azure PowerShell dans des runbooks, vous devez effectuer cette mise à jour chaque mois pour être certain d’avoir les derniers modules. Azure Automation utilise la connexion `AzureRunAsConnection` pour l’authentification pendant la mise à jour des modules. Si le principal de service a expiré ou n’existe plus au niveau de l’abonnement, la mise à jour du module échoue.

## <a name="known-issues"></a>Problèmes connus

Il existe un problème connu avec la mise à jour les modules AzureRM dans un compte Automation qui se trouve dans un groupe de ressources avec un nom numérique qui commence par 0. Pour mettre à jour vos modules Azure dans votre compte Automation, il doit être dans un groupe de ressources qui a un nom d’alphanumériques. Groupes de ressources avec des noms numériques commençant par 0 ne peuvent pas mettre à jour les modules AzureRM pour l’instant.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [runbook Mettre à jour les modules Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) open source pour en savoir plus à ce sujet.
