---
title: Découvrez comment intégrer les solutions Update Management, Change Tracking et Inventory pour plusieurs machines virtuelles dans Azure Automation
description: Découvrez comment intégrer une machine virtuelle Azure avec Update Management, Change Tracking et Inventory qui font partie d’Azure Automation.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: d97fbe6ac515a2559340474105d73b7c9b9c6ee4
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731915"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Activer les solutions Update Management, Change Tracking et Inventory sur plusieurs machines virtuelles

Azure Automation fournit des solutions pour gérer les mises à jour de sécurité du système d’exploitation, le suivi des modifications et l’inventaire de ce qui est installé sur vos ordinateurs. Vous pouvez intégrer des machines de plusieurs façons : vous pouvez intégrer la solution [à partir d’une machine virtuelle](automation-onboard-solutions-from-vm.md), à partir de votre [compte Automation](automation-onboard-solutions-from-automation-account.md), par la navigation sur des machines virtuelles, ou par [runbook](automation-onboard-solutions.md). Cet article présente l’intégration de ces solutions par la navigation sur des machines virtuelles dans Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Se connecter à Azure à https://portal.azure.com

## <a name="enable-solutions"></a>Activer des solutions

Dans le portail Azure, accédez à **Machines virtuelles**.

À l’aide des cases à cocher, sélectionnez les machines virtuelles que vous souhaitez intégrer avec les solutions Change Tracking and Inventory ou Update Management. L’intégration est disponible pour les trois différents groupes de ressources à la fois. Les machines virtuelles Azure peuvent exister dans n’importe quelle région, quel que soit l’emplacement de votre compte Automation.

