---
title: Déplacer votre compte Azure Automation vers un autre abonnement
description: Cet article explique comment déplacer votre compte Automation vers un autre abonnement.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681890"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Déplacer votre compte Azure Automation vers un autre abonnement

Azure Automation vous permet de déplacer certaines ressources vers un nouveau groupe de ressources ou un nouvel abonnement. Vous pouvez déplacer des ressources via le portail Azure, PowerShell, Azure CLI ou l’API REST. Pour en savoir plus sur le processus, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Le compte Azure Automation est l’une des ressources que vous pouvez déplacer. Dans cet article, vous allez apprendre à déplacer des comptes Automation vers une autre ressource ou un autre abonnement. Les étapes générales du déplacement de votre compte Automation sont les suivantes :

1. Supprimez vos solutions.
2. Dissociez votre espace de travail.
3. Déplacez le compte Automation.
4. Supprimez et recréez les comptes d’identification.
5. Réactivez vos solutions.

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Supprimer les solutions

Pour dissocier votre espace de travail de votre compte Automation, vous devez supprimer les solutions suivantes de votre espace de travail :

- Suivi des modifications et inventaire
- Update Management
- Démarrer/arrêter des machines virtuelles pendant les heures creuses

1. Dans le Portail Azure, localisez votre groupe de ressources.
2. Recherchez chaque solution et cliquez sur **Supprimer** dans la page Supprimer des ressources.

    ![Supprimer des solutions du portail Azure](../media/move-account/delete-solutions.png)

    Si vous préférez, vous pouvez supprimer les solutions à l’aide de la cmdlet [Remove-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) :

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Supprimer les règles d’alerte pour la solution Start/Stop VMs during off-hours

Pour la solution Start/Stop VMs during off-hours, vous devez également supprimer les règles d’alerte créées par la solution.

1. Dans le portail Azure, accédez à votre groupe de ressources, puis sélectionnez **Supervision** > **Alertes** > **Gérer les règles d’alerte**.

![Page d’alertes montrant la sélection effectuée pour la gestion des règles d’alertes](../media/move-account/alert-rules.png)

