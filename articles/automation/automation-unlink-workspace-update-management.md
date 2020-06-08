---
title: Dissocier un espace de travail d’un compte Automation pour Update Management
description: Cet article explique comment dissocier un espace de travail Log Analytics du compte Automation pour Update Management
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835782"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Dissocier un espace de travail d’un compte Automation pour Update Management

Vous pouvez décider de ne pas intégrer votre compte Automation à un espace de travail Log Analytics pendant les opérations [Update Management](automation-update-management.md). Cet article vous invite à dissocier l’espace de travail de votre compte.

1. Connectez-vous à Azure sur https://portal.azure.com.

2. Supprimez Gestion des mises à jour pour vos machines virtuelles. Voir [Supprimer des machines virtuelles d’Update Management](automation-remove-vms-from-update-management.md).

3. Si la fonctionnalité Update Management inclut des versions antérieures d’Azure SQL Monitoring, le programme d’installation de la fonctionnalité a peut-être créé des ressources Automation que vous devez supprimer. Pour Update Management, vous pouvez supprimer les éléments suivants qui ne sont plus nécessaires :

   * Planifications des mises à jour : chacune a un nom qui correspond au déploiement de mise à jour que vous avez créé.
   * Groupes Worker hybrides créés pour Update Management : chacun porte un nom similaire à machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

4. Accédez à votre compte Automation et sélectionnez **Espace de travail lié** sous **Ressources associées** sur la gauche.

5. Dans la page Dissocier l’espace de travail, cliquez sur **Dissocier l’espace de travail** et répondez aux invites.

   ![Page Dissocier l’espace de travail](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Pour suivre la progression de la suppression du lien dans l’espace de travail Log Analytics par Azure Automation, sélectionnez **Notifications** dans le menu.

Vous pouvez également dissocier votre espace de travail Log Analytics de votre compte Automation à partir de l’espace de travail :

1. Dans votre espace de travail, sélectionnez **Compte Automation** sous **Ressources connexes**. 
2. Sur la page du compte Automation, sélectionnez **Dissocier le compte**.

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser la fonctionnalité, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
* Pour détecter un problème lié aux fonctionnalités, consultez [Résolution des problèmes d’Update Management](troubleshoot/update-management.md).
* Pour détecter un problème de l’agent de mise à jour Windows, consultez [Résolution des problèmes liés à l’agent de mise à jour Windows](troubleshoot/update-agent-issues.md).
* Pour détecter un problème lié à l’agent de mise à jour Linux, consultez [Résolution des problèmes de l’agent de mise à jour Linux](troubleshoot/update-agent-issues-linux.md).
