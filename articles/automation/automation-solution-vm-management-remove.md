---
title: Présentation de la suppression de Start/Stop VMs during off-hours d’Azure Automation
description: Cet article décrit comment supprimer la fonctionnalité Start/Stop VMs during off-hours et dissocier un compte Automation de l’espace de travail Log Analytics.
services: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: 9ec76197bfde6bb679f70c44ab01712f9f52bfd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533958"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Supprimer Start/Stop VMs during off-hours d’un compte Automation

Après avoir activé la fonctionnalité Start/Stop VMs during off-hours pour gérer l’état d’exécution de vos machines virtuelles Azure, vous pouvez décider de ne plus l’utiliser. Vous pouvez supprimer cette fonctionnalité à l’aide de l’une des méthodes suivantes, en fonction des modèles de déploiement pris en charge :

* Supprimez le groupe de ressources contenant le compte Automation et l’espace de travail Log Analytics d’Azure Monitor lié, tous deux dédiés à la prise en charge de cette fonctionnalité.
* Dissociez l’espace de travail Log Analytics du compte Automation et supprimez le compte Automation dédié à cette fonctionnalité.
* Supprimez la fonctionnalité d’un compte Automation et d’un espace de travail lié, qui prennent en charge d’autres objectifs de gestion et de surveillance.

La suppression de cette fonctionnalité n’a pour effet que de supprimer les runbooks associés. Elle ne supprime pas les planifications ou les variables créées pendant le déploiement ou personnalisées par la suite.

> [!NOTE]
> Avant de continuer, vérifiez qu’aucun [verrou Resource Manager](../azure-resource-manager/management/lock-resources.md) n’est appliqué au niveau de l’abonnement, du groupe de ressources ou de la ressource, ce qui empêche toute suppression ou modification accidentelle des ressources critiques. Lorsque vous déployez la solution Start/Stop VMs during off-hours, elle définit le niveau de verrouillage sur **CanNotDelete** pour plusieurs ressources dépendantes dans le compte Automation (en particulier ses runbooks et variables). Tous les verrous doivent être supprimés pour que vous puissiez supprimer le compte Automation.

## <a name="delete-the-dedicated-resource-group"></a>Supprimer le groupe de ressources dédié

Pour supprimer le groupe de ressources, procédez de la manière décrite dans l’article [Suppression d’un groupe de ressources et de ressources Azure Resource Manager](../azure-resource-manager/management/delete-resource-group.md).

## <a name="delete-the-automation-account"></a>Supprimer le compte Automation

Pour supprimer votre compte Automation dédié à Start/Stop VMs during off-hours, procédez comme suit.

1. Connectez-vous à Azure via la page [https://portal.azure.com](https://portal.azure.com).

2. Accédez à votre compte Automation, puis, sous **Ressources associées**, sélectionnez **Espace de travail lié**.

3. Sélectionnez **Accéder à l’espace de travail**.

4. Cliquez sur **Solutions** sous **Général**.

5. Sur la page Solutions, sélectionnez **Start-Stop-VM[espace de travail]** .

6. Sur la page **VMManagementSolution[Workspace]** , sélectionnez l’option **Supprimer** dans le menu.

7. Pendant que les informations sont vérifiées et que la fonctionnalité est supprimée, vous pouvez suivre la progression sous **Notifications** à partir du menu. Vous êtes redirigé vers la page Solutions une fois le processus de suppression terminé.

### <a name="unlink-workspace-from-automation-account"></a>Dissocier un espace de travail d’un compte Automation

Il existe deux options pour dissocier l’espace de travail Log Analytics de votre compte Automation. Vous pouvez effectuer ce processus à partir du compte Automation ou de l’espace de travail lié.

Pour dissocier votre compte Automation, procédez comme suit.

1. Dans le portail Azure, sélectionnez **Comptes Automation**.

2. Accédez à votre compte Automation et sélectionnez **Espace de travail lié** sous **Ressources associées** sur la gauche.

3. Dans la page **Dissocier l’espace de travail**, sélectionnez **Dissocier l’espace de travail** et répondez aux invites.

   ![Page Dissocier l’espace de travail](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Vous pouvez suivre la progression de la dissociation de l’espace de travail Log Analytics en sélectionnant **Notifications** dans le menu.

Pour opérer la dissociation de l’espace de travail, procédez comme suit.

1. Dans le portail Azure, sélectionnez **Espaces de travail Log Analytics**.

2. Dans l’espace de travail, sélectionnez **Compte Automation** sous **Ressources connexes**.

3. Sur la page du compte Automation, sélectionnez **Dissocier le compte**, puis répondez aux invites.

Vous pouvez suivre la progression de la dissociation du compte Automation en sélectionnant **Notifications** dans le menu.

### <a name="delete-automation-account"></a>Supprimer un compte Automation

1. Dans le portail Azure, sélectionnez **Comptes Automation**.

2. Ouvrez votre compte Automation, puis sélectionnez **Supprimer** dans le menu.

Pour suivre la progression de la vérification des informations et de la suppression du compte, dans le menu, sélectionnez **Notifications**.

## <a name="delete-the-feature"></a>Supprimer la fonctionnalité

Pour supprimer Start/Stop VMs during off-hours de votre compte Automation, procédez comme suit. Le compte Automation et l’espace de travail Log Analytics ne sont pas supprimés au cours de ce processus. Si vous ne souhaitez pas conserver l’espace de travail Log Analytics, vous devez le supprimer manuellement. Pour plus d’informations sur la suppression de votre espace de travail, consultez [Supprimer et récupérer un espace de travail Azure Log Analytics](../azure-monitor/logs/delete-workspace.md).

1. Accédez à votre compte Automation, puis, sous **Ressources associées**, sélectionnez **Espace de travail lié**.

2. Sélectionnez **Accéder à l’espace de travail**.

3. Cliquez sur **Solutions** sous **Général**.

4. Sur la page Solutions, sélectionnez **Start-Stop-VM[espace de travail]** .

5. Sur la page **VMManagementSolution[Workspace]** , sélectionnez l’option **Supprimer** dans le menu.

    ![Delete VM management feature](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Dans la fenêtre Supprimer la solution, confirmez que vous souhaitez supprimer la fonctionnalité.

7. Pendant que les informations sont vérifiées et que la fonctionnalité est supprimée, vous pouvez suivre la progression sous **Notifications** à partir du menu. Vous êtes redirigé vers la page Solutions une fois le processus de suppression terminé.

8. Si vous ne souhaitez pas conserver les [ressources](automation-solution-vm-management.md#components) créées par la fonctionnalité ou par vous (par exemple, des variables, des planifications, etc.), vous devez les supprimer manuellement du compte.

## <a name="next-steps"></a>Étapes suivantes

Pour réactiver cette fonctionnalité, consultez [Activer Start/Stop VMs during off-hours](automation-solution-vm-management-enable.md).