---
title: Activer Azure Automation Update Management à partir d’une machine virtuelle Azure
description: Cet article explique comment activer Update Management à partir d’une machine virtuelle Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cab114e50852f293a3d1caf5bdc9a341f75f2557
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743960"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Activer Update Management à partir d’une machine virtuelle Azure

Cet article explique comment utiliser une machine virtuelle Azure pour activer la fonctionnalité [Update Management](automation-update-management.md) sur d’autres machines. Pour activer des machines virtuelles Azure à grande échelle, vous devez activer une machine virtuelle existante à l’aide d’Update Management. 

> [!NOTE]
> Lors de l’activation d’Update Management, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de régions pour un compte Automation et un espace de travail Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Compte Automation](automation-offering-get-started.md) pour gérer les machines.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="enable-update-management"></a>Activer Update Management

1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez **Machines virtuelles**, ou recherchez et sélectionnez **Machines virtuelles** sur la page d’accueil.

2. Sélectionnez la machine virtuelle pour laquelle vous souhaitez activer Update Management. Les machines virtuelles peuvent exister dans n’importe quelle région, quel que soit l’emplacement de votre compte Automation. Vous

3. Sur la page de la machine virtuelle, sous **Opérations**, sélectionnez **Update Management**.

4. Vous devez disposer de l’autorisation `Microsoft.OperationalInsights/workspaces/read` pour déterminer si la machine virtuelle est activée pour un espace de travail. Pour en savoir plus sur les autorisations supplémentaires nécessaires, consultez les [autorisations nécessaires pour activer des machines](automation-role-based-access-control.md#feature-setup-permissions). Pour savoir comment activer plusieurs machines en même temps, consultez [Activer Update Management à partir d’un compte Automation](automation-onboard-solutions-from-automation-account.md).

5. Choisissez l’espace de travail Log Analytics et un compte Automation, puis cliquez sur **Activer** pour activer Update Management. Le configuration prend jusqu’à 15 minutes. 

    ![Activer Update Management](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Vérifier la configuration d’étendue

Update Management utilise une configuration d’étendue au sein de l’espace de travail pour cibler les ordinateurs qui activent la fonctionnalité. La configuration d’étendue est un groupe de recherches enregistrées utilisé pour limiter l’étendue de la fonctionnalité à des ordinateurs spécifiques. Pour plus d’informations, consultez [Utiliser des configurations d’étendue pour Update Management](automation-scope-configurations-update-management.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser Update Management pour des machines virtuelles, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
* Pour les configurations d’étendue, consultez [Utiliser des configurations d’étendue pour Update Management](automation-scope-configurations-update-management.md).
* Si vous n’avez plus besoin de l’espace de travail Log Analytics, consultez les instructions fournies dans [Dissocier un espace de travail d’un compte Automation pour Update Management](automation-unlink-workspace-update-management.md).
* Pour supprimer des machines virtuelles d’Update Management, consultez [Supprimer des machines virtuelles d’Update Management](automation-remove-vms-from-update-management.md).
* Pour résoudre les erreurs générales d’Update Management, consultez [Résoudre les problèmes liés à Update Management](troubleshoot/update-management.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Windows, consultez [Résoudre les problèmes de l’agent de mise à jour Windows](troubleshoot/update-agent-issues.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Linux, consultez [Résoudre les problèmes de l’agent de mise à jour Linux](troubleshoot/update-agent-issues-linux.md).
