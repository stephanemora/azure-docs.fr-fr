---
title: Activer Azure Automation Update Management à partir d’un runbook
description: Cet article explique comment activer Update Management à partir d’un runbook.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: a5b6fe5cf83eaa18b34a00767e14e75737aa055e
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449555"
---
# <a name="enable-update-management-from-a-runbook"></a>Activer Update Management à partir d’un runbook

Cet article explique comment utiliser un runbook pour activer la fonctionnalité [Update Management](update-mgmt-overview.md) pour les machines virtuelles de votre environnement. Pour activer des machines virtuelles Azure à grande échelle, vous devez activer une machine virtuelle existante en utilisant Update Management. 

> [!NOTE]
> Lors de l’activation d’Update Management, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de régions pour un compte Automation et un espace de travail Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Compte Automation](../index.yml) pour gérer les machines.
* Une [machine virtuelle](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="enable-update-management"></a>Activer Update Management

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Sélectionnez l’espace de travail Log Analytics, puis cliquez sur **Activer**. Pendant l’activation d’Update Management, une bannière bleue s’affiche.

    ![Activer Update Management](media/update-mgmt-enable-runbook/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Sélectionner la machine virtuelle Azure à gérer

Quand Update Management est activé, vous pouvez ajouter une machine virtuelle Azure pour recevoir des mises à jour.

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Sélectionnez **Ajouter des machines virtuelles Azure** pour ajouter votre machine virtuelle.

3. Choisissez la machine virtuelle dans la liste et cliquez sur **Activer** afin de configurer la machine virtuelle pour les mises à jour.

   ![Activer Update Management pour la machine virtuelle](media/update-mgmt-enable-runbook/enable-update.png)

    > [!NOTE]
    > Si vous essayez d’activer une autre fonctionnalité avant la fin de l’installation d’Update Management, ce message s’affiche : `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Installer et mettre à jour les modules

Vous devez effectuer la mise à jour vers les derniers modules Azure et importer le module [Az.OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) afin que l’activation d’Update Management pour vos machines virtuelles réussisse.

1. Dans votre compte Automation, sélectionnez **Modules** sous **Ressources partagées**.
2. Sélectionnez **Mettre à jour les modules Azure** pour mettre à jour les modules Azure vers la dernière version.
3. Cliquez sur **Oui** pour mettre à jour tous les modules Azure existants vers la dernière version.

    ![Mettre à jour les modules](media/update-mgmt-enable-runbook/update-modules.png)

4. Revenez à **Modules** sous **Ressources partagées**.
5. Sélectionnez **Parcourir la galerie** pour ouvrir la galerie des modules.
6. Recherchez `Az.OperationalInsights` et importez ce module dans votre compte Automation.

    ![Importer le module OperationalInsights](media/update-mgmt-enable-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Importer un runbook pour activer Update Management

1. Dans votre compte Automation, sélectionnez **Runbooks** sous **Automatisation des processus**.
2. Sélectionnez **Parcourir la galerie**.
3. Recherchez `update and change tracking`.
4. Sélectionnez le runbook, puis cliquez sur **Importer** dans la page Afficher la source.
5. Cliquez sur **OK** pour importer le runbook dans le compte Automation.

   ![Importer un runbook pour la configuration](media/update-mgmt-enable-runbook/import-from-gallery.png)

6. Dans la page Runbook, cliquez sur **Modifier**, puis sélectionnez **Publier**.
7. Dans le volet Publier un runbook, cliquez sur **Oui** pour le publier.

## <a name="start-the-runbook"></a>Démarrer le runbook

Pour démarrer ce runbook, vous devez avoir activé Update Management pour une machine virtuelle Azure. Une machine virtuelle et un groupe de ressources existants avec la fonctionnalité activée sont nécessaires pour les paramètres.

1. Ouvrez le runbook **Enable-MultipleSolution**.

   ![Runbook avec plusieurs solutions](media/update-mgmt-enable-runbook/runbook-overview.png)

2. Cliquez sur le bouton Démarrer et entrez les valeurs des paramètres dans les champs suivants :

   * **VMNAME** : nom d’une machine virtuelle existante à ajouter à Update Management. Laissez ce champ vide pour ajouter toutes les machines virtuelles du groupe de ressources.
   * **VMRESOURCEGROUP** : nom du groupe de ressources des machines virtuelles à activer.
   * **SUBSCRIPTIONID** : ID d’abonnement de la nouvelle machine virtuelle à activer. Laissez ce champ vide pour utiliser l’abonnement de l’espace de travail. Quand vous utilisez un ID d’abonnement différent, ajoutez le compte d’identification de votre compte Automation en tant que contributeur pour l’abonnement.
   * **ALREADYONBOARDEDVM** : nom de la machine virtuelle qui est déjà activée manuellement pour les mises à jour.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** : Nom du groupe de ressources auquel appartient la machine virtuelle.
   * **TYPESOLUTION** : entrez **Updates**.

   ![Paramètres du runbook Enable-MultipleSolution](media/update-mgmt-enable-runbook/runbook-parameters.png)

3. Sélectionnez **OK** pour démarrer le travail du runbook.

4. Surveillez la progression du travail de runbook et les erreurs éventuelles dans la page **Travaux**.

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser Update Management pour des machines virtuelles, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles](update-mgmt-manage-updates-for-vm.md).

* Pour résoudre les erreurs générales d’Update Management, consultez [Résoudre les problèmes liés à Update Management](../troubleshoot/update-management.md).
