---
title: Supprimer votre compte Azure Automation
description: Cet article explique comment supprimer votre compte Automation dans les différents scénarios de configuration.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe2d99a610be3877b4a347e4bd0dd17df53ba326
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834153"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Guide pratique pour supprimer votre compte Azure Automation

Après avoir activé un compte Azure Automation pour aider à automatiser les processus informatiques ou métier, ou avoir activé ses autres fonctionnalités afin de prendre en charge la gestion des opérations de vos machines Azure et non-Azure telles qu’Update Management, vous pouvez décider d’arrêter d’utiliser le compte Automation. Si vous avez activé des fonctionnalités qui dépendent de l’intégration à un espace de travail Azure Monitor Log Analytics, vous devez effectuer d’autres étapes pour effectuer cette action.

La suppression de votre compte Azure Automation peut s’effectuer à l’aide de l’une des méthodes suivantes, en fonction des modèles de déploiement pris en charge :

* Supprimer le groupe de ressources contenant le compte Automation
* Supprimer le groupe de ressources contenant le compte Automation et l’espace de travail Azure Monitor Log Analytics lié si :

    * Le compte et l’espace de travail sont dédiés à la prise en charge d’Update Management, de Suivi des modifications et inventaire, et/ou de Start/Stop VMs during off-hours.
    * Le compte est dédié au traitement de l’automatisation et intégré à un espace de travail pour l’envoi des flux de tâches et de l’état des tâches de runbook.

* Dissocier l’espace de travail Log Analytics du compte Automation et supprimer le compte Automation
* Supprimer la fonctionnalité de votre espace de travail lié, dissocier le compte de l’espace de travail, puis supprimer le compte Automation

Cet article explique comment supprimer entièrement votre compte Automation par le biais du portail Azure, à l’aide d’Azure PowerShell, d’Azure CLI ou de l’API REST.

> [!NOTE]
> Avant de continuer, vérifiez qu’aucun [verrou Resource Manager](../azure-resource-manager/management/lock-resources.md) n’est appliqué au niveau de l’abonnement, du groupe de ressources ou de la ressource, ce qui empêche toute suppression ou modification accidentelle des ressources critiques. Si vous avez déployé la solution Start/Stop VMs during off-hours, elle définit le niveau de verrouillage sur **CanNotDelete** pour plusieurs ressources dépendantes dans le compte Automation (en particulier ses runbooks et variables). Tous les verrous doivent être supprimés pour que vous puissiez supprimer le compte Automation.

## <a name="delete-the-dedicated-resource-group"></a>Supprimer le groupe de ressources dédié

Pour supprimer votre compte Automation et l’espace de travail Log Analytics s’il est lié au compte, créé dans le même groupe de ressources dédié au compte, suivez les étapes décrites dans l’article [Suppression d’un groupe de ressources et de ressources Azure Resource Manager](../azure-resource-manager/management/delete-resource-group.md).

## <a name="delete-a-standalone-automation-account"></a>Supprimer un compte Automation autonome

