---
title: Démarrage rapide Azure - Créer un runbook Azure Automation | Microsoft Docs
description: Apprendre à créer un runbook Azure Automation
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 5a6dbda59495fccb6b9d53440f408fd4750925b5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75421667"
---
# <a name="create-an-azure-automation-runbook"></a>Créer un runbook Azure Automation

Les runbooks Azure Automation peuvent être créés via Azure. Cette méthode fournit une interface utilisateur basée sur un navigateur pour créer des runbooks Automation. Dans ce démarrage rapide, vous passez au travers de la création, de la modification, du test et de la publication d’un runbook Automation PowerShell.

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Se connecter à Azure à https://portal.azure.com

## <a name="create-runbook"></a>Créer un runbook

Tout d’abord, créez un runbook. L’exemple de runbook créé dans ce démarrage rapide génère `Hello World` par défaut.

1. Ouvrez votre compte Automation.

1. Cliquez sur **Runbooks** sous **AUTOMATISATION DE PROCESSUS**. La liste des runbooks s’affiche.

1. Cliquez sur le bouton **Ajouter un Runbook** situé en haut de la liste.

1. Saisissez « Hello World » comme **nom** de runbook, puis sélectionnez **PowerShell** comme **type de runbook**. Cliquez sur **Créer**.

   ![Saisir les informations relatives à votre runbook Automation dans la page](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Le runbook est créé et la page **Modifier le runbook PowerShell** s’ouvre.

    ![Créer le script PowerShell dans l’éditeur de runbook](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. Saisissez ou bien copiez et collez le code suivant dans le volet d’édition. Il crée un paramètre d’entrée facultatif nommé « Nom » avec « World » comme valeur par défaut et il génère une chaîne qui utilise cette valeur d’entrée :

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Cliquez sur **Enregistrer** pour enregistrer un brouillon du runbook.

    ![Créer le script PowerShell dans l’éditeur de runbook](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>Tester le Runbook

Une fois le runbook créé, vous testez le runbook pour vérifier qu’il fonctionne.

1. Cliquez sur **Volet de test** pour ouvrir la page **Test**.

1. Saisissez une valeur comme **nom**, puis cliquez sur **Démarrer**. Le travail de test démarre et l’état du travail ainsi que la sortie s’affichent.

    ![Travail de test de runbook](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. Fermez la page **Test** en cliquant sur le symbole **X** dans le coin supérieur droit. Sélectionnez **OK** dans le menu contextuel qui s’affiche.

1. Sur la page **Modifier un runbook PowerShell**, cliquez sur **Publier** pour publier le runbook en tant que version officielle du runbook au sein du compte.

   ![Travail de test de runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>Exécuter le runbook

Une fois le runbook publié, la page de vue d’ensemble apparaît.

1. Dans la page de vue d’ensemble de runbook, cliquez sur **Démarrer** pour ouvrir la page de configuration **Démarrer le Runbook** pour ce runbook.

   ![Travail de test de runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. Laissez le **nom** vide afin que la valeur par défaut soit utilisée, puis cliquez sur **OK**. Le travail de runbook est soumis et la page du travail s’affiche.

   ![Travail de test de runbook](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. Lorsque **l’état du travail** est **En cours d’exécution** ou **Terminé**, cliquez sur **Sortie** pour ouvrir le volet **Sortie** et afficher la sortie du runbook.

   ![Travail de test de runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsqu’il n’est plus nécessaire, supprimez le runbook. Pour le faire, sélectionnez le runbook dans la liste de runbooks, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé, modifié, testé et publié un runbook et vous avez démarré un travail de runbook. Pour en savoir plus sur les runbooks Automation, consultez l’article traitant des différents types de runbooks que vous pouvez créer et utiliser dans Automation.

> [!div class="nextstepaction"]
> [Procédures Automation - Types de runbooks](./automation-runbook-types.md)
