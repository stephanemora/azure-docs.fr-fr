---
title: Créer une tâche Observateur dans le compte Azure Automation | Documents Microsoft
description: Découvrez comment créer une tâche d'observateur dans le compte Azure Automation afin de surveiller les fichiers créés dans un dossier.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617355"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Créer des tâches d’observateur Azure Automation pour suivre les modifications des fichiers sur un ordinateur local

Azure Automation utilise une tâche d’observateur pour rechercher les événements et déclencher des actions avec des runbooks PowerShell. La tâche d’observateur se compose de deux éléments : l’observateur et l’action. Un runbook d’observateur s’exécute à un intervalle défini dans la tâche d’observateur et génère des données vers un runbook d’action. 

Ce didacticiel vous guide lors de la création d’une tâche d’observateur pour surveiller l’ajout d’un nouveau fichier à un répertoire. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Importer un runbook d’observateur
> * Créer une variable Automation
> * Créer un runbook d’action
> * Créer une tâche d’observateur
> * Déclencher un observateur
> * Inspecter la sortie

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [compte Automation](automation-offering-get-started.md) qui contiendra les runbooks Watcher et d'actions ainsi que la tâche d'observateur.
* Un [Runbook Worker hybride](automation-hybrid-runbook-worker.md) où la tâche d'observateur est exécutée.
* Runbooks PowerShell. Les runbooks PowerShell Workflow ne sont pas pris en charge par les tâches d’observateur.

> [!NOTE]
> Les tâches Watcher ne sont pas prises en charge dans Azure China.

## <a name="import-a-watcher-runbook"></a>Importer un runbook d’observateur

Ce didacticiel utilise un runbook d’observateur appelé **Watch-NewFile** pour rechercher les nouveaux fichiers dans un répertoire. Le runbook d’observateur récupère la dernière heure d’écriture connue pour les fichiers d’un dossier et examine tous les fichiers plus récents que ce filigrane.

Ce processus d’importation peut être effectué par le biais de [PowerShell Gallery](https://www.powershellgallery.com).

1. Accédez à la page de la galerie pour [Watch-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Sous l’onglet **Azure Automation**, cliquez sur **Déployer sur Azure Automation**.

Vous pouvez également importer ce runbook dans votre compte Automation à partir du portail en suivant les étapes ci-dessous.

1. Ouvrez votre compte Automation, puis cliquez sur la page Runbooks.
2. Cliquez sur **Parcourir la galerie** .
3. Recherchez **Runbook d’observateur**, sélectionnez **Runbook d’observateur qui recherche les nouveaux fichiers dans un répertoire**, puis cliquez sur **Importer**.
  ![Importer un runbook Automation à partir de l’interface utilisateur](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Ajoutez un nom et une description pour le runbook, puis cliquez sur **OK** pour importer le runbook dans votre compte Automation.
5. Sélectionnez **Modifier**, puis cliquez sur **Publier**. Lorsque vous y êtes invité, cliquez sur **Oui** pour publier le runbook.

## <a name="create-an-automation-variable"></a>Créer une variable Automation

Une [variable automation](automation-variables.md) sert à stocker les timestamps que le runbook précédent lit et stocke à partir de chaque fichier.

1. Sélectionnez **Variables** sous **Ressources partagées**, puis cliquez sur **+ Ajouter une variable**.
1. Saisissez le nom Watch-NewFileTimestamp.
1. Sélectionnez le type DateTime.
1. Cliquez sur **Créer** pour créer la variable Automation.

## <a name="create-an-action-runbook"></a>Créer un runbook d’action

Dans une tâche d’observateur, un runbook d’action sert à agir sur les données transmises à partir d’un runbook d’observateur. Vous devez importer un runbook d’action prédéfini appelé **Process-NewFile** à partir de [PowerShell Gallery](https://www.powershellgallery.com). 

Pour créer un runbook d’action :

1. Accédez à la page de la galerie pour [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Sous l’onglet **Azure Automation**, cliquez sur **Déployer sur Azure Automation**.

Vous pouvez également importer ce runbook dans votre compte Automation à partir du portail :

1. Accédez à votre compte Automation et sélectionnez **Runbooks** sous **Automatisation de processus**.
1. Cliquez sur **Parcourir la galerie** .
1. Recherchez **Action d’observateur**, sélectionnez **Action d’observateur qui traite des événements déclenchés par un runbook d’observateur**, puis cliquez sur **Importer**.
  ![Importer un runbook d’action à partir de l’interface utilisateur](media/automation-watchers-tutorial/importsourceaction.png)
1. Ajoutez un nom et une description pour le runbook, puis cliquez sur **OK** pour importer le runbook dans votre compte Automation.
1. Sélectionnez **Modifier**, puis cliquez sur **Publier**. Lorsque vous y êtes invité, cliquez sur **Oui** pour publier le runbook.

## <a name="create-a-watcher-task"></a>Créer une tâche d’observateur

À cette étape, vous configurez la tâche d’observateur référençant les runbooks d’observateur et d’action définis aux sections précédentes.

1. Accédez à votre compte Automation et sélectionnez **Tâches d’observateur** sous **Automatisation de processus**.
1. Sélectionnez la page des tâches d'observateur, puis cliquez sur **+ Ajouter une tâche d'observateur**.
1. Saisissez le nom **WatchMyFolder**.

1. Sélectionnez **Configurer l'observateur**, puis choisissez le Runbook **Watch-NewFile**.

1. Entrez les valeurs suivantes pour les paramètres :

   * **FOLDERPATH** : dossier du runbook Worker hybride où les fichiers sont créés, par exemple, **d:\fichiersexemples**.
   * **EXTENSION** : extension pour la configuration. Laisser vide pour traiter toutes les extensions de fichier.
   * **RECURSE** : opération récursive. Conservez cette valeur par défaut.
   * **RUN SETTINGS** : paramètre d’exécution du runbook. Sélectionnez le worker hybride.

1. Cliquez sur **OK**, puis sur **Sélectionner** pour revenir à la page de l'observateur.
1. Sélectionnez **Configurer l'action**, puis choisissez le runbook **Process-NewFile**.
1. Entrez les valeurs suivantes pour les paramètres :

   * **EVENTDATA** : données d’événement. Laisser vide. Les données sont transmises à partir du runbook Watcher.
   * **Run Settings** : paramètre d’exécution du runbook. Conservez la valeur Azure car ce runbook s'exécute dans Azure Automation.

1. Cliquez sur **OK**, puis sur **Sélectionner** pour revenir à la page de l'observateur.
1. Cliquez sur **OK** pour créer la tâche d'observateur.

![Configurer l'action d'observateur à partir de l'interface utilisateur](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Déclencher un observateur

Vous devez exécuter un test comme décrit ci-dessous pour vous assurer que la tâche d’observateur fonctionne comme prévu. 

1. Accédez à distance au runbook Worker hybride. 
2. Ouvrez **PowerShell** et créez un fichier de test dans le dossier.

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

1. Accédez à votre compte Automation et sélectionnez **Tâches d’observateur** sous **Automatisation de processus**.
1. Cliquez sur la tâche d'observateur **WatchMyFolder**.
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

