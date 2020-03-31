---
title: Déplacer votre compte Azure Automation vers un autre abonnement
description: Cet article explique comment déplacer votre compte Automation vers un autre abonnement
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969829"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Déplacer votre compte Azure Automation vers un autre abonnement

Azure vous permet de déplacer certaines ressources vers un nouveau groupe de ressources ou un nouvel abonnement. Vous pouvez déplacer des ressources via le portail Azure, PowerShell, Azure CLI ou l’API REST. Pour en savoir plus sur le processus, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Les comptes Azure Automation font partie des ressources que vous pouvez déplacer. Dans cet article, vous allez découvrir les étapes à suivre pour déplacer des comptes Automation vers une autre ressource ou un autre abonnement.

Les étapes générales du déplacement de votre compte Automation sont les suivantes :

1. Supprimez vos solutions.
2. Dissociez votre espace de travail.
3. Déplacez le compte Automation.
4. Supprimez et recréez les comptes d’identification.
5. Réactivez vos solutions.

## <a name="remove-solutions"></a>Supprimer les solutions

Pour dissocier votre espace de travail de votre compte Automation, vous devez supprimer les solutions suivantes de votre espace de travail :
- **Change Tracking and Inventory**
- **Gestion des mises à jour**
- **Start/Stop VMs during off-hours**

Dans votre groupe de ressources, recherchez chaque solution, puis sélectionnez **Supprimer**. Dans la page **Supprimer des ressources**, vérifiez les ressources à supprimer, puis sélectionnez **Supprimer**.

![Supprimer des solutions du portail Azure](../media/move-account/delete-solutions.png)

Vous pouvez effectuer la même tâche avec l’applet de commande [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource), comme indiqué dans l’exemple suivant :

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Étapes supplémentaires pour le démarrage/l’arrêt des machines virtuelles

Pour la solution **Start/Stop VMs**, vous devez également supprimer les règles d’alerte créées par cette solution.

Dans le portail Azure, accédez à votre groupe de ressources, puis sélectionnez **Supervision** > **Alertes** > **Gérer les règles d’alerte**.

![Page d’alertes montrant la sélection effectuée pour la gestion des règles d’alertes](../media/move-account/alert-rules.png)

Dans la page **Règles**, vous devez voir une liste des alertes configurées dans ce groupe de ressources. La solution **Start/Stop VMs** crée trois règles d’alerte :

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Sélectionnez ces trois règles d’alerte, puis sélectionnez **Supprimer**. Cette action permet de supprimer ces règles d’alerte.

![Page de règles demandant la confirmation de la suppression des règles sélectionnées](../media/move-account/delete-rules.png)

> [!NOTE]
> Si vous ne voyez aucune règle d’alerte dans la page **Règles**, changez l’**État** pour afficher les alertes **Désactivées**, au cas où vous les auriez désactivées.

Une fois les règles d’alerte supprimées, supprimez le groupe d’actions créé pour les notifications de la solution **Start/Stop VMs**.

Dans le portail Azure, sélectionnez **Superviser** > **Alertes** > **Gérer les groupes d’actions**.

Dans la liste, sélectionnez **StartStop_VM_Notification**. Dans la page du groupe d’actions, sélectionnez **Supprimer**.

![Page de groupe d’actions, sélectionner supprimer](../media/move-account/delete-action-group.png)

De même, vous pouvez supprimer votre groupe d’actions à l’aide de l’applet de commande PowerShell [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup), comme indiqué dans l’exemple suivant :

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Dissocier votre espace de travail

Dans le portail Azure, sélectionnez **Compte Automation** > **Ressources associées** > **Espace de travail lié**. Sélectionnez **Dissocier l’espace de travail** pour dissocier l’espace de travail de votre compte Automation.

![Dissocier un espace de travail d’un compte Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Déplacer votre compte Automation

Après avoir supprimé les éléments précédents, vous pouvez continuer à supprimer votre compte Automation et ses runbooks. Dans le portail Azure, accédez au groupe de ressources de votre compte Automation. Sélectionnez **Déplacer** > **Déplacer vers un autre abonnement**.

![Page de groupe de ressources, déplacer vers un autre abonnement](../media/move-account/move-resources.png)

Sélectionnez les ressources de votre groupe de ressources que vous souhaitez déplacer. Veillez à inclure vos ressources de **compte Automation**, **Runbook** et **espace de travail Log Analytics**.

Une fois le déplacement effectué, vous devez suivre des étapes supplémentaires pour que tout fonctionne correctement.

## <a name="re-create-run-as-accounts"></a>Recréer les comptes d’identification

Les [comptes d’identification](../manage-runas-account.md) permettent de créer un principal de service dans Azure Active Directory pour l’authentification auprès des ressources Azure. Quand vous changez d’abonnement, le compte Automation n’utilise plus le compte d’identification existant.

Accédez à votre compte Automation dans le nouvel abonnement, puis sélectionnez **Comptes d’identification** sous **Paramètres de compte**. Comme vous pouvez le constater, les comptes d’identification apparaissent comme étant incomplets.

![Les comptes d’identification sont incomplets](../media/move-account/run-as-accounts.png)

Sélectionnez chaque compte d’identification. Dans la page **Propriétés**, sélectionnez **Supprimer** pour supprimer le compte d’identification.

> [!NOTE]
> Si vous n’avez pas les autorisations nécessaires pour créer ou voir les comptes d’identification, le message suivant s’affiche : `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`Pour en savoir plus sur les autorisations relatives à la configuration d’un compte d’identification, consultez [Autorisations nécessaires à la configuration des comptes d’identification](../manage-runas-account.md#permissions).

Une fois les comptes d’identification supprimés, sélectionnez **Créer** sous **Compte d’identification Azure**. Dans la page **Ajouter un compte d’identification Azure**, sélectionnez **Créer** pour créer le compte d’identification et le principal de service. Répétez les étapes précédentes avec le **compte d’identification Azure Classic**.

## <a name="enable-solutions"></a>Activer des solutions

Après avoir recréé les comptes d’identification, vous devez réactiver les solutions que vous avez supprimées avant le déplacement. Pour activer le **suivi des modifications et l’inventaire** ainsi que la **gestion des mises à jour**, sélectionnez la fonctionnalité correspondante dans votre compte Automation. Choisissez l’espace de travail Log Analytics que vous avez déplacé, puis sélectionnez **Activer**.

![Réactiver les solutions dans votre compte Automation déplacé](../media/move-account/reenable-solutions.png)

Les machines intégrées à vos solutions sont visibles une fois que vous vous êtes connecté à l’espace de travail Log Analytics existant.

Pour activer la solution **Start/Stop VMs during off-hours**, vous devez la redéployer. Sous **Ressources liées**, sélectionnez **Start/Stop VMs** > **En savoir plus sur la solution et l’activer** > **Créer** pour démarrer le déploiement.

Dans la page **Ajouter une solution**, choisissez votre espace de travail Log Analytics Workspace et votre compte Automation.

![Menu Ajouter une solution](../media/move-account/add-solution-vm.png)

Pour obtenir des instructions détaillées sur la configuration de la solution, consultez [Solution Start/Stop VMs during off-hours dans Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Vérification postérieure au déplacement

Une fois le déplacement effectué, consultez la liste suivante des tâches à vérifier :

|Fonctionnalité|Tests|Lien de résolution des problèmes|
|---|---|---|
|Runbooks|Un runbook peut s’exécuter et se connecter correctement aux ressources Azure.|[Résolution des problèmes des runbooks](../troubleshoot/runbooks.md)
|Contrôle de code source|Vous pouvez exécuter une synchronisation manuelle sur votre dépôt de contrôle de code source.|[Intégration du contrôle de code source](../source-control-integration.md)|
|Suivi des modifications et inventaire|Vérifiez si vous voyez les données d’inventaire actuelles de vos machines.|[Résoudre les problèmes liés au suivi des modifications](../troubleshoot/change-tracking.md)|
|Gestion des mises à jour|Vérifiez si vous voyez vos machines et si elles sont saines.</br>Exécutez un test de déploiement de mise à jour de logiciel.|[Résoudre les problèmes liés à Update Management](../troubleshoot/update-management.md)|
|Ressources partagées|Vérifiez que vous voyez toutes vos ressources partagées, telles que [Informations d’identification](../shared-resources/credentials.md), [Variables](../shared-resources/variables.md), etc.|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déplacement des ressources dans Azure, consultez [Déplacer des ressources dans Azure](../../azure-resource-manager/management/move-support-resources.md).