Si votre compte Automation n’est pas lié à un espace de travail Log Analytics, effectuez les étapes suivantes pour le supprimer.

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Connectez-vous à Azure via la page [https://portal.azure.com](https://portal.azure.com).

2. Dans le portail Azure, accédez à **Comptes Automation**.

3. Ouvrez votre compte Automation, puis sélectionnez **Supprimer** dans le menu.

Pour suivre la progression de la vérification des informations et de la suppression du compte, dans le menu, sélectionnez **Notifications**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cette commande supprime le compte Automation sans afficher d’invite de validation.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Supprimer un compte Automation autonome lié à un espace de travail

Si votre compte Automation est lié à un espace de travail Log Analytics pour collecter des flux de tâches et des journaux de tâches, effectuez les étapes suivantes pour le supprimer.

Il existe deux options pour dissocier l’espace de travail Log Analytics de votre compte Automation. Vous pouvez effectuer ce processus à partir du compte Automation ou de l’espace de travail lié.

Pour dissocier votre compte Automation, procédez comme suit.

1. Dans le portail Azure, accédez à **Comptes Automation**.

2. Accédez à votre compte Automation et sélectionnez **Espace de travail lié** sous **Ressources associées** sur la gauche.

3. Dans la page **Dissocier l’espace de travail**, sélectionnez **Dissocier l’espace de travail** et répondez aux invites.

   ![Page Dissocier l’espace de travail](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Vous pouvez suivre la progression de la dissociation de l’espace de travail Log Analytics en sélectionnant **Notifications** dans le menu.

Pour opérer la dissociation de l’espace de travail, procédez comme suit.

1. Dans le portail Azure, accédez à **Espaces de travail Log Analytics**.

2. Dans l’espace de travail, sélectionnez **Compte Automation** sous **Ressources connexes**.

3. Dans la page du compte Automation, sélectionnez **Dissocier le compte**, puis répondez aux invites.

Vous pouvez suivre la progression de la dissociation du compte Automation en sélectionnant **Notifications** dans le menu.

Une fois que le compte Automation a été correctement supprimé de l’espace de travail, effectuez les étapes de la section [Compte Automation autonome](#delete-a-standalone-automation-account) pour supprimer le compte.

## <a name="delete-a-shared-capability-automation-account"></a>Supprimer un compte Automation à capacité partagée

Pour supprimer votre compte Automation lié à un espace de travail Log Analytics pour la prise en charge d’Update Management, Suivi des modifications et inventaire, et/ou Start/Stop VMs during off-hours, effectuez les étapes suivantes.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Étape 1. Supprimer la solution de l’espace de travail lié

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Connectez-vous à Azure via la page [https://portal.azure.com](https://portal.azure.com).

2. Accédez à votre compte Automation, puis, sous **Ressources associées**, sélectionnez **Espace de travail lié**.

3. Sélectionnez **Accéder à l’espace de travail**.

4. Cliquez sur **Solutions** sous **Général**.

5. Dans la page Solutions, sélectionnez l’un des éléments suivants en fonction des fonctionnalités déployées dans le compte :

    * Pour Start/Stop VMs during off-hours, sélectionnez **Start-Stop-VM [nom de l’espace de travail]** .
    * Pour Update Management, sélectionnez **Updates(nom de l’espace de travail)** .
    * Pour Suivi des modifications et inventaire, sélectionnez **ChangeTracking(nom de l’espace de travail)** .

6. Dans la page **Solution**, sélectionnez **Supprimer** dans le menu. Si plusieurs des fonctionnalités listées ci-dessus sont déployées dans le compte Automation et l’espace de travail lié, vous devez sélectionner et supprimer chacune d’elles avant de continuer.

7. Pendant que les informations sont vérifiées et que la fonctionnalité est supprimée, vous pouvez suivre la progression sous **Notifications** à partir du menu. Vous êtes redirigé vers la page Solutions une fois le processus de suppression terminé.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour supprimer une solution installée à l’aide d’Azure PowerShell, utilisez l’applet de commande [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution).

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Étape 2. Dissocier un espace de travail d’un compte Automation

Il existe deux options pour dissocier l’espace de travail Log Analytics de votre compte Automation. Vous pouvez effectuer ce processus à partir du compte Automation ou de l’espace de travail lié.

Pour dissocier votre compte Automation, procédez comme suit.

1. Dans le portail Azure, accédez à **Comptes Automation**.

2. Accédez à votre compte Automation et sélectionnez **Espace de travail lié** sous **Ressources associées** sur la gauche.

3. Dans la page **Dissocier l’espace de travail**, sélectionnez **Dissocier l’espace de travail** et répondez aux invites.

   ![Page Dissocier l’espace de travail](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Vous pouvez suivre la progression de la dissociation de l’espace de travail Log Analytics en sélectionnant **Notifications** dans le menu.

Pour opérer la dissociation de l’espace de travail, procédez comme suit.

1. Dans le portail Azure, accédez à **Espaces de travail Log Analytics**.

2. Dans l’espace de travail, sélectionnez **Compte Automation** sous **Ressources connexes**.

3. Dans la page du compte Automation, sélectionnez **Dissocier le compte**, puis répondez aux invites.

Vous pouvez suivre la progression de la dissociation du compte Automation en sélectionnant **Notifications** dans le menu.

### <a name="step-3-delete-automation-account"></a>Étape 3. Supprimer un compte Automation

Une fois que le compte Automation a été correctement supprimé de l’espace de travail, effectuez les étapes de la section [Compte Automation autonome](#delete-a-standalone-automation-account) pour supprimer le compte.

## <a name="next-steps"></a>Étapes suivantes

Pour créer un compte Automation à partir du portail Azure, consultez [Créer un compte Azure Automation autonome](automation-create-standalone-account.md). Si vous préférez créer votre compte à l’aide d’un modèle, consultez [Créer un compte Automation à l’aide d’un modèle Azure Resource Manager](quickstart-create-automation-account-template.md).
