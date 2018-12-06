---
title: Mettre à jour des modules Azure dans Azure Automation
description: Cet article décrit comment vous pouvez désormais mettre à jour les modules Azure PowerShell courants fournis par défaut dans Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fc605ab45241280d9331ad7d515ba007a015daa
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583651"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Guide de mise à jour des modules Azure PowerShell dans Azure Automation

Les modules Azure PowerShell courants sont fournis par défaut dans chaque compte Automation. L’équipe Azure met régulièrement à jour les modules Azure. Dans votre compte Automation, vous avez un moyen de mettre à jour les modules quand de nouvelles versions sont disponibles dans le portail.

Comme les modules sont régulièrement mis à jour par le groupe de produits, des changements peuvent se produire concernant les applets de commande incluses. Cette action peut impacter négativement vos runbooks selon le type de changement, par exemple, le renommage d’un paramètre ou la dépréciation d’une applet de commande. Pour éviter toute incidence sur vos runbooks et sur les processus qu’ils automatisent, testez et validez les mises à jour avant de les appliquer. Si vous n’avez pas de compte Automation dédié destiné à cet usage, créez-en un pour pouvoir tester différents scénarios pendant le développement de vos runbooks. Ces tests doivent comprendre des changements itératifs comme la mise à jour de modules PowerShell. Une fois que les résultats sont validés et que vous avez appliqué les changements nécessaires, vous pouvez les passer en production.

> [!NOTE]
> Un nouveau compte Automation peut ne pas contenir les derniers modules.

## <a name="updating-azure-modules"></a>Mise à jour de modules Azure

1. Dans la page Modules de votre compte Automation, vous avez une option appelée **Mettre à jour les modules Azure**. Elle est toujours activée.<br><br> ![Option Mettre à jour les modules Azure dans la page Modules](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Avant de mettre à jour vos modules Azure, nous vous recommandons de le faire dans un compte Automation de test pour vérifier que vos scripts existants fonctionnent comme prévu.
  >
  > Le bouton **Mettre à jour les modules Azure** est uniquement disponible dans le cloud public. Il n’est pas disponible dans les [régions souveraines](https://azure.microsoft.com/global-infrastructure/). Consultez la section dédiée aux [méthodes alternatives pour mettre à jour vos modules](#alternative-ways-to-update-your-modules) afin d’en savoir plus.


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

## <a name="alternative-ways-to-update-your-modules"></a>Méthodes alternatives pour mettre à jour vos modules

Comme indiqué, le bouton **Mettre à jour les modules Azure** n’est pas disponible dans les clouds souverains, seulement dans le cloud Azure global. C’est parce que la dernière version des modules Azure PowerShell de PowerShell Gallery peut ne pas fonctionner avec les ressources Resource Manager actuellement déployées dans ces clouds.

Vous pouvez importer et exécuter le runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) pour tenter de mettre à jour les modules Azure dans votre compte Automation. Ce processus peut échouer si les versions que vous essayez d’importer à partir de la galerie ne sont pas compatibles avec les services Azure actuellement déployés dans l’environnement Azure cible. Vous devez peut-être vérifier que les versions de module compatibles sont spécifiées dans les paramètres de runbook.

Utilisez le paramètre `AzureRmEnvironment` pour transmettre l’environnement approprié au runbook.  Les valeurs acceptables sont **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** et **AzureUSGovernment**. Ces valeurs peuvent être obtenues en utilisant `Get-AzureRmEnvironment | select Name`. Si vous ne transmettez pas de valeur à ce paramètre, le runbook utilise par défaut le cloud public Azure, **AzureCloud**

Si vous souhaitez utiliser une version spécifique du module Azure PowerShell au lieu de la dernière version disponible sur PowerShell Gallery, transmettez ces versions au paramètre facultatif `ModuleVersionOverrides` du runbook **Update-AzureModule**. Pour obtenir des exemples, reportez-vous au runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1). Les modules Azure PowerShell qui ne sont pas mentionnés dans le paramètre `ModuleVersionOverrides` sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Si vous ne passez aucune valeur au paramètre `ModuleVersionOverrides`, tous les modules sont mis à jour avec les dernières versions de module sur PowerShell Gallery. Ce comportement est le même que celui du bouton **Mettre à jour les modules Azure**.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les modules d’intégration et la création de modules personnalisés pour intégrer Automation dans d’autres systèmes, services ou solutions, consultez [Modules d’intégration](automation-integration-modules.md).