2. Dans la page Règles, vous devez voir une liste des alertes configurées dans ce groupe de ressources. La solution crée les règles suivantes :

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Sélectionnez les règles une à la fois et cliquez sur **Supprimer** pour les supprimer.

    ![Page de règles demandant la confirmation de la suppression des règles sélectionnées](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Si vous ne voyez aucune règle d’alerte dans la page Règles, définissez le champ **État** sur Désactivé pour afficher les alertes désactivées, au cas où vous les auriez désactivées.

4. Une fois les règles d’alerte supprimées, vous devez supprimer le groupe d’actions créé pour les notifications de la solution Start/Stop VMs during off-hours. Dans le portail Azure, sélectionnez **Superviser** > **Alertes** > **Gérer les groupes d’actions**.

5. Sélectionnez **StartStop_VM_Notification**. 

6. Dans la page du groupe d’actions, sélectionnez **Supprimer**.

    ![Page du groupe d’actions](../media/move-account/delete-action-group.png)

    Si vous préférez, vous pouvez supprimer votre groupe d’actions à l’aide de la cmdlet [Remove-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) :

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Dissocier votre espace de travail

Vous pouvez maintenant dissocier votre espace de travail :

1. Dans le portail Azure, sélectionnez **Compte Automation** > **Ressources associées** > **Espace de travail lié**. 

2. Sélectionnez **Dissocier l’espace de travail** pour dissocier l’espace de travail de votre compte Automation.

    ![Dissocier un espace de travail d’un compte Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Déplacer votre compte Automation

Vous pouvez maintenant déplacer votre compte Automation et ses runbooks. 

1. Dans le portail Azure, accédez au groupe de ressources de votre compte Automation. Sélectionnez **Déplacer** > **Déplacer vers un autre abonnement**.

    ![Page de groupe de ressources, déplacer vers un autre abonnement](../media/move-account/move-resources.png)

2. Sélectionnez les ressources de votre groupe de ressources que vous souhaitez déplacer. Veillez à inclure vos ressources du compte Automation, des runbooks et de l’espace de travail Log Analytics.

## <a name="recreate-run-as-accounts"></a>Recréer les comptes d’identification

Les [comptes d’identification](../manage-runas-account.md) permettent de créer un principal de service dans Azure Active Directory pour l’authentification auprès des ressources Azure. Quand vous changez d’abonnement, le compte Automation n’utilise plus le compte d’identification existant. Pour recréer les comptes d’identification :

1. Accédez à votre compte Automation dans le nouvel abonnement, puis sélectionnez **Comptes d’identification** sous **Paramètres de compte**. Comme vous pouvez le constater, les comptes d’identification apparaissent comme étant incomplets.

    ![Les comptes d’identification sont incomplets](../media/move-account/run-as-accounts.png)

2. Supprimez les comptes d’identification un par un à l’aide du bouton **Supprimer** de la page Propriétés. 

    > [!NOTE]
    > Si vous n’avez pas les autorisations nécessaires pour créer ou voir les comptes d’identification, le message suivant s’affiche : `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`Pour en savoir plus sur les autorisations relatives à la configuration d’un compte d’identification, consultez [Autorisations nécessaires à la configuration des comptes d’identification](../manage-runas-account.md#permissions).

3. Une fois que vous avez supprimé les comptes d’identification, sélectionnez **Créer** sous **Compte d’identification Azure**. 

4. Dans la page Ajouter un compte d’identification Azure, sélectionnez **Créer** pour créer le compte d’identification et le principal de service. 

5. Répétez les étapes ci-dessus avec le compte d’identification Azure Classic.

## <a name="enable-solutions"></a>Activer des solutions

Après avoir recréé les comptes d’identification, vous devez réactiver les solutions que vous avez supprimées avant le déplacement : 

1. Pour activer la solution Change Tracking et Inventory sélectionnez Change Tracking et Inventory dans votre compte Automation. Choisissez l’espace de travail Log Analytics que vous avez déplacé, puis sélectionnez **Activer**.

2. Répétez l’étape 1 pour la solution Update Management.

    ![Réactiver les solutions dans votre compte Automation déplacé](../media/move-account/reenable-solutions.png)

3. Les machines intégrées à vos solutions sont visibles une fois que vous vous êtes connecté à l’espace de travail Log Analytics existant. Pour activer la solution Start/Stop VMs during off-hours, vous devez la redéployer. Sous **Ressources liées**, sélectionnez **Start/Stop VMs** > **En savoir plus sur la solution et l’activer** > **Créer** pour démarrer le déploiement.

4. Dans la page Ajouter une solution, choisissez votre espace de travail Log Analytics et votre compte Automation.

    ![Menu Ajouter une solution](../media/move-account/add-solution-vm.png)

5. Configurez la solution comme décrit dans l’article [Solution Start/Stop VMs during off-hours dans Azure Automation](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Vérifier le déplacement

Une fois le déplacement terminé, vérifiez que les capacités répertoriées ci-dessous sont activées. 

|Fonctionnalité|Tests|Dépannage|
|---|---|---|
|Runbooks|Un runbook peut s’exécuter et se connecter correctement aux ressources Azure.|[Résolution des problèmes des runbooks](../troubleshoot/runbooks.md)
|Contrôle de code source|Vous pouvez exécuter une synchronisation manuelle sur votre référentiel de contrôle de code source.|[Intégration du contrôle de code source](../source-control-integration.md)|
|Suivi des modifications et inventaire|Vérifiez que les données d’inventaire actuelles s’affichent sur vos machines.|[Résoudre les problèmes liés au suivi des modifications](../troubleshoot/change-tracking.md)|
|Gestion des mises à jour|Vérifiez que vous voyez vos machines et qu’elles sont saines.</br>Exécutez un test de déploiement de mise à jour de logiciel.|[Résoudre les problèmes liés à Update Management](../troubleshoot/update-management.md)|
|Ressources partagées|Vérifiez que vous voyez toutes vos ressources partagées, telles que les [informations d’identification](../shared-resources/credentials.md), les [variables](../shared-resources/variables.md), etc.|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déplacement des ressources dans Azure, consultez [Déplacer des ressources dans Azure](../../azure-resource-manager/management/move-support-resources.md).
