---
title: Mettre à jour des modules Azure dans Azure Automation
description: Cet article décrit comment vous pouvez désormais mettre à jour les modules Azure PowerShell courants fournis par défaut dans Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fbb57753117f3c60010fe910616b8d0af5178360
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434821"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Guide de mise à jour des modules Azure PowerShell dans Azure Automation

Les modules Azure PowerShell courants sont fournis par défaut dans chaque compte Automation. L’équipe Azure met à jour régulièrement les modules Azure. Vous disposez donc dans le compte Automation d’une méthode permettant de mettre à jour les modules dans le compte lorsque de nouvelles versions sont disponibles dans le portail.

Étant donné que les modules sont régulièrement mis à jour par le groupe de produits, les modifications peuvent se produire avec les cmdlets inclus. Cela peut avoir un impact négatif sur vos runbooks suivant le type de modification, comme renommer un paramètre ou désapprouver entièrement un cmdlet. Pour éviter toute incidence sur vos runbooks ainsi que les processus qu’ils automatisent, il est conseillé de tester et valider les mises à jour avant de les appliquer. Si vous ne possédez pas de compte Automation dédié destiné à cet usage, pensez à en créer un. Vous pourrez tester de nombreux scénarios et différentes permutations lors du développement de vos runbooks, en plus des modifications répétitives comme la mise à jour des modules PowerShell. Dès que les résultats sont validés et une fois les modifications requises appliquées, passez à la coordination de la migration de tout runbook nécessitant une modification, puis réalisez la mise à jour comme décrit dans la production.

> [!NOTE]
> Un nouveau compte Automation peut ne pas contenir les derniers modules.

## <a name="updating-azure-modules"></a>Mise à jour de modules Azure

1. La page Modules de votre compte Automation comprend une option **Mettre à jour les modules Azure**. Elle est toujours activée.<br><br> ![Option Mettre à jour les modules Azure dans la page Modules](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Avant la mise à jour vos modules Azure, il est recommandé de les mettre à jour dans un compte Automation test pour vous assurer que les scripts existants se comportent comme prévu avec la mise à jour de vos modules Azure.
  >
  > Le bouton **Mettre à jour les modules Azure** est uniquement disponible dans le cloud public. Elle n’est pas disponible dans les [régions souveraines](https://azure.microsoft.com/global-infrastructure/). Consultez la section dédiée aux [méthodes alternatives pour mettre à jour vos modules](#alternative-ways-to-update-your-modules) afin d’en savoir plus.


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

    Si les modules sont déjà à jour, le processus se termine en quelques secondes. Vous êtes averti quand le processus de mise à jour est terminé.<br><br> ![Mise à jour d’état de mise à jour des modules Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    Les modules AzureRm .NET Core (AzureRm.*.Core) ne sont pas pris en charge dans Azure Automation et ne peuvent pas être importés.

> [!NOTE]
> Azure Automation utilise les modules les plus récents de votre compte Automation durant l’exécution d’un nouveau travail planifié.  

Si vous utilisez des cmdlets à partir de ces modules Azure PowerShell dans des runbooks, vous devez effectuer cette mise à jour chaque mois pour être certain d’avoir les derniers modules. Azure Automation utilise la connexion AzureRunAsConnection pour l’authentification lors de la mise à jour des modules. Si le principal de service est arrivé à expiration ou n’existe plus au niveau de l’abonnement, la mise à jour du module échoue.

## <a name="alternative-ways-to-update-your-modules"></a>Méthodes alternatives pour mettre à jour vos modules

Comme évoqué, le bouton **Mettre à jour les modules Azure** n’est pas disponible dans les clouds souverains ; il est uniquement disponible dans le cloud Azure global. Cela est dû au fait que la dernière version des modules Azure PowerShell de PowerShell Gallery peut ne pas fonctionner avec les services Resource Manager actuellement déployés dans ces clouds.

La mise à jour des modules peut toujours être effectuée en important le runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) dans votre compte Automation et en l’exécutant.

Utilisez le paramètre `AzureRmEnvironment` pour transmettre l’environnement approprié au runbook.  Les valeurs acceptables sont **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** et **AzureUSGovernmentCloud**. Si vous ne transmettez pas de valeur à ce paramètre, le runbook utilise par défaut le cloud public Azure, soit la valeur **AzureCloud**.

Si vous souhaitez utiliser une version spécifique du module Azure PowerShell au lieu de la dernière version disponible sur PowerShell Gallery, transmettez ces versions au paramètre facultatif `ModuleVersionOverrides` du runbook **Update-AzureModule**. Pour obtenir des exemples, reportez-vous au runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1). Les modules Azure PowerShell qui ne sont pas mentionnés dans le paramètre `ModuleVersionOverrides` sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Si aucun élément n’est transmis au paramètre `ModuleVersionOverrides`, tous les modules sont mis à jour avec les dernières versions du module sur PowerShell Gallery, ce qui correspond au comportement du bouton **Mettre à jour les modules Azure**.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les modules d’intégration et la création de modules personnalisés pour intégrer Automation dans d’autres systèmes, services ou solutions, consultez [Modules d’intégration](automation-integration-modules.md).

* Envisagez d’intégrer un contrôle de code source à l’aide de [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) ou [d’Azure DevOps](automation-scenario-source-control-integration-with-vsts.md), afin de gérer et contrôler votre runbook Automation et votre portefeuille de configuration de manière centralisée.  
