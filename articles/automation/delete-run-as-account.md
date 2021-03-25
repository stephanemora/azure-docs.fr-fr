---
title: Supprimer un compte d’identification Azure Automation
description: Cet article décrit comment supprimer un compte d’identification avec PowerShell ou à partir du portail Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99056770"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Supprimer un compte d’identification Azure Automation

Les comptes d’identification d’Azure Automation assurent l’authentification pour la gestion des ressources sur le modèle de déploiement Azure Resource Manager ou Azure Classic à l’aide de runbooks Automation et d’autres fonctionnalités Automation. Cet article décrit comment supprimer un compte d’identification standard ou Classic. Lorsque vous effectuez cette opération, le compte Automation est conservé. Après avoir supprimé le compte d’identification, vous pouvez le recréer sur le Portail Azure ou avec le script PowerShell fourni.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Supprimer un compte d’identification standard ou Classic

1. Dans le portail Azure, ouvrez le compte Automation.

2. Dans le volet gauche, sélectionnez **Comptes d’identification** dans la section des paramètres de compte.

3. Dans la page de propriétés Comptes d’identification, sélectionnez le compte d’identification standard ou le compte d’identification Classic que vous voulez supprimer.

4. Dans le volet Propriétés, pour le compte sélectionné, cliquez sur **Supprimer**.

   ![Supprimer un compte d’identification](media/delete-run-as-account/automation-account-delete-run-as.png)

5. Pour suivre la progression de la suppression du compte, accédez à l’onglet **Notifications** du menu.

## <a name="next-steps"></a>Étapes suivantes

Pour recréer votre compte d’identification standard ou Classic, consultez [Créer des comptes d’identification](create-run-as-account.md).