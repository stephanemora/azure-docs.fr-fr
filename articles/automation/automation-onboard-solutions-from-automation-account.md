---
title: Découvrez comment intégrer les solutions Update Management, Change Tracking et Inventory dans Azure Automation.
description: Découvrez comment intégrer une machine virtuelle Azure avec Update Management, Change Tracking et Inventory qui font partie d’Azure Automation.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: a63202fd5f29c3c2eee4ecaaae9a940078c15edc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421874"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Intégrer les solutions Update Management, Change Tracking et Inventory

Azure Automation fournit des solutions pour gérer les mises à jour de sécurité du système d’exploitation, le suivi des modifications et l’inventaire de ce qui est installé sur vos ordinateurs. Vous pouvez intégrer des machines de nombreuses façons : vous pouvez intégrer la solution [à partir d’une machine virtuelle](automation-onboard-solutions-from-vm.md), [par la navigation sur plusieurs machines](automation-onboard-solutions-from-browse.md), à partir de votre compte Automation ou par [runbook](automation-onboard-solutions.md). Cet article traite de l’intégration de ces solutions à partir de votre compte Automation.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Se connecter à Azure à https://portal.azure.com

## <a name="enable-solutions"></a>Activer des solutions

Accédez à votre compte Automation et sélectionnez **Inventory** ou **Change Tracking** sous **GESTION DE LA CONFIGURATION**.

Choisissez l’espace de travail Log Analytics et un compte Automation, puis cliquez sur **Activer** pour activer la solution. L’activation de la solution prend jusqu’à 15 minutes.

![Intégrer la solution Inventory](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> Lors de l’activation de solutions, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation.
>
> Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de région pour un compte Automation et l’espace de travail Log Analytics](how-to/region-mappings.md).

La solution Change Tracking et Inventory fournit la possibilité de [suivre les changements](automation-vm-change-tracking.md) et de [faire l’inventaire](automation-vm-inventory.md) de vos machines virtuelles. Dans cette étape, vous activez la solution sur une machine virtuelle.

Quand vous recevez la notification d’intégration de la solution Change Tracking et Inventory, cliquez sur **Update Management** sous **GESTION DE LA CONFIGURATION**.

La solution Update Management vous permet de gérer les mises à jour et les correctifs pour vos machines virtuelles Microsoft Azure. Vous pouvez évaluer l’état des mises à jour disponibles, planifier l’installation des mises à jour obligatoires et examiner les résultats des déploiements pour vérifier que les mises à jour ont été appliquées à la machine virtuelle. Cette action a permis d’activer la solution pour votre machine virtuelle.

Sélectionnez **Update Management** sous **UPDATE MANAGEMENT**. L’espace de travail Log Analytics sélectionné est le même que celui utilisé à l’étape précédente. Cliquez sur **Activer** pour intégrer la solution Update Management. L’activation de la solution prend jusqu’à 15 minutes.

![Intégrer la solution Update](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Configuration d’étendue

Chaque solution utilise une configuration d’étendue au sein de l’espace de travail pour cibler les ordinateurs qui obtiennent la solution. La configuration d’étendue est un groupe de recherches enregistrées utilisé pour limiter l’étendue de la solution à des ordinateurs spécifiques. Pour accéder aux configurations d’étendue, dans votre compte Automation sous **RESSOURCES CONNEXES**, sélectionnez **Espace de travail**. Ensuite, dans l’espace de travail sous **SOURCES DE DONNÉES DE L’ESPACE DE TRAVAIL**, sélectionnez **Configurations des étendues**.

Si l’espace de travail sélectionné ne contient pas encore les solutions Update Management ou Change Tracking, les configurations d’étendue suivantes sont créées :

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Si l’espace de travail sélectionné comporte déjà la solution, cette dernière n’est pas redéployée et la configuration d’étendue n’y est pas ajoutée.

## <a name="saved-searches"></a>Recherches enregistrées

Quand un ordinateur est ajouté aux solutions Update Management ou Change Tracking et Inventory, celles-ci sont ajoutées à l’une des deux recherches enregistrées dans votre espace de travail. Ces recherches enregistrées sont des requêtes qui contiennent les ordinateurs ciblés pour ces solutions.

Accédez à votre compte Automation et sélectionnez **Recherches enregistrées** sous **Ggénéral**. Les deux recherches enregistrées utilisées par ces solutions sont présentées dans le tableau suivant :

|Name     |Category  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Mises à jour        | Updates__MicrosoftDefaultComputerGroup         |

Sélectionnez l’une des deux recherches enregistrées pour afficher la requête utilisée pour remplir le groupe. L’image suivante montre la requête et ses résultats :

![Recherches enregistrées](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Intégrer des machines virtuelles Azure

À partir de votre compte Automation, sélectionnez **Inventory** ou **Change Tracking** sous **GESTION DE LA CONFIGURATION**, ou **Update Management** sous **UPDATE MANAGEMENT**.

Cliquez sur **+ Ajouter des machines virtuelles Azure**, sélectionnez une ou plusieurs machines virtuelles dans la liste. Les machines virtuelles qui ne peuvent pas être activées sont grisées et ne peuvent pas être sélectionnées. Les machines virtuelles Azure peuvent exister dans n’importe quelle région, quel que soit l’emplacement de votre compte Automation. Dans la page **Activer Update Management**, cliquez sur **Activer**. Cette action ajoute les machines virtuelles sélectionnées à la recherche enregistrée de groupe d’ordinateurs pour la solution.

![Activer les machines virtuelles Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Intégrer une machine non-Azure

Les machines qui ne sont pas dans Azure doivent être ajoutées manuellement. À partir de votre compte Automation, sélectionnez **Inventory** ou **Change Tracking** sous **GESTION DE LA CONFIGURATION**, ou **Update Management** sous **UPDATE MANAGEMENT**.

Cliquez sur **Ajouter une machine virtuelle non-Azure**. Une nouvelle fenêtre de navigateur s’ouvre contenant des [instructions pour installer et configurer Microsoft Monitoring Agent sur la machine](../azure-monitor/platform/log-analytics-agent.md) pour que celle-ci puisse commencer à envoyer des informations à la solution. Si vous intégrez une machine actuellement gérée par System Center Operations Manager, l’installation d’un nouvel agent n’est pas nécessaire, car les informations de l’espace de travail sont entrées dans l’agent existant.

## <a name="onboard-machines-in-the-workspace"></a>Intégrer des machines dans l’espace de travail

Les machines installées manuellement ou les machines déjà connectées à votre espace de travail doivent être ajoutées à Azure Automation pour que la solution soit activée. À partir de votre compte Automation, sélectionnez **Inventory** ou **Change Tracking** sous **GESTION DE LA CONFIGURATION**, ou **Update Management** sous **UPDATE MANAGEMENT**.

Sélectionnez **Gérer des machines**. La page **Gérer des machines** s’ouvre. Cette page vous permet soit d’activer la solution sur un ensemble sélectionné de machines ou sur toutes les machines disponibles, soit d’activer la solution pour toutes les machines actuelles et de l’activer sur toutes les futures machines. Le bouton **Gérer des machines** peut être grisé si vous avez précédemment choisi l’option **Activer sur tous les ordinateurs disponibles et à venir**.

![Recherches enregistrées](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Toutes les machines disponibles

Pour activer la solution sur toutes les machines disponibles, sélectionnez **Activer sur toutes les machines disponibles**. La commande permettant d’ajouter des machines individuellement est alors désactivée. Cette tâche ajoute tous les noms des machines envoyant des informations à l’espace de travail dans la requête de recherche enregistrée de groupe d’ordinateurs. Quand cette option est sélectionnée, le bouton **Gérer des machines** est désactivé.

### <a name="all-available-and-future-machines"></a>Toutes les machines disponibles et futures

Pour activer la solution sur toutes les machines disponibles et les futures machines, sélectionnez **Activer sur tous les ordinateurs disponibles et à venir**. Cette option supprime les recherches enregistrées et les configurations d’étendue de l’espace de travail. Cette action ouvre la solution à toutes les machines Azure et non-Azure qui envoient des informations à l’espace de travail. Quand cette option est sélectionnée, le bouton **Gérer des machines** est désactivé définitivement, car il n’existe plus aucune configuration d’étendue.

Vous pouvez rajouter les configurations d’étendue en rajoutant les recherches enregistrées initiales. Pour plus d'informations, consultez [Recherches enregistrées](#saved-searches).

### <a name="selected-machines"></a>Machines sélectionnées

Pour activer la solution sur une ou plusieurs machines, sélectionnez **Activer sur les machines sélectionnées** et cliquez sur **Ajouter** en regard de chaque machine à ajouter à la solution. Cette tâche ajoute les noms de machine sélectionnés à la requête de recherche enregistrée de groupe d’ordinateurs pour la solution.

## <a name="unlink-workspace"></a>Supprimer le lien de votre espace de travail

Les solutions ci-après sont tributaires d’un espace de travail Log Analytics :

* [Gestion des mises à jour](automation-update-management.md)
* [Suivi des modifications](automation-change-tracking.md)
* [Démarrer/arrêter des machines virtuelles pendant les heures creuses](automation-solution-vm-management.md)

Si vous ne souhaitez plus intégrer votre compte Automation dans un espace de travail Log Analytics, vous pouvez supprimer son lien directement dans le portail Microsoft Azure.  Avant de poursuivre, vous devez d’abord supprimer les solutions mentionnées précédemment, sinon la procédure ne peut pas aboutir. Consultez l’article relatif à la solution que vous avez importée pour comprendre sa procédure de suppression.

Après avoir supprimé ces solutions, vous pouvez effectuer les étapes suivantes pour supprimer le lien de votre compte Automation.

> [!NOTE]
> Certaines solutions, y compris les versions antérieures de la solution de supervision de SQL Azure ont peut-être créé des ressources d’automatisation et doivent également être supprimées avant d’être de l’espace de travail.

1. Dans le portail Azure, ouvrez votre compte Automation puis, dans la page de ce compte qui est affichée, sélectionnez **Espace de travail lié** dans la section intitulée **Ressources associées** sur la gauche.

2. Dans la page Dissocier l’espace de travail, cliquez sur **Dissocier l’espace de travail**.

   ![Page Dissocier l’espace de travail](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Vous recevez une invite de confirmation de la poursuite de l’opération.

3. Pour suivre la progression de la suppression du lien du compte dans votre espace de travail Log Analytics par Azure Automation, sélectionnez **Notifications** dans le menu.

Si vous avez utilisé la solution de gestion de la mise à jour, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Planifications de mise à jour : chacune aura un nom correspondant aux déploiements de mise à jour que vous avez créés.

* Groupes de travail hybrides créés pour la solution : chacun a un nom similaire à machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

Si vous avez utilisé la solution Démarrer et Arrêter des machines virtuelles pendant les heures creuses, vous pouvez, si vous le souhaitez, supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Start and stop VM runbook schedules (Démarrer et arrêter les planifications de Runbook de machine virtuelle)
* Start and stop VM runbooks (Démarrer et arrêter les Runbooks de machine virtuelle)
* Variables

Vous pouvez également dissocier votre espace de travail de votre compte Automation à partir de votre espace de travail Log Analytics. Dans votre espace de travail, sélectionnez **Compte Automation** sous **Ressources connexes**. Sur la page du compte Automation, sélectionnez **Dissocier le compte**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer une machine virtuelle de Update Management :

* Dans votre espace de travail Log Analytics, supprimez la machine virtuelle de la rechercher enregistrée pour la configuration d’étendue `MicrosoftDefaultScopeConfig-Updates`. Les recherches enregistrées se trouvent sous la section **Général** de votre espace de travail.
* Supprimez l’agent [Microsoft Monitoring agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou l’[agent Log Analytics pour Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

Poursuivez avec les tutoriels sur les solutions pour apprendre à les utiliser.

* [Tutoriel - Gérer les mises à jour pour votre machine virtuelle](automation-tutorial-update-management.md)

* [Tutoriel - Identifier les logiciels sur une machine virtuelle](automation-tutorial-installed-software.md)

* [Tutoriel - Résoudre les modifications sur une machine virtuelle](automation-tutorial-troubleshoot-changes.md)