![Liste des machines virtuelles](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Utilisez les contrôles de filtre pour modifier la liste des machines virtuelles, puis cochez la case supérieure pour sélectionner toutes les machines virtuelles de la liste.

Dans la barre de commandes, cliquez sur **Services** et sélectionnez **Change tracking**, **Inventory** ou **Update Management**.

> [!NOTE]
> Change Tracking et Inventory utilisent la même solution. Lorsque l’un est activé, l’autre l’est également.

L’image suivante s’applique à Update Management. Change Tracking et Inventory ont le même layout et le même comportement.

La liste des machines virtuelles est filtrée pour afficher uniquement celles qui se trouvent dans le même abonnement et le même emplacement. Si vos machines virtuelles se trouvent dans plus de trois groupes de ressources, les trois premiers groupes de ressources sont sélectionnés.

### <a name="onboarding-limitations"></a><a name="resource-group-limit"></a> Limitations en matière d’intégration

Le nombre de groupes de ressources que vous pouvez utiliser pour l’intégration est restreint par les [limites de déploiement Resource Manager](../azure-resource-manager/templates/cross-resource-group-deployment.md). Les déploiements Resource Manager (à ne pas confondre avec les déploiements de mises à jour) sont limités à 5 groupes de ressources par déploiement. Pour garantir l’intégrité de l’intégration, 2 de ces groupes de ressources sont réservés à la configuration de l’espace de travail Log Analytics, du compte Automation et des ressources associées. Il vous reste donc 3 groupes de ressources sélectionnables pour le déploiement. Cette limite s’applique uniquement à l’intégration simultanée, pas au nombre de groupes de ressources qui peuvent être gérés par une solution Automation.

Vous pouvez également utiliser un runbook pour l’intégration. Pour plus d’informations, consultez [Intégrer les solutions de suivi de modifications et de mises à jour dans Azure Automation](automation-onboard-solutions.md).

Utilisez les contrôles de filtre pour sélectionner des machines virtuelles provenant de différents abonnements, emplacements et groupes de ressources.

![Intégrer la solution Update Management](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Passez en revue les options concernant l’espace de travail Log Analytics et le compte Automation. Un espace de travail et un compte Automation existant sont sélectionnés par défaut. Si vous souhaitez utiliser un espace de travail Log Analytics et un compte Automation différents, cliquez sur **PERSONNALISÉ** pour les sélectionner sur la page **Configuration personnalisée**. Lorsque vous choisissez un espace de travail Log Analytics, une vérification est effectuée pour déterminer si ce dernier est lié à un compte Automation. Si un compte Automation lié est trouvé, l’écran ci-après s’affiche. Une fois que vous avez terminé, cliquez sur **OK**.

![Sélectionner un espace de travail et un compte](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Si l’espace de travail sélectionné n’est pas lié à un compte Automation, l’écran ci-après apparaît. Sélectionnez un compte Automation, puis cliquez sur **OK** lorsque vous avez terminé.

![Aucun espace de travail](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> Lors de l’activation de solutions, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation.
>
> Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de région pour un compte Automation et l’espace de travail Log Analytics](how-to/region-mappings.md).

Désactivez la case à cocher en regard des machines virtuelles que vous ne souhaitez pas activer. Les machines virtuelles qui ne peuvent pas être activées sont déjà désélectionnées.

Cliquez sur **Activer** pour activer la solution. L’activation de la solution prend jusqu’à 15 minutes.

## <a name="unlink-workspace"></a>Supprimer le lien de votre espace de travail

Les solutions ci-après sont tributaires d’un espace de travail Log Analytics :

* [Gestion des mises à jour](automation-update-management.md)
* [Suivi des modifications](automation-change-tracking.md)
* [Démarrer/arrêter des machines virtuelles pendant les heures creuses](automation-solution-vm-management.md)

Si vous ne souhaitez plus intégrer votre compte Automation dans un espace de travail Log Analytics, vous pouvez supprimer son lien directement dans le portail Microsoft Azure. Avant toute chose, vous devez supprimer les solutions mentionnées précédemment. Sinon, la procédure ne pourra pas aboutir. Consultez l’article relatif à la solution que vous avez importée pour comprendre sa procédure de suppression.

Après avoir supprimé ces solutions, vous pouvez effectuer les étapes suivantes pour supprimer le lien de votre compte Automation.

> [!NOTE]
> Certaines solutions, y compris les versions antérieures de la solution de supervision de SQL Azure ont peut-être créé des ressources d’automatisation et doivent également être supprimées avant d’être de l’espace de travail.

1. Dans le portail Azure, ouvrez votre compte Automation puis, dans la page de ce dernier, sélectionnez **Espace de travail lié** dans la section **Ressources associées** sur la gauche.

2. Dans la page Dissocier l’espace de travail, cliquez sur **Dissocier l’espace de travail**.

   ![Page Dissocier l’espace de travail](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Vous recevez une invite de confirmation de la suppression.

3. Pour suivre la progression de la suppression du lien de votre espace de travail Log Analytics dans Azure Automation, sélectionnez **Notifications** dans le menu.

Si vous avez utilisé la solution de gestion de la mise à jour, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Planifications de mise à jour : chacune aura un nom correspondant aux déploiements de mise à jour que vous avez créés.

* Groupes de travail hybrides créés pour la solution : chacun a un nom similaire à machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

Si vous avez utilisé la solution Démarrer/arrêter des machines virtuelles pendant les heures creuses, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Start and stop VM runbook schedules (Démarrer et arrêter les planifications de Runbook de machine virtuelle)
* Start and stop VM runbooks (Démarrer et arrêter les Runbooks de machine virtuelle)
* Variables

Vous pouvez également dissocier votre espace de travail de votre compte Automation à partir de votre espace de travail Log Analytics. Dans votre espace de travail, sélectionnez **Compte Automation** sous **Ressources connexes**. Sur la page du compte Automation, sélectionnez **Dissocier le compte**.

## <a name="troubleshooting"></a>Dépannage

Lors de l’intégration de plusieurs machines, certaines machines peuvent afficher le message `Cannot enable`. Il existe différentes raisons pour lesquelles certaines machines ne peuvent pas être activées. Les sections suivantes expliquent les raisons possibles pour lesquelles le message `Cannot enable` s’affiche sur une machine virtuelle lors de la tentative d’intégration.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>La machine virtuelle est associée à un autre espace de travail : '\<workspaceName\>'.  Modifier la configuration afin de l’utiliser pour l’activation

**Cause** : cette erreur indique que la machine virtuelle que vous tentez d’intégrer est associée à un autre espace de travail.

**Solution**: cliquez sur **Utiliser comme configuration** pour modifier le compte Automation et l’espace de travail Log Analytics ciblés.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>La machine virtuelle est associée à un espace de travail qui n’est pas disponible pour cet abonnement

**Cause** : la machine virtuelle est associée à un espace de travail qui :

* figure dans un autre abonnement, ou
* n’existe plus, ou
* figure dans un groupe de ressources pour lequel vous n’avez pas les autorisations d’accès

**Solution**: recherchez le compte Automation lié à l’espace de travail auquel la machine virtuelle est associée, puis intégrez la machine virtuelle en modifiant la configuration de l’étendue.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>La version ou distribution du système d’exploitation de la machine virtuelle n’est pas prise en charge

**Cause :** la solution n’est pas prise en charge pour toutes les distributions de Linux ni pour toutes les versions de Windows.

**Solution :** Reportez-vous à la [liste des clients pris en charge](automation-update-management.md#supported-client-types).

### <a name="classic-vms-cannot-be-enabled"></a>Les machines virtuelles classiques ne peuvent pas être activées

**Cause** : les machines virtuelles qui utilisent le modèle de déploiement classique ne sont pas prises en charge.

**Solution**: migrez la machine virtuelle vers le modèle de déploiement de Resource Manager. Pour savoir comment procéder, consultez [Migrer des ressources d’un modèle de déploiement classique](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>La machine virtuelle est arrêtée. (désallouée)

**Cause** : la machine virtuelle n’affiche pas l’état **En cours d’exécution**.

**Solution**: pour intégrer une machine virtuelle à une solution, la machine virtuelle doit être en cours d’exécution. Cliquez sur le lien inline **Démarrer la machine virtuelle** pour démarrer la machine virtuelle sans quitter la page.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer une machine virtuelle de Update Management :

* Dans votre espace de travail Log Analytics, supprimez la machine virtuelle de la rechercher enregistrée pour la configuration d’étendue `MicrosoftDefaultScopeConfig-Updates`. Les recherches enregistrées se trouvent sous la section **Général** de votre espace de travail.
* Supprimez l’[agent Log Analytics pour Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou l’[agent Log Analytics pour Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que la solution est activée pour vos machines virtuelles, consultez l’article de présentation de la solution Update Management pour savoir comment créer un **Déploiement de mise à jour** pour vos machines.

> [!div class="nextstepaction"]
> [Gestion des mises à jour - Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](./automation-tutorial-update-management.md)

Autres tutoriels sur les solutions et leur utilisation :

* [Tutoriel - Gérer les mises à jour pour votre machine virtuelle](automation-tutorial-update-management.md)

* [Tutoriel - Identifier les logiciels sur une machine virtuelle](automation-tutorial-installed-software.md)

* [Tutoriel - Résoudre les modifications sur une machine virtuelle](automation-tutorial-troubleshoot-changes.md)
