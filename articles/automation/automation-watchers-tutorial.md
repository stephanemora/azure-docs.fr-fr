---
title: Créer une tâche Observateur dans le compte Azure Automation | Documents Microsoft
description: Découvrez comment créer une tâche d'observateur dans le compte Azure Automation afin de surveiller les fichiers créés dans un dossier.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 5dc6145940883ff6f4446ad67c399cdf4931d38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75419754"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Créer des tâches d’observateur Azure Automation pour suivre les modifications des fichiers sur un ordinateur local

Azure Automation utilise des tâches d’observateur afin de surveiller les événements et de déclencher des actions avec des runbooks PowerShell. Ce didacticiel vous guide lors de la création d’une tâche d’observateur pour surveiller l’ajout d’un nouveau fichier à un répertoire.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Importer un runbook d’observateur
> * Créer une variable Automation
> * Créer un runbook d’action
> * Créer une tâche d’observateur
> * Déclencher un observateur
> * Inspecter la sortie

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [compte Automation](automation-offering-get-started.md) qui contiendra les runbooks Watcher et d'actions ainsi que la tâche d'observateur.
* Un [Runbook Worker hybride](automation-hybrid-runbook-worker.md) où la tâche d'observateur est exécutée.

> [!NOTE]
> Les tâches Watcher ne sont pas prises en charge dans Azure China.

## <a name="import-a-watcher-runbook"></a>Importer un runbook d’observateur

Ce didacticiel utilise un runbook d’observateur appelé **Watch-NewFile** pour rechercher les nouveaux fichiers dans un répertoire. Le runbook d’observateur récupère la dernière heure d’écriture connue pour les fichiers d’un dossier et examine tous les fichiers plus récents que ce filigrane.

