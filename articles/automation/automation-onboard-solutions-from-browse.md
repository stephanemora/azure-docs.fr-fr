---
title: Découvrez comment intégrer les solutions Update Management, Change Tracking et Inventory pour plusieurs machines virtuelles dans Azure Automation
description: Découvrez comment intégrer une machine virtuelle Azure avec Update Management, Change Tracking et Inventory qui font partie d’Azure Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0a624d850b8c3260acb24cb17566090e8ad0043e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233935"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Activer les solutions Update Management, Change Tracking et Inventory sur plusieurs machines virtuelles

Azure Automation fournit des solutions pour gérer les mises à jour de sécurité du système d’exploitation, le suivi des modifications et l’inventaire de ce qui est installé sur vos ordinateurs. Vous pouvez intégrer des machines de plusieurs façons : vous pouvez intégrer la solution [à partir d’une machine virtuelle](automation-onboard-solutions-from-vm.md), à partir de votre [compte Automation](automation-onboard-solutions-from-automation-account.md), par la navigation sur des machines virtuelles, ou par [runbook](automation-onboard-solutions.md). Cet article présente l’intégration de ces solutions par la navigation sur des machines virtuelles dans Azure.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à Azure à l’adresse https://portal.azure.com.

## <a name="enable-solutions"></a>Activer des solutions

Dans le portail Azure, accédez à **Machines virtuelles**.

À l’aide des cases à cocher, sélectionnez les machines virtuelles que vous souhaitez intégrer avec les solutions Change Tracking and Inventory ou Update Management. L’intégration est disponible pour les trois différents groupes de ressources à la fois.

![Liste des machines virtuelles](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Utilisez les contrôles de filtre pour modifier la liste des machines virtuelles, puis cochez la case supérieure pour sélectionner toutes les machines virtuelles de la liste.

Dans la barre de commandes, cliquez sur **Services** et sélectionnez **Change tracking**, **Inventory** ou **Update Management**.

> [!NOTE]
> **Change Tracking** et **Inventory** utilisent la même solution. Quand l’une est activée, l’autre l’est aussi.

L’image suivante s’applique à Update Management. Update Management et Inventory ont la même disposition et le même comportement.

La liste des machines virtuelles est filtrée pour afficher uniquement celles qui se trouvent dans le même abonnement et le même emplacement. Si vos machines virtuelles se trouvent dans plus de trois groupes de ressources, les trois premiers groupes de ressources sont sélectionnés.

Utilisez les contrôles de filtre pour sélectionner des machines virtuelles provenant de différents abonnements, emplacements et groupes de ressources.

![Intégrer la solution Update Management](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Passez en revue les options concernant l’espace de travail Log Analytics et le compte Automation. Un nouvel espace de travail et un nouveau compte Automation sont sélectionnés par défaut. Si vous disposez déjà d’un espace de travail Log Analytics et d’un compte Automation que vous souhaitez utiliser, cliquez sur **change** (modifier) pour les sélectionner dans la page **Configuration**. Une fois que vous avez terminé, cliquez sur **Enregistrer**.

![Sélectionner un espace de travail et un compte](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Désactivez la case à cocher en regard des machines virtuelles que vous ne souhaitez pas activer. Les machines virtuelles qui ne peuvent pas être activées sont déjà désélectionnées.

Cliquez sur **Activer** pour activer la solution. L’activation de la solution prend jusqu’à 15 minutes.

## <a name="troubleshooting"></a>Résolution de problèmes

Lors de l’intégration de plusieurs machines, certaines machines peuvent afficher le message **Cannot enable** (Impossible d’activer). Il existe différentes raisons pour lesquelles certaines machines ne peuvent pas être activées. Les sections suivantes expliquent les raisons possibles pour lesquelles le message **Cannot enable** (Impossible d’activer) s’affiche sur une machine virtuelle lors de la tentative d’intégration.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>La machine virtuelle est associée à un autre espace de travail : '\<workspaceName\>'.  Modifier la configuration afin de l’utiliser pour l’activation

**Cause** : cette erreur indique que la machine virtuelle que vous tentez d’intégrer est associée à un autre espace de travail.

**Solution** : cliquez sur **Use as configuration** (Utiliser comme configuration) pour modifier le compte Automation et l’espace de travail Log Analytics ciblés.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>La machine virtuelle est associée à un espace de travail qui n’est pas disponible pour cet abonnement

**Cause** : la machine virtuelle est associée à un espace de travail qui :

* figure dans un autre abonnement, ou
* n’existe plus, ou
* figure dans un groupe de ressources pour lequel vous n’avez pas les autorisations d’accès

**Solution** : recherchez le compte Automation lié à l’espace de travail auquel la machine virtuelle est associée, puis intégrez la machine virtuelle en modifiant la configuration de l’étendue.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>La version ou distribution du système d’exploitation de la machine virtuelle n’est pas prise en charge

**Cause :** la solution n’est pas prise en charge pour toutes les distributions de Linux ou toutes les versions de Windows.

**Solution :** reportez-vous à la [liste des clients pris en charge](automation-update-management.md#clients) pour la solution.

### <a name="classic-vms-cannot-be-enabled"></a>Les machines virtuelles classiques ne peuvent pas être activées

**Cause** : les machines virtuelles qui utilisent le modèle de déploiement classique ne sont pas prises en charge.

**Solution** : migrez la machine virtuelle vers le modèle de déploiement de Resource Manager. Pour savoir comment procéder, consultez [Migrer des ressources d’un modèle de déploiement classique](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>La machine virtuelle est arrêtée. (désallouée)

**Cause** : la machine virtuelle n’affiche pas l’état **Running** (en cours d’exécution).

**Solution** : pour intégrer une machine virtuelle à une solution, la machine virtuelle doit être en cours d’exécution. Cliquez sur le lien inline **Démarrer la machine virtuelle** pour démarrer la machine virtuelle sans quitter la page.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que la solution est activée pour vos machines virtuelles, consultez l’article de présentation de la solution Update Management pour savoir comment afficher l’évaluation des mises à jour pour vos machines.

> [!div class="nextstepaction"]
> [Update Management - Afficher l’évaluation des mises à jour](./automation-update-management.md#viewing-update-assessments)

Autres tutoriels sur les solutions et leur utilisation :

* [Tutoriel - Gérer les mises à jour pour votre machine virtuelle](automation-tutorial-update-management.md)

* [Tutoriel - Identifier les logiciels sur une machine virtuelle](automation-tutorial-installed-software.md)

* [Tutoriel - Résoudre les modifications sur une machine virtuelle](automation-tutorial-troubleshoot-changes.md)