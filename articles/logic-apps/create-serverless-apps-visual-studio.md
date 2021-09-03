---
title: Création d’un exemple d’application serverless avec Visual Studio
description: À l’aide d’un modèle de démarrage rapide Azure, créez, déployez et gérez un exemple d’application serverless avec Azure Logic Apps et Azure Functions dans Visual Studio.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: df395fcffc1a7e675921f2ff397d488c301c703a
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114363808"
---
# <a name="create-an-example-serverless-app-with-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Création d’un exemple d’application serverless avec Azure Logic Apps et Azure Functions dans Visual Studio

Il est possible de créer, de générer et de déployer rapidement des applications cloud « serverless » à l’aide des services et fonctionnalités disponibles dans Azure, comme Azure Logic Apps et Azure Functions. Azure Logic Apps vous permet de développer des workflows de manière rapide et simple avec peu de code, voire sans code, pour simplifier l’orchestration des tâches combinées. Vous pouvez intégrer différents services (cloud, locaux ou hybrides), sans avoir à coder ces interactions, à maintenir du code de type glue ni à apprendre de nouvelles API et spécifications. Azure Functions, pour sa part, vous permet d’accélérer le développement suivant un modèle piloté par les événements. Vous pouvez utiliser des déclencheurs qui répondent aux événements en exécutant automatiquement votre propre code. Les liaisons vous servent à intégrer en toute transparence d’autres services.

Cet article explique comment créer un exemple d’application serverless qui s’exécute dans un environnement Azure multi-locataire avec un modèle de démarrage rapide Azure. Le modèle crée un projet de groupe de ressources Azure qui comprend un modèle de déploiement Azure Resource Manager. Ce modèle définit une ressource d’application logique de base dans laquelle un flux de travail prédéfini comprend un appel à une fonction Azure définie par vos soins. La définition du flux de travail inclut les composants suivants :

* Un déclencheur de demande qui reçoit des requêtes HTTP et que l’on met en œuvre en envoyant une demande à son URL
* Une action Azure Functions qui appelle une fonction Azure que vous pouvez définir par la suite
* Une action de réponse qui retourne une réponse HTTP contenant le résultat de la fonction

Pour plus d’informations, consultez les articles suivants :

* [Informatique serverless : présentation des technologies serverless](https://azure.microsoft.com/overview/serverless-computing/)
* [À propos d’Azure Logic Apps](logic-apps-overview.md)
* [À propos d’Azure Functions](../azure-functions/functions-overview.md)
* [Azure serverless : Vue d’ensemble de la création d’applications et de solutions informatiques avec Azure Logic Apps et Azure Functions](logic-apps-serverless-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Téléchargez et installez les outils suivants, si vous ne les possédez pas déjà :

  * [Visual Studio 2019, 2017 ou 2015 (édition Community ou autre)](https://aka.ms/download-visual-studio). Ce démarrage rapide utilise Visual Studio Community 2019, qui est gratuit.

    > [!IMPORTANT]
    > Quand vous installez Visual Studio 2019 ou 2017, veillez à sélectionner la charge de travail **Développement Azure**.

  * [Kit de développement logiciel Microsoft Azure SDK pour .NET (version 2.9.1 ou ultérieure)](https://azure.microsoft.com/downloads/). En savoir plus sur [Azure SDK pour .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Outils Azure Logic Apps de la version de Visual Studio utilisée. Vous pouvez soit [découvrir comment installer cette extension dans Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions), soit télécharger la version correspondante des outils Azure Logic Apps dans Visual Studio Marketplace :

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)
    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)
    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    > [!IMPORTANT]
    > Veillez à redémarrer Visual Studio après l’installation.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) pour pouvoir déboguer localement votre fonction Azure. Pour plus d’informations, consultez [Utilisation d’Azure Functions Core Tools](../azure-functions/functions-run-local.md).

* Accès à Internet pour utiliser le concepteur de flux de travail incorporé.

  Le concepteur réclame une connexion Internet pour créer des ressources dans Azure, et lire les propriétés et les données issues des [connecteurs managés](../connectors/managed.md) du flux de travail. Par exemple, si vous utilisez le connecteur SQL, le concepteur recherche les propriétés par défaut et personnalisées disponibles dans votre instance de serveur.

## <a name="create-a-resource-group-project"></a>Créer un projet de groupe de ressources

Pour commencer, créez un projet de groupe de ressources Azure servant de conteneur pour votre application serverless. Dans Azure, un *groupe de ressources* consiste en un regroupement logique permettant d’organiser les ressources de toute une application. Vous pouvez ainsi gérer et déployer ces ressources comme une ressource unique. Pour une application serverless dans Azure, un groupe de ressources comprend les ressources issues d’Azure Logic Apps *et* d’Azure Functions. Pour plus d’informations, consultez la [terminologie Resource Manager](../azure-resource-manager/management/overview.md#terminology).

1. Ouvrez Visual Studio et connectez-vous avec votre compte Azure, si cela vous est demandé.

1. Si la fenêtre de démarrage s’ouvre, sélectionnez **Créer un projet**.

   ![Capture d’écran montrant la fenêtre de démarrage de Visual Studio avec l’option « Créer un projet » sélectionnée.](./media/create-serverless-apps-visual-studio/start-window.png)

1. Si la fenêtre de démarrage ne s’ouvre pas, sélectionnez **Nouveau** > **Projet** dans le menu **Fichier**.

   ![Capture d’écran montrant le menu « Fichier » ouvert avec le menu « Nouveau » et l’option « Projet » sélectionnés.](./media/create-serverless-apps-visual-studio/create-new-project-visual-studio.png)

1. Lorsque la fenêtre **Créer un projet** s’ouvre, sélectionnez le modèle de projet **Groupe de ressources Azure** dans la zone de recherche pour C# ou Visual Basic. Ensuite, sélectionnez **Suivant**. Prenons ici l’exemple de C#.

   ![Capture d’écran montrant la fenêtre « Créer un projet », la zone de recherche comportant « groupe de ressources » et le modèle de projet « Groupe de ressources Azure » sélectionné.](./media/create-serverless-apps-visual-studio/start-window-find-project-template.png)

1. Lorsque la fenêtre **Configurer le nouveau projet** s’ouvre, donnez des informations sur votre projet, notamment le nom. Sélectionnez **Créer** lorsque vous avez terminé.

   ![Capture d’écran montrant la fenêtre « Configurer le nouveau projet » et les détails du projet.](./media/create-serverless-apps-visual-studio/start-window-create-new-project-details.png)

1. Lorsque la fenêtre **Sélectionner le modèle Azure** s’ouvre, sélectionnez **Démarrage rapide Azure (github.com/Azure/azure-quickstart-templates)** comme emplacement des modèles dans la liste **Afficher les modèles de cet emplacement**.

1. Dans la zone de recherche, entrez `logic-app-and-function-app`. Dans les résultats, sélectionnez le modèle nommé **quickstarts\microsoft.logic\logic-app-and-function-app**. Quand vous avez terminé, sélectionnez **OK**.

   ![Capture d’écran montrant la fenêtre « Sélectionner un modèle Azure » avec « Démarrage rapide Azure » sélectionné comme emplacement des modèles et « quickstarts\microsoft.logic\logic-app-and-function-app » sélectionné.](./media/create-serverless-apps-visual-studio/select-template.png)

   Visual Studio crée votre projet de groupe de ressources, y compris le conteneur de solution associé.

   ![Capture d’écran montrant le projet et la solution créés.](./media/create-serverless-apps-visual-studio/create-serverless-solution.png)

1. Ensuite, déployez votre solution vers Azure.

   > [!IMPORTANT]
   > Veillez à effectuer l’étape de déploiement. Sinon, vous ne pourrez pas ouvrir, consulter ni modifier le flux de travail de votre application logique avec le concepteur dans Visual Studio.

## <a name="deploy-your-solution"></a>Déployer votre solution

Pour pouvoir ouvrir votre application logique avec le concepteur dans Visual Studio, vous devez la déployer sur Azure. Cela permet au concepteur de créer des connexions aux services et aux ressources utilisés dans le flux de travail de votre application logique.

1. Dans l’Explorateur de solutions, à partir du menu contextuel de votre projet de ressources, sélectionnez **Déployer** > **Nouveau**.

   ![Capture d’écran montrant l’Explorateur de solutions avec le menu contextuel du projet et le menu « Déployer » ouverts et l’option « Nouveau » sélectionnée.](./media/create-serverless-apps-visual-studio/deploy.png)

1. Lorsque la fenêtre **Déployer dans le groupe de ressources** s’ouvre, suivez la procédure ci-dessous pour indiquer les informations de déploiement :

   1. Une fois que la fenêtre a détecté votre abonnement actuel, confirmez votre abonnement Azure ou sélectionnez un autre abonnement si vous le souhaitez.

   1. Créez un groupe de ressources dans Azure. Dans la liste **Groupe de ressources**, sélectionnez **Créer**.

      ![Capture d’écran montrant la fenêtre « Déployer dans le groupe de ressources » avec l’option « Créer » sélectionnée.](./media/create-serverless-apps-visual-studio/create-resource-group-start.png)

   1. Lorsque la fenêtre **Créer un groupe de ressources** s’ouvre, indiquez les informations suivantes :

      | Propriété | Description |
      |----------|-------------|
      | Nom de groupe ressources | Nom à donner à votre groupe de ressources |
      | Emplacement du groupe de ressources | Région du centre de données Azure dans laquelle la ressource d’application logique est hébergée |
      |||

      Dans cet exemple, nous allons créer un groupe de ressources dans la région USA Ouest.

      ![Capture d’écran montrant la fenêtre « Créer un groupe de ressources » avec des informations sur le nouveau groupe de ressources.](./media/create-serverless-apps-visual-studio/create-resource-group.png)

   1. Terminez la création et le déploiement de votre solution, par exemple :

      ![Paramètres de déploiement](./media/create-serverless-apps-visual-studio/deploy-to-resource-group.png)

1. Si la fenêtre **Modifier les paramètres** s’affiche, indiquez le nom à utiliser pour vos ressources d’application logique et d’application de fonction déployées, puis enregistrez vos modifications.

   > [!IMPORTANT]
   > Veillez à utiliser des noms globaux uniques pour votre application logique et votre application de fonction.

   ![Capture d’écran montrant la fenêtre « Modifier les paramètres » avec le nom de la ressource d’application logique et celui de la ressource d’application de fonction.](./media/create-serverless-apps-visual-studio/logic-function-app-name-parameters.png)

   Lorsque Visual Studio commence le déploiement dans le groupe de ressources, l’état de déploiement de la solution s’affiche dans la fenêtre **Sortie** de Visual Studio. À l’issue du déploiement, votre application logique est en ligne dans le portail Azure.

## <a name="open-and-edit-your-deployed-logic-app"></a>Ouverture et modification de l’application logique déployée

1. Dans l’Explorateur de solutions, sélectionnez **Ouvrir avec le concepteur d’application logique** dans le menu contextuel du fichier **azuredeploy.json**.

   ![Capture d’écran montrant le menu contextuel « azuredeploy.json » avec l’option « Ouvrir avec le concepteur d’applications logiques » sélectionnée.](./media/create-serverless-apps-visual-studio/open-designer.png)

   > [!TIP]
   > Si vous ne voyez pas cette commande dans Visual Studio 2019, vérifiez que Visual Studio possède les dernières mises à jour.

1. Lorsque le concepteur de flux de travail s’ouvre, vous pouvez continuer en modifiant le flux de travail ou en ajoutant des étapes. Une fois que vous avez terminé, n’oubliez pas d’enregistrer les modifications apportées au fichier **azuredeploy.json**.

   ![Capture d’écran montrant le flux de travail de l’application logique dans le concepteur.](./media/create-serverless-apps-visual-studio/opened-logic-app-workflow.png)

## <a name="create-an-azure-functions-project"></a>Créer un projet Azure Functions

Pour créer une fonction Azure C# au sein de votre solution, créez un projet de bibliothèque de classes C# en suivant le [Démarrage rapide : Création d’une première fonction C# dans Azure avec Visual Studio](../azure-functions/functions-create-your-first-function-visual-studio.md). Si vous souhaitez créer un projet Azure Functions et une fonction dans d’autres langages, suivez les démarrages rapides suivants :

* [Démarrage rapide : Créer une fonction Java dans Azure avec Visual Studio Code](../azure-functions/create-first-function-vs-code-java.md)
* [Démarrage rapide : Créer une fonction JavaScript dans Azure à l’aide de Visual Studio Code](../azure-functions/create-first-function-vs-code-node.md)
* [Démarrage rapide : Créer une fonction PowerShell dans Azure à l’aide de Visual Studio Code](../azure-functions/create-first-function-vs-code-powershell.md)
* [Démarrage rapide : Création d’une fonction Python dans Azure avec Visual Studio Code](../azure-functions/create-first-function-vs-code-python.md)
* [Démarrage rapide : Création d’une fonction TypeScript dans Azure avec Visual Studio Code](../azure-functions/create-first-function-vs-code-typescript.md)

## <a name="deploy-functions-from-visual-studio"></a>Déployer des fonctions depuis Visual Studio

Le modèle de déploiement de votre solution peut déployer toutes les fonctions Azure qui existent dans votre solution à partir du référentiel Git spécifié par les variables du fichier **azuredeploy.json**. Si vous créez votre projet Functions dans votre solution, vous pouvez le vérifier dans le contrôle de code source Git (par exemple GitHub ou Azure DevOps), puis mettre à jour la variable `repo` dans le fichier **azuredeploy.json** afin que le modèle déploie votre fonction Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gérer les applications logiques et afficher l’historique des exécutions

Si vous avez déjà déployé des ressources d’applications logiques dans Azure, vous pouvez les modifier, les gérer, afficher l’historique des exécutions et les désactiver dans Visual Studio. Pour plus d’informations, consultez [Gestion des applications logiques avec Visual Studio](manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour un autre exemple d’utilisation d’Azure Logic Apps et d’Azure Functions, essayez le [Tutoriel : Automatisation des tâches de traitement des e-mails avec Azure Logic Apps, Azure Functions et le Stockage Azure](tutorial-process-email-attachments-workflow.md).