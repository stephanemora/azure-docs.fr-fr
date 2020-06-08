---
title: Dissocier un espace de travail du compte Automation pour le suivi des modifications et l'inventaire
description: Cet article explique comment dissocier un espace de travail Log Analytics du compte Automation pour le suivi des modifications et l'inventaire
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828251"
---
# <a name="unlink-workspace-from-automation-account"></a>Dissocier un espace de travail d’un compte Automation

Vous pouvez décider de ne pas intégrer votre compte Automation à un espace de travail Log Analytics lors de l’activation des opérations [Suivi des modifications et inventaire](change-tracking.md). Cet article vous invite à dissocier l’espace de travail de votre compte.

1. Connectez-vous à Azure sur https://portal.azure.com.

2. Supprimez Gestion des mises à jour pour vos machines virtuelles. Voir [Supprimer des machines virtuelles de la fonctionnalité Suivi des modifications et inventaire](automation-remove-vms-from-change-tracking.md).

3. Si la fonctionnalité Suivi des modifications et inventaire inclut des versions antérieures d’Azure SQL Monitoring, le programme d’installation de la fonctionnalité a peut-être créé des ressources Automation que vous devez supprimer. Localisez ces ressources et supprimez-les.

4. Accédez à votre compte Automation et sélectionnez **Espace de travail lié** sous **Ressources associées** sur la gauche.

5. Dans la page Dissocier l’espace de travail, cliquez sur **Dissocier l’espace de travail** et répondez aux invites.

   ![Page Dissocier l’espace de travail](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. Pour suivre la progression de la suppression du lien dans l’espace de travail Log Analytics par Azure Automation, sélectionnez **Notifications** dans le menu.

Vous pouvez également dissocier votre espace de travail Log Analytics de votre compte Automation à partir de l’espace de travail :

1. Dans votre espace de travail, sélectionnez **Compte Automation** sous **Ressources connexes**. 
2. Sur la page du compte Automation, sélectionnez **Dissocier le compte**.

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser la fonctionnalité Suivi des modifications et inventaire, consultez [Gérer Suivi des modifications et inventaire](change-tracking-file-contents.md).
* Pour résoudre les problèmes généraux liés à la fonctionnalité, consultez [Résoudre les problèmes liés à Suivi des modifications et inventaire](troubleshoot/change-tracking.md).
