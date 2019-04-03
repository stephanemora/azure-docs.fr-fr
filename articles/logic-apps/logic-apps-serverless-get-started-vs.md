---
title: Créer des applications serverless avec Azure Logic Apps et Azure Functions dans Visual Studio
description: Créer, déployer et gérer votre première application serverless avec Azure Logic Apps et Azure Functions dans Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 04/02/2019
ms.openlocfilehash: 39b44668a89ce0c77c09a7fa20dc4d95b2164bf4
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862996"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Créer votre première application serverless avec Azure Logic Apps et Azure Functions - Visual Studio

Vous pouvez développer et déployer rapidement des applications cloud à l’aide des fonctionnalités et outils serverless dans Azure, comme [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et [Azure Functions](../azure-functions/functions-overview.md). Cet article explique comment démarrer la création d’une application serverless, celle-ci utilise une application logique qui effectue un appel à une fonction Azure, dans Visual Studio. Pour en savoir plus sur les solutions serverless dans Azure, consultez [Azure Serverless avec Functions et Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Conditions préalables

Pour créer une application serverless dans Visual Studio, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Téléchargez et installez ces outils, si vous ne les avez pas déjà :

  * <a href="https://aka.ms/download-visual-studio" target="_blank">Visual Studio 2019, 2017 ou 2015 - Community edition ou supérieur</a>. 
  Ce démarrage rapide utilise Visual Studio Community 2017, qui est gratuit.

    > [!IMPORTANT]
    > Lorsque vous installez Visual Studio 2019 ou 2017, vous assurer que vous sélectionnez le **développement Azure** charge de travail.
    > Pour Visual Studio 2019, Cloud Explorer peut ouvrir le Concepteur d’application logique dans le portail Azure, mais encore Impossible d’ouvrir le Concepteur d’application logique intégré.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Microsoft Azure SDK pour .NET (2.9.1 ou version ultérieure)</a>. En savoir plus sur <a href="https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet">Azure SDK pour .NET</a>.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Outils Azure Logic Apps pour la version de Visual Studio :

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019" target="_blank">Visual Studio 2019</a>

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017" target="_blank">Visual Studio 2017</a>

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015" target="_blank">Visual Studio 2015</a>
  
    Vous pouvez télécharger et installer les outils Azure Logic Apps directement à partir de Visual Studio Marketplace ou en apprendre davantage sur <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">l’installation de cette extension dans Visual Studio</a>. 
    Veillez à redémarrer Visual Studio après l’installation.

  * <a href="https://www.npmjs.com/package/azure-functions-core-tools" target="_blank">Azure Functions Core Tools</a> pour déboguer Functions localement

* Accès au web lors de l’utilisation du Concepteur d’application logique intégré

  Le Concepteur requiert une connexion Internet pour créer des ressources dans Azure et pour lire les propriétés et les données à partir de connecteurs dans votre application logique. 
  Par exemple, si vous utilisez le connecteur Dynamics CRM Online, le Concepteur recherche les propriétés par défaut et personnalisées disponibles dans votre instance CRM.

## <a name="create-resource-group-project"></a>Créer un projet de groupe de ressources

Pour commencer, créez un [projet de groupe de ressources Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) pour votre application serverless. Dans Azure, vous créez des ressources au sein d’un groupe de ressources, en fait dans une collection logique que vous utilisez pour organiser, gérer et déployer des ressources pour une application entière, en tant que composant unique. Pour une application serverless dans Azure, votre groupe de ressources comprend des ressources Azure Logic Apps et Azure Functions. En savoir plus sur [les ressources et groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md).

1. Démarrez Visual Studio et connectez-vous avec votre compte Azure.

1. Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.

   ![Création de projet dans Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Sous **Installé**, sélectionnez **Visual C#** ou **Visual Basic**. Sélectionnez **Cloud** > **Groupe de ressources Azure**.

   > [!NOTE]
   > Si la catégorie **Cloud** ou le projet **Groupe de ressources Azure** n’existe pas, vérifiez que vous avez installé le kit SDK Azure pour Visual Studio.

   Si vous utilisez Visual Studio 2019, procédez comme suit :

   1. Dans le **créer un nouveau projet** boîte, sélectionnez le **groupe de ressources Azure** modèle de projet pour un visuel C# ou Visual Basic, puis choisissez **suivant**.

   1. Indiquez le nom pour le groupe de ressources Azure que vous souhaitez utiliser et d’autres informations de projet. Lorsque vous êtes prêt, choisissez **Créer**.

1. Attribuez un nom et un emplacement au projet, puis choisissez **OK**.

   Visual Studio vous invite à sélectionner un modèle à partir de la liste des modèles. 
   Cet exemple utilise un modèle de démarrage rapide Azure, vous pouvez ainsi créer une application sans serveur qui inclut une application logique et un appel à une fonction Azure.

   > [!TIP]
   > Dans les scénarios où vous ne souhaitez pas predeploy votre solution dans un groupe de ressources Azure, vous pouvez utiliser l’essai à blanc **application logique** modèle qui crée simplement une application logique vide.

1. À partir de la **afficher les modèles de cet emplacement** liste, sélectionnez **Azure Quickstart (github.com/Azure/azure-quickstart-templates)**.

1. Dans la zone de recherche, entrez « application logique » comme filtre. À partir des résultats, sélectionnez ce modèle : **101-logic-app-and-function-app**

   ![Sélection du modèle de démarrage rapide Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio crée et ouvre une solution pour votre projet de groupe de ressources. 
   Le modèle de démarrage rapide Azure que vous avez sélectionné crée un modèle de déploiement nommé `azuredeploy.json` à l’intérieur de votre projet de groupe de ressources. Ce modèle de déploiement inclut la définition d’une application logique simple qui se déclenche sur une requête HTTP, appelle une fonction Azure et retourne le résultat sous la forme d’une réponse HTTP.

   ![Nouvelle solution serverless](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Vous devez ensuite déployer votre solution sur Azure avant de pouvoir ouvrir le modèle de déploiement et passer en revue les ressources de votre application serverless.

## <a name="deploy-your-solution"></a>Déployer votre solution

Avant de pouvoir ouvrir votre application logique avec le concepteur d’application logique dans Visual Studio, vous devez disposer d’un groupe de ressources Azure déjà déployé dans Azure. Le concepteur peut alors créer des connexions aux services et aux ressources dans votre application logique. Pour cette tâche, déployez votre solution depuis Visual Studio sur le portail Azure.

1. Dans l’Explorateur de solutions, dans le menu contextuel du projet de votre ressource, sélectionnez **déployer** > **New**.

   ![Création du déploiement pour le groupe de ressources](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Si cela n’est pas déjà fait, sélectionnez votre abonnement Azure et le groupe de ressources vers lequel vous voulez déployer. Choisissez **Déployer**.

   ![Paramètres de déploiement](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Si le champ **Modifier les paramètres** s’affiche, indiquez le nom de la ressource à utiliser pour votre application logique et votre application de fonction Azure au moment du déploiement, puis enregistrez vos paramètres. Veillez à utiliser un nom global unique pour votre application de fonction.

   ![Attribution du nom de l’application logique et de celui de l’application de fonction](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Lorsque Visual Studio commence le déploiement vers votre groupe de ressources spécifié, l’état de déploiement de votre solution s’affiche dans la fenêtre Visual Studio **Sortie**. 
   À l’issue du déploiement, votre application logique est en ligne dans le portail Azure.

## <a name="edit-logic-app-in-visual-studio"></a>Modifier l’application logique dans Visual Studio

Maintenant que votre solution est déployée sur votre groupe de ressources, ouvrez votre application logique avec le Concepteur d’applications logiques afin que vous pouvez modifier et modifier votre application logique.

1. Dans l’Explorateur de solutions, à partir de la `azuredeploy.json` menu contextuel du fichier, sélectionnez **ouvrir avec Concepteur d’application logique**.

   ![Ouverture de « azuredeploy.json » dans le concepteur d’application logique](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. Lorsque la boîte de dialogue **Propriétés de l’application logique** s’affiche, et s’il n’est pas déjà sélectionné, dans la zone **Abonnement**, sélectionnez votre abonnement Azure. Sous **Groupe de ressources**, sélectionnez le groupe de ressources et l’emplacement où vous avez déployé votre solution, puis choisissez **OK**.

   ![Propriétés de l’application logique](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Une fois le concepteur d’application logique ouvert, vous pouvez continuer à ajouter des étapes ou à modifier le flux de travail, et enregistrer vos mises à jour.

   ![Application logique ouverte dans le concepteur d’application logique](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Créer un projet Azure Functions

Si vous voulez créer votre projet Functions pour qu’il fonctionne avec JavaScript, Python, F#, PowerShell, Batch ou Bash, suivez les étapes décrites dans l’article [Utiliser Azure Functions Core Tools](../azure-functions/functions-run-local.md). Pour développer votre fonction Azure avec C# à l’intérieur de votre solution, vous pouvez utiliser une bibliothèque de classes C# en suivant les étapes décrites dans l’article [Publier une bibliothèque de classes .NET comme application de fonction](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Déployer des fonctions depuis Visual Studio

Votre modèle de déploiement met en œuvre toutes les fonctions Azure qui existent dans votre solution à partir du dépôt Git spécifié par les variables du fichier `azuredeploy.json`. Si vous créez votre projet Functions dans votre solution, vous pouvez vérifier ce projet dans le contrôle de code source Git, par exemple GitHub ou Azure DevOps, puis mettre à jour la variable `repo` afin que le modèle déploie votre fonction Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gérer les applications logiques et afficher l’historique des exécutions

Pour les applications logiques déjà déployées dans Azure, vous pouvez toujours modifier, gérer, afficher l’historique des exécutions et désactiver ces applications à partir de Visual Studio.

1. Dans le menu **Affichage** de Visual Studio, ouvrez **Cloud Explorer**.

1. Sous **Tous les abonnements**, sélectionnez l’abonnement Azure associé aux applications logiques que vous souhaitez gérer, puis choisissez **Appliquer**.

1. Dans la zone **Logic Apps**, sélectionnez votre application logique. À partir du menu contextuel de l’application, sélectionnez **Ouvrir avec l’éditeur d’application logique**.

Vous pouvez à présent télécharger l’application logique déjà publiée dans votre projet de groupe de ressources. En effet, même si vous avez démarré une application logique dans le portail Azure, vous avez toujours la possibilité de l’importer pour la gérer dans Visual Studio. Pour plus d’informations, consultez [Gérer des applications logiques avec Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Étapes suivantes

* [Gérer des applications logiques avec Visual Studio](manage-logic-apps-with-visual-studio.md)