Ce processus d’importation peut être effectué par le biais de [PowerShell Gallery](https://www.powershellgallery.com).

1. Accédez à la page de la galerie pour [Watch-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Sous l’onglet **Azure Automation**, cliquez sur **Déployer sur Azure Automation**.

Vous pouvez également importer ce runbook dans votre compte Automation à partir du portail en suivant les étapes ci-dessous.

1. Ouvrez votre compte Automation, puis cliquez sur la page **Runbooks**.
2. Cliquez sur le bouton **Parcourir la galerie**.
3. Recherchez « runbook d’observateur », sélectionnez **Runbook d’observateur qui recherche les nouveaux fichiers dans un répertoire** et choisissez **Importer**.
  ![Importer un runbook Automation à partir de l’interface utilisateur](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Ajoutez un nom et une description pour le runbook, puis sélectionnez **OK** pour importer le runbook dans votre compte Automation.
1. Sélectionnez **Modifier**, puis cliquez sur **Publier**. Lorsque vous y êtes invité, sélectionnez **Oui** pour publier le runbook.

## <a name="create-an-automation-variable"></a>Créer une variable Automation

Une [variable automation](automation-variables.md) sert à stocker les timestamps que le runbook précédent lit et stocke à partir de chaque fichier.

1. Sélectionnez **Variables** sous **RESSOURCES PARTAGÉES**, puis **+ Ajouter une variable**.
1. Saisissez le nom « Watch-NewFileTimestamp ».
1. Sélectionnez le type DateTime.
1. Cliquez sur le bouton **Créer**. Cela crée la variable Automation.

## <a name="create-an-action-runbook"></a>Créer un runbook d’action

Dans une tâche d’observateur, un runbook d’action sert à agir sur les données transmises à partir d’un runbook d’observateur. Les runbooks PowerShell Workflow ne sont pas pris en charge par les tâches d’observateur ; vous devez utiliser des runbooks PowerShell. Vous devez importer un runbook d’action prédéfini appelé **Process-NewFile**.

Ce processus d’importation peut être effectué par le biais de [PowerShell Gallery](https://www.powershellgallery.com).

1. Accédez à la page de la galerie pour [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Sous l’onglet **Azure Automation**, cliquez sur **Déployer sur Azure Automation**.

Vous pouvez également importer ce runbook dans votre compte Automation à partir du portail en suivant les étapes ci-dessous.

1. Accédez à votre compte Automation et sélectionnez **Runbooks** sous la catégorie **AUTOMATISATION DES PROCESSUS**.
1. Cliquez sur le bouton **Parcourir la galerie**.
1. Recherchez « Action d’observateur », sélectionnez **Action d’observateur qui traite des événements déclenchés par un runbook d’observateur** et choisissez **Importer**.
  ![Importer un runbook d’action à partir de l’interface utilisateur](media/automation-watchers-tutorial/importsourceaction.png)
1. Ajoutez un nom et une description pour le runbook, puis sélectionnez **OK** pour importer le runbook dans votre compte Automation.
1. Sélectionnez **Modifier**, puis cliquez sur **Publier**. Lorsque vous y êtes invité, sélectionnez **Oui** pour publier le runbook.

## <a name="create-a-watcher-task"></a>Créer une tâche d’observateur

La tâche d’observateur contient deux parties. L’observateur et l’action. L’observateur s’exécute à intervalles fixes dans la tâche d’observateur. Données du runbook d’observateur sont transmises au runbook d’action. À cette étape, vous configurez la tâche d’observateur référençant les runbooks d’observateur et d’action définis aux étapes précédentes.

1. Accédez à votre compte Automation et sélectionnez **Tâches d’observateur** sous la catégorie **AUTOMATISATION DES PROCESSUS**.
1. Sélectionnez la page des tâches d'observateur, puis cliquez sur le bouton **+ Ajouter une tâche d'observateur**.
1. Saisissez le nom « WatchMyFolder ».

1. Sélectionnez **Configurer l'observateur**, puis choisissez le runbook **Watch-NewFile**.

1. Entrez les valeurs suivantes pour les paramètres :

   * **FOLDERPATH** : dossier du worker hybride où les fichiers sont créés. d:\examplefiles
   * **EXTENSION** : laissez vide pour traiter toutes les extensions de fichier.
   * **RECURSE** : conserver cette valeur par défaut.
   * **RUN SETTINGS** : sélectionnez le worker hybride.

1. Cliquez sur OK, puis sur Sélectionner pour revenir à la page de l'observateur.
1. Sélectionnez **Configurer l'action**, puis choisissez le runbook « Process-NewFile ».
1. Entrez les valeurs suivantes pour les paramètres :

   * **EVENTDATA** : laissez vide. Les données sont transmises à partir du runbook Watcher.
   * **Paramètres d’exécution** : conservez la valeur Azure, car ce runbook s'exécute dans le service Automation.

1. Cliquez sur **OK**, puis sur Sélectionner pour revenir à la page de l'observateur.
1. Cliquez sur **OK** pour créer la tâche d'observateur.

![Configurer l'action d'observateur à partir de l'interface utilisateur](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Déclencher un observateur

Pour tester le bon fonctionnement de l’observateur, vous devez créer un fichier de test.

Accédez à distance au worker hybride. Ouvrez **PowerShell** et créez un fichier de test dans le dossier.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

L’exemple ci-après illustre la sortie attendue.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Inspecter la sortie

1. Accédez à votre compte Automation et sélectionnez **Tâches d’observateur** sous la catégorie **AUTOMATISATION DES PROCESSUS**.
1. Cliquez sur la tâche d'observateur « WatchMyFolder ».
1. Cliquez sur **Afficher les flux d'observateurs** sous **Flux** pour voir que l'observateur a trouvé le nouveau fichier et lancé le runbook d'action.
1. Pour voir les tâches du runbook d'action, cliquez sur **Afficher les tâches d'action d'observateur**. Sélectionnez une tâche pour en voir les détails.

   ![Tâches d'action d'observateur à partir de l'interface utilisateur](media/automation-watchers-tutorial/WatcherActionJobs.png)

La sortie attendue lors de la détection du nouveau fichier peut être consultée dans l’exemple suivant :

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Importer un runbook d’observateur
> * Créer une variable Automation
> * Créer un runbook d’action
> * Créer une tâche d’observateur
> * Déclencher un observateur
> * Inspecter la sortie

Suivez ce lien pour en savoir plus sur la création de votre propre runbook.

> [!div class="nextstepaction"]
> [Mon premier runbook PowerShell](automation-first-runbook-textual-powershell.md).

