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
ms.openlocfilehash: cc5884e1f70bdccee4e7a113e6e3ee2d6604b50a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406082"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Remédier aux serveurs DSC non conformes

Quand des serveurs sont inscrits auprès d’Azure Automation State Configuration, le mode de configuration est défini sur `ApplyOnly`, `ApplyandMonitor` ou `ApplyAndAutoCorrect`. Si le mode n’est pas défini sur `ApplyAndAutoCorrect`, les serveurs qui quittent un état conforme pour une raison quelconque restent non conformes jusqu’à ce qu’ils soient corrigés manuellement.

Azure Compute offre une fonctionnalité nommée Run Command qui permet aux clients d’exécuter des scripts dans des machines virtuelles.
Ce document fournit des exemples de scripts pour cette fonctionnalité lors de la correction manuelle de dérives de configuration.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Correction de la dérive des machines virtuelles Windows à l’aide de PowerShell

Pour obtenir des instructions étape par étape sur l’utilisation de la fonctionnalité Exécuter une commande sur les machines virtuelles Windows, consultez la page de documentation [Exécuter des scripts PowerShell dans votre machine virtuelle Windows avec Exécuter une commande](/azure/virtual-machines/windows/run-command).

Pour forcer un nœud Azure Automation State Configuration à télécharger la configuration la plus récente et à l’appliquer, utilisez l’applet de commande [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Corrigez la dérive des machines virtuelles Linux

Aucune fonctionnalité similaire n’est actuellement disponibles pour les serveurs Linux.
La seule option est de recommencer le processus d’inscription.

Pour les nœuds Azure, vous pouvez corriger la dérive depuis le portail Azure ou avec des applets de commande du module Az. Pour plus d’informations sur ce processus, consultez [Intégration des machines pour la gestion par Azure Automation State Configuration](automation-dsc-onboarding.md#onboard-vms-by-using-the-azure-portal).
Pour les nœuds hybrides, vous pouvez corriger la dérive en utilisant les scripts Python inclus.
Consultez [Dépôt Configuration d’état souhaité PowerShell pour Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Pour voir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md).