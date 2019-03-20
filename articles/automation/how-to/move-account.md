---
title: Déplacer votre compte Azure Automation vers un autre abonnement
description: Cet article décrit comment déplacer votre compte Automation vers un autre abonnement
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225958"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Déplacer votre compte Azure Automation vers un autre abonnement

Azure vous offre la possibilité de déplacer des ressources vers un nouveau groupe de ressources ou d’un abonnement. Vous pouvez déplacer des ressources via le portail Azure, PowerShell, l’interface CLI ou l’API REST. Pour en savoir plus sur le processus, consultez [déplacer des ressources vers un nouveau groupe de ressources ou d’un abonnement](../../azure-resource-manager/resource-group-move-resources.md). 

Les comptes Azure Automation sont une des ressources qui peuvent être déplacés. Dans cet article, vous allez découvrir les étapes pour déplacer les comptes Automation à une autre ressource ou un abonnement.

Les étapes principales de déplacement de votre compte Automation sont :

1. Supprimer vos solutions.
2. Supprimer le lien de votre espace de travail.
3. Déplacer le compte Automation.
4. Supprimer et recréer les comptes d’identification.
5. Réactiver vos solutions.

## <a name="remove-solutions"></a>Supprimer des solutions

Pour supprimer le lien de votre espace de travail à partir de votre compte Automation, ces solutions doivent être supprimées à partir de votre espace de travail :
- **Suivi des modifications et inventaire**
- **Gestion des mises à jour** 
- **Machines virtuelles de Start/Stop durant les heures creuses** 

Dans votre groupe de ressources, recherchez chaque solution et sélectionnez **supprimer**. Sur le **suppression de ressources** , vérifiez les ressources pour supprimer, puis sélectionnez **supprimer**.

![Supprimer des solutions à partir du portail Azure](../media/move-account/delete-solutions.png)

Vous pouvez accomplir la même tâche avec la [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) comme indiqué dans l’exemple suivant :

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Étapes supplémentaires pour démarrer/arrêter les machines virtuelles

Pour le **Start/Stop VMs** solution, vous devez également supprimer les règles d’alerte créés par la solution.

Dans le portail Azure, accédez à votre groupe de ressources et sélectionnez **surveillance** > **alertes** > **gérer les règles d’alerte**.

![Sélection de montrant page des règles d’alertes de gestion des alertes](../media/move-account/alert-rules.png)

Sur le **règles** page, vous devez voir une liste des alertes configurées dans ce groupe de ressources. Le **Start/Stop VMs** solution crée trois règles d’alerte :

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Sélectionnez ces trois règles d’alerte, puis sélectionnez **supprimer**. Cette action va supprimer ces règles d’alerte.

![Page règles de demande de confirmation de suppression pour les règles sélectionnées](../media/move-account/delete-rules.png)

> [!NOTE]
> Si vous ne voyez aucune règle d’alerte sur le **règles** , changez le **état** pour afficher **désactivé** des alertes, car vous pouvez avoir désactivé les.

Lorsque les règles d’alerte sont supprimées, supprimez le groupe d’actions a été créé pour le **Start/Stop VMs** notifications de la solution.

Dans le portail Azure, sélectionnez **moniteur** > **alertes** > **gérer des groupes d’actions**.

Sélectionnez **StartStop_VM_Notification** dans la liste. Dans la page de groupe d’action, sélectionnez **supprimer**.

![Page de groupe d’action, sélectionnez Supprimer](../media/move-account/delete-action-group.png)

De même, vous pouvez supprimer votre groupe d’actions à l’aide de PowerShell avec le [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) applet de commande, comme illustré dans l’exemple suivant :

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Supprimer le lien de votre espace de travail

Dans le portail Azure, sélectionnez **compte Automation** > **les ressources associées** > **espace de travail lié**. Sélectionnez **dissocier l’espace de travail** à dissocier l’espace de travail à partir de votre compte Automation.

![Dissocier un espace de travail à partir d’un compte Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Déplacer votre compte Automation

Après avoir supprimé les éléments précédemment cités, vous pouvez continuer à supprimer votre compte Automation et ses runbooks. Dans le portail Azure, accédez au groupe de ressources de votre compte Automation. Sélectionnez **déplacer** > **déplacer vers un autre abonnement**.

![Page de groupe de ressources, déplacement vers un autre abonnement](../media/move-account/move-resources.png)

Sélectionnez les ressources dans votre groupe de ressources que vous souhaitez déplacer. Assurez-vous que vous incluez votre **compte Automation**, **Runbook**, et **espace de travail Analytique de journal** ressources.

Une fois le déplacement terminé, il existe des étapes supplémentaires requises pour que tout fonctionne.

## <a name="re-create-run-as-accounts"></a>Recréer les comptes d’identification

[Comptes d’identification](../manage-runas-account.md) créer un principal de service dans Azure Active Directory pour s’authentifier auprès des ressources Azure. Lorsque vous modifiez les abonnements, le compte Automation n’utilise plus le compte d’identification existant.

Accédez à votre compte Automation dans le nouvel abonnement et sélectionnez **comptes d’identification** sous **comptable**. Vous verrez que les comptes d’identification affichent maintenant comme incomplètes.

![Comptes d’identification sont incomplets](../media/move-account/run-as-accounts.png)

Sélectionnez chaque profil d’identification. Sur le **propriétés** page, sélectionnez **supprimer** pour supprimer le compte d’identification.

> [!NOTE]
> Si vous ne disposez pas des autorisations pour créer ou afficher les comptes d’identification, vous verrez le message suivant : `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Pour en savoir plus sur les autorisations requises pour configurer un compte d’identification, consultez [autorisations requises pour configurer des comptes d’identification](../manage-runas-account.md#permissions).

Une fois les comptes d’identification sont supprimées, sélectionnez **créer** sous **Azure exécuter en tant que compte**. Sur le **ajouter Azure exécuter en tant que compte** page, sélectionnez **créer** pour créer le compte d’identification et le service principal. Répétez les étapes précédentes avec le **compte d’identification Azure Classic**.

## <a name="enable-solutions"></a>Activer des solutions

Une fois que vous recréez les comptes d’identification, vous devez réactiver les solutions que vous avez supprimé avant le déplacement. Pour allumer **Change Tracking et Inventory** et **Update Management**, sélectionnez la fonctionnalité correspondante dans votre compte Automation. Cliquez sur l’espace de travail Analytique de journal que vous avez déplacé sur et sélectionnez **activer**.

![Réactiver les solutions dans votre compte Automation déplacé](../media/move-account/reenable-solutions.png)

Les ordinateurs qui sont intégrés avec vos solutions seront visibles lorsque vous vous êtes connecté à l’espace de travail Analytique de journal existante.

Pour activer la **Start/Stop VMs** au cours de la solution d’heures creuses, vous devez redéployer la solution. Sous **les ressources associées**, sélectionnez **Start/Stop VMs** > **en savoir plus sur, activez la solution** > **créer** pour démarrer le déploiement.

Sur le **ajouter une Solution** page, choisissez votre espace de travail Log Analytique et un compte Automation.  

![Ajouter un menu de la Solution](../media/move-account/add-solution-vm.png)

Pour obtenir des instructions détaillées sur la configuration de la solution, consultez [Start/Stop VMs during solution les heures creuses dans Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Déplacer après vérification

Une fois le déplacement terminé, vérifiez la liste suivante de tâches qui doivent être vérifiés :

|Fonctionnalité|Tests|Résolution des problèmes de lien|
|---|---|---|
|Runbooks|Un runbook avec succès pour exécuter et se connecter aux ressources Azure.|[Résolution des problèmes des runbooks](../troubleshoot/runbooks.md)
| Contrôle de code source|Vous pouvez exécuter une synchronisation manuelle sur votre référentiel de contrôle source.|[Intégration du contrôle de code source](../source-control-integration.md)|
|Suivi des modifications et inventaire|Vérifiez que vous voyez des données d’inventaire en cours à partir de vos machines.|[Résoudre les problèmes de suivi des modifications](../troubleshoot/change-tracking.md)|
|Gestion des mises à jour|Vérifiez que vous voyez vos machines et ils sont intègres.</br>Exécuter un déploiement de mises à jour de logiciels de test.|[Résoudre les problèmes de gestion de la mise à jour](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déplacement des ressources dans Azure, consultez [déplacer des ressources dans Azure](../../azure-resource-manager/move-support-resources.md).
