---
title: Supprimer le lien d’un compte Azure Automation à partir de Log Analytics
description: Cet article décrit comment dissocier votre compte Azure Automation d’un espace de travail Log Analytics.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e331d3841887def06de0133bb72e938b4539b65f
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Comment supprimer le lien de votre compte Automation dans un espace de travail Log Analytics

Azure Automation s’intègre dans Log Analytics non seulement pour assurer une surveillance de vos travaux de Runbook sur l’ensemble de vos comptes Automation, mais il est indispensable lorsque vous importez les solutions suivantes qui dépendent de Log Analytics :

* [Gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md)
* [Suivi des modifications](../log-analytics/log-analytics-change-tracking.md)
* [Démarrer/arrêter des machines virtuelles pendant les heures creuses](automation-solution-vm-management.md)

Si vous ne souhaitez plus intégrer votre compte Automation dans Log Analytics, vous pouvez supprimer son lien directement dans le portail Azure.  Avant toute chose, vous devez supprimer les solutions mentionnées précédemment. Sinon, la procédure ne pourra pas aboutir. Consultez la rubrique relative à la solution que vous avez importée pour comprendre sa procédure de suppression.

Après avoir supprimé ces solutions, vous pouvez effectuer les étapes suivantes pour supprimer le lien de votre compte Automation.

> [!NOTE]
> Certaines solutions, y compris les versions antérieures de la solution d’analyse de SQL Azure ont peut-être créé des ressources d’automatisation et doivent également être supprimées avant d’être de l’espace de travail.

## <a name="unlink-workspace"></a>Supprimer le lien de votre espace de travail

1. Dans le portail Azure, ouvrez votre compte Automation puis, dans le panneau de ce dernier, sélectionnez **Dissocier l'espace de travail** dans la section **Ressources associées** sur la gauche.

   ![Option Dissocier l’espace de travail](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)

1. Dans la page Dissocier l’espace de travail, cliquez sur **Dissocier l’espace de travail**.

   ![Page Dissocier l’espace de travail](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).

   Vous recevez une invite de confirmation de la suppression.

1. Pour suivre la progression de la suppression du lien de votre espace de travail Log Analytics dans Azure Automation, sélectionnez **Notifications** dans le menu.

Si vous avez utilisé la solution de gestion de la mise à jour, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Planifications de mise à jour.  Chacune aura un nom correspondant aux déploiements de mise à jour que vous avez créés.

* Groupes de travail hybrides créés pour la solution.  Chacun a un nom similaire à machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

Si vous avez utilisé la solution Démarrer/arrêter des machines virtuelles pendant les heures creuses, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Start and stop VM runbook schedules (Démarrer et arrêter les planifications de Runbook de machine virtuelle)
* Start and stop VM runbooks (Démarrer et arrêter les Runbooks de machine virtuelle)
* variables

## <a name="next-steps"></a>Étapes suivantes

Pour reconfigurer votre compte Automation et l’intégrer à Log Analytics, consultez [Transférer l’état d’un travail et des flux de travail d’Automation vers Log Analytics](automation-manage-send-joblogs-log-analytics.md).