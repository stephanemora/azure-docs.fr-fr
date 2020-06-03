---
title: Activer la fonctionnalité Suivi des modifications et inventaire d’Azure Automation à partir d’un runbook
description: Cet article explique comment activer la fonctionnalité Suivi des modifications et inventaire à partir d’un runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 014442dee1be23a189e22a505abf86050601b2aa
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826740"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Activer Change Tracking et Inventory à partir d’un runbook

Cet article explique comment utiliser un runbook pour activer la fonctionnalité [Suivi des modifications et inventaire](change-tracking.md) pour les machines virtuelles de votre environnement. Pour activer des machines virtuelles Azure à grande échelle, vous devez activer une machine virtuelle existante à l’aide de la fonctionnalité Suivi des modifications et inventaire. 

> [!NOTE]
> Lors de l’activation de la fonctionnalité Suivi des modifications et inventaire, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de régions pour un compte Automation et un espace de travail Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Compte Automation](automation-offering-get-started.md) pour gérer les machines.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-change-tracking-and-inventory"></a>Activer Change Tracking et Inventory 

1. Dans le Portail Azure, sélectionnez **Comptes Automation**, puis votre compte Automation dans la liste.
1. Sélectionnez **Inventaire** sous **Gestion de la configuration**.
1. Sélectionnez un espace de travail Log Analytics existant ou créez-en un. 
1. Cliquez sur **Activer**.

    ![Activer Change Tracking et Inventory](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Sélectionner la machine virtuelle Azure à gérer

Avec la fonctionnalité Suivi des modifications et inventaire, vous pouvez ajouter une machine virtuelle Azure pour la gérer via cette fonction.

1. À partir de votre compte Automation, sélectionnez **Suivi des modifications** ou **Inventaire** sous **Gestion de la configuration**.

2. Cliquez sur **Ajouter des machines virtuelles Azure** pour ajouter votre machine virtuelle.

3. Sélectionnez votre machine virtuelle dans la liste, puis cliquez sur **Activer**. Cette action active la fonction Suivi des modifications et inventaire pour la machine virtuelle.

   ![Activer la fonction Suivi des modifications et inventaire pour une machine virtuelle](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Si vous essayez d’activer une autre fonctionnalité avant la fin de la configuration de Suivi des modifications et inventaire, ce message s’affiche : `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Installer et mettre à jour les modules

Vous devez effectuer la mise à jour vers les derniers modules Azure et importer le module [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) afin d’activer la fonction Suivi des modifications et inventaire pour votre machine virtuelle.

1. Dans votre compte Automation, sélectionnez **Modules** sous **Ressources partagées**. 
2. Sélectionnez **Mettre à jour les modules Azure** pour mettre à jour les modules Azure vers la dernière version. 
3. Cliquez sur **Oui** pour mettre à jour tous les modules Azure existants vers la dernière version.

    ![Mettre à jour les modules](media/automation-enable-changes-from-runbook/update-modules.png)

4. Revenez à **Modules** sous **Ressources partagées**. 
5. Sélectionnez **Parcourir la galerie** pour ouvrir la galerie des modules. 
6. Recherchez Az.OperationalInsights et importez ce module dans le compte Automation.

    ![Importer le module OperationalInsights](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importer un runbook pour activer la fonctionnalité Suivi des modifications et inventaire

1. Dans votre compte Automation, sélectionnez **Runbooks** sous **Automatisation des processus**.
2. Sélectionnez **Parcourir la galerie**.
3. Recherchez `update and change tracking`.
4. Sélectionnez le runbook, puis cliquez sur **Importer** dans la page Afficher la source. 
5. Cliquez sur **OK** pour importer le runbook dans le compte Automation.

   ![Importer un runbook pour la configuration](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. Dans la page Runbook, cliquez sur **Modifier**, puis sélectionnez **Publier**. 
7. Dans le volet Publier un runbook, cliquez sur **Oui** pour le publier.

## <a name="start-the-runbook"></a>Démarrer le runbook

Pour démarrer ce runbook, vous devez avoir activé Suivi des modifications et inventaire pour une machine virtuelle. Une machine virtuelle et un groupe de ressources existants avec la fonctionnalité activée sont nécessaires pour les paramètres.

1. Ouvrez le runbook **Enable-MultipleSolution**.

   ![Runbooks avec plusieurs solutions](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Cliquez sur le bouton Démarrer et entrez les valeurs des paramètres dans les champs suivants :

   * **VMNAME** : nom d’une machine virtuelle existante à ajouter à la fonctionnalité Suivi des modifications et inventaire. Laissez ce champ vide pour ajouter toutes les machines virtuelles du groupe de ressources.
   * **VMRESOURCEGROUP** : nom du groupe de ressources des machines virtuelles à activer.
   * **SUBSCRIPTIONID** : ID d’abonnement de la nouvelle machine virtuelle à activer. Laissez ce champ vide pour utiliser l’abonnement de l’espace de travail. Quand vous utilisez un ID d’abonnement différent, ajoutez le compte d’identification de votre compte Automation en tant que contributeur pour l’abonnement.
   * **ALREADYONBOARDEDVM** : nom de la machine virtuelle qui est déjà activée manuellement pour les modifications.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** : Nom du groupe de ressources auquel appartient la machine virtuelle.
   * **SOLUTIONTYPE** : entrez **ChangeTracking**.

   ![Paramètres du runbook Enable-MultipleSolution](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. Sélectionnez **OK** pour démarrer le travail du runbook.
1. Surveillez la progression et la présence d’erreurs depuis la page de travail du runbook.

## <a name="next-steps"></a>Étapes suivantes

* Pour planifier un runbook, voir [Gérer les planifications dans Azure Automation](shared-resources/schedules.md).
* Pour plus d’informations sur l’utilisation de la fonctionnalité, voir [Gérer Suivi des modifications et inventaire](change-tracking-file-contents.md).
* Pour plus d’informations sur les configurations d’étendue, voir [Utiliser des configurations d’étendue pour la fonctionnalité Suivi des modifications et inventaire](automation-scope-configurations-change-tracking.md).
* Pour savoir comment utiliser la fonctionnalité afin d’identifier les logiciels installés dans votre environnement, voir [Détecter les logiciels installés sur vos machines virtuelles](automation-tutorial-installed-software.md).
* Si vous ne souhaitez pas intégrer votre compte Automation avec un espace de travail Log Analytics lors de l’activation de cette fonctionnalité, voir [Dissocier un espace de travail d’un compte Automation](automation-unlink-workspace-change-tracking.md).
* Lorsque vous avez fini de déployer des modifications sur des machines virtuelles, vous pouvez les supprimer comme décrit dans [Supprimer des machines virtuelles de Suivi des modifications et inventaire](automation-remove-vms-from-change-tracking.md).
* Pour résoudre des problèmes généraux liés à la fonctionnalité, voir [Résoudre les problèmes rencontrés avec Suivi des modifications et inventaire](troubleshoot/change-tracking.md).