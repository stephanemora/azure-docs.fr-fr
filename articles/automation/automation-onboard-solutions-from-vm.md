---
title: Intégrer les solutions Update Management, Change Tracking et Inventory à partir d’une machine virtuelle Azure
description: Découvrez comment intégrer une machine virtuelle Azure avec Update Management, Change Tracking et Inventory, qui font partie d’Azure Automation.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 039e2d3c70493868ca2f79e89fc82d8970ec6865
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032389"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Intégrer les solutions Update Management, Change Tracking et Inventory à partir d’une machine virtuelle Azure

Azure Automation fournit des solutions pour gérer les mises à jour de sécurité du système d’exploitation, le suivi des modifications et l’inventaire des éléments installés sur vos ordinateurs. Il existe plusieurs manières d’intégrer des machines. Vous pouvez intégrer la solution à partir d’une machine virtuelle, [à partir de votre compte Automation](automation-onboard-solutions-from-automation-account.md), [via la navigation sur plusieurs machines](automation-onboard-solutions-from-browse.md) ou via un [runbook](automation-onboard-solutions.md). Cet article traite de l’intégration de ces solutions à partir d’une machine virtuelle Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="enable-the-solutions"></a>Activer les solutions

Accédez à une machine virtuelle existante. Sous **OPÉRATIONS**, sélectionnez **Gestion des mises à jour**, **Inventaire**, ou **Suivi des modifications**.

Si vous voulez activer la solution pour la machine virtuelle uniquement, assurez-vous que l’option **Activer pour cette machine virtuelle** est bien sélectionnée. Pour intégrer plusieurs machines à la solution, sélectionnez **Activer pour les machines virtuelles dans cet abonnement**, puis choisissez **Cliquer pour sélectionner les machines à activer**. Consultez la section [Intégrer les solutions Update Management, Change Tracking et Inventory](automation-onboard-solutions-from-automation-account.md) pour savoir comment intégrer plusieurs machines en même temps.

Sélectionnez l’espace de travail Log Analytics et le compte Automation, puis cliquez sur **Activer** pour activer la solution. L’activation de la solution prend jusqu’à 15 minutes.

![Intégrer la solution Update Management](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Accédez aux autres solutions, puis sélectionnez **Activer**. Les listes déroulantes des comptes Log Analytics et Automation sont désactivées, car ces solutions utilisent le même espace de travail et le même compte Automation que la solution précédemment activée.

> [!NOTE]
> **Change Tracking** et **Inventory** utilisent la même solution. Lorsqu’une de ces solutions est activée, l’autre l’est également.

## <a name="scope-configuration"></a>Configuration d’étendue

Chaque solution utilise une configuration d’étendue au sein de l’espace de travail pour cibler les ordinateurs qui obtiennent la solution. La configuration d’étendue est un groupe de recherches enregistrées utilisé pour limiter l’étendue de la solution à des ordinateurs spécifiques. Pour accéder aux configurations d’étendue, dans votre compte Automation sous **RESSOURCES CONNEXES**, sélectionnez **Espace de travail**. Dans l’espace de travail, sous **SOURCES DE DONNÉES DE L’ESPACE DE TRAVAIL**, sélectionnez **Configurations des étendues**.

Si l’espace de travail sélectionné n’inclut pas encore les solutions Update Management et Change Tracking, les configurations de portée suivantes sont créées :

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Si l’espace de travail sélectionné comporte déjà la solution, cette dernière n’est pas redéployée et la configuration de l’étendue n’est pas ajoutée.

Cliquez sur le bouton de sélection (**...**) dans l’une des configurations, puis sélectionnez **Modifier**. Dans le volet **Modifier la configuration d’étendue**, sélectionnez **Sélectionner des groupes d’ordinateurs**. Le volet **Groupes d’ordinateurs** affiche les recherches enregistrées utilisées pour créer la configuration d’étendue.

## <a name="saved-searches"></a>Recherches enregistrées

Lorsqu’un ordinateur est ajouté aux solutions Update Management, Change Tracking ou Inventaire, il est ajouté à l’une des deux recherches enregistrées dans votre espace de travail. Les recherches enregistrées sont des requêtes qui contiennent les ordinateurs ciblés pour ces solutions.

Accédez à votre espace de travail. Sous **Général**, sélectionnez **Recherches enregistrées**. Les deux recherches enregistrées utilisées par ces solutions sont présentées dans le tableau suivant :

|NOM     |Catégorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Mises à jour        | Updates__MicrosoftDefaultComputerGroup         |

Sélectionnez l’une des deux recherches enregistrées pour afficher la requête utilisée pour remplir le groupe. L’image suivante montre la requête et ses résultats :

![Recherches enregistrées](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Supprimer le lien de votre espace de travail

Les solutions ci-après sont tributaires d’un espace de travail Log Analytics :

* [Gestion des mises à jour](automation-update-management.md)
* [Suivi des modifications](automation-change-tracking.md)
* [Démarrer/arrêter des machines virtuelles pendant les heures creuses](automation-solution-vm-management.md)

Si vous ne souhaitez plus intégrer votre compte Automation dans Log Analytics, vous pouvez supprimer son lien directement dans le portail Azure.  Avant toute chose, vous devez supprimer les solutions mentionnées précédemment. Sinon, la procédure ne pourra pas aboutir. Consultez l’article relatif à la solution que vous avez importée pour comprendre sa procédure de suppression.

Après avoir supprimé ces solutions, vous pouvez effectuer les étapes suivantes pour supprimer le lien de votre compte Automation.

> [!NOTE]
> Certaines solutions, y compris les versions antérieures de la solution d’analyse de SQL Azure ont peut-être créé des ressources d’automatisation et doivent également être supprimées avant d’être de l’espace de travail.

1. Dans le portail Azure, ouvrez votre compte Automation puis, dans la page de ce dernier, sélectionnez **Espace de travail lié** dans la section **Ressources associées** sur la gauche.

1. Dans la page Dissocier l’espace de travail, cliquez sur **Dissocier l’espace de travail**.

   ![Page Dissocier l’espace de travail](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Vous recevez une invite de confirmation de la suppression.

1. Pour suivre la progression de la suppression du lien de votre espace de travail Log Analytics dans Azure Automation, sélectionnez **Notifications** dans le menu.

Si vous avez utilisé la solution de gestion de la mise à jour, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Planifications de mise à jour : chacune aura un nom correspondant aux déploiements de mise à jour que vous avez créés.

* Groupes de travail hybrides créés pour la solution : chacun a un nom similaire à machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

Si vous avez utilisé la solution Démarrer/arrêter des machines virtuelles pendant les heures creuses, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Start and stop VM runbook schedules (Démarrer et arrêter les planifications de Runbook de machine virtuelle)
* Start and stop VM runbooks (Démarrer et arrêter les Runbooks de machine virtuelle)
* variables

## <a name="next-steps"></a>Étapes suivantes

Passez aux tutoriels sur les solutions pour apprendre à les utiliser :

* [Tutoriel - Gérer les mises à jour de votre machine virtuelle](automation-tutorial-update-management.md)
* [Tutoriel - Identifier les logiciels sur une machine virtuelle](automation-tutorial-installed-software.md)
* [Tutoriel - Résoudre les modifications sur une machine virtuelle](automation-tutorial-troubleshoot-changes.md)
