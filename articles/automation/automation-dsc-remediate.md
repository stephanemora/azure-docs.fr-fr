---
title: Remédier aux serveurs Azure Automation State Configuration non conformes
description: Comment réappliquer des configurations à la demande pour des serveurs dont l’état de la configuration a dérivé
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: f4ca76f4be9d00e185f8774fc33296d1af1aeece
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585495"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Remédier aux serveurs DSC non conformes

Lorsque des serveurs sont inscrits auprès d’Azure Automation State Configuration, le « mode de configuration » est défini sur ApplyOnly, ApplyandMonitor ou ApplyAndAutoCorrect.
Si le mode n’est pas défini sur Correction automatique, les serveurs qui dérivent d’un état conforme pour une raison quelconque restent non conformes jusqu’à ce qu’ils soient corrigés manuellement.

Azure Compute offre une fonctionnalité nommée Run Command qui permet aux clients d’exécuter des scripts dans des machines virtuelles.
Ce document fournit des exemples de scripts pour cette fonctionnalité lors de la correction manuelle de dérives de configuration.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Correction de la dérive des machines virtuelles Windows à l’aide de PowerShell

Pour obtenir des instructions étape par étape sur l’utilisation de la fonctionnalité Run Command des machines virtuelles Windows, consultez la page de documentation [Exécuter des scripts PowerShell dans vos machines virtuelles Windows en utilisant la fonctionnalité Run Command](/azure/virtual-machines/windows/run-command).

Pour forcer un nœud d’Azure Automation State Configuration à télécharger la configuration la plus récente et à l’appliquer, utilisez le `Update-DscConfiguration` cmdlet.
Pour plus d’informations, consultez la documentation cmdlet sur la[Mise à jour de configuration DSC](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Corrigez la dérive des machines virtuelles Linux

Aucune fonctionnalité similaire n’est actuellement disponibles pour les serveurs Linux.
La seule option est de recommencer le processus d’inscription.
Pour les nœuds Azure, la dérive peut être corrigée à partir du portail ou des cmdlets Azure Automation.
Pour plus d’informations sur ce processus, consultez la page [Intégration des machines pour la gestion avec Azure Automation State Configuration](/azure/automation/automation-dsc-onboarding#onboard-a-vm-using-azure-portal).
Pour les nœuds hybrides, la dérive peut être corrigée à l’aide des scripts Python inclus.
Consultez la documentation dans le [Référentiel PowerShell DSC pour Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Applets de commande d’Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Pour voir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md)
