---
title: Créer des applications serverless avec Visual Studio | Microsoft Docs
description: Créer, déployer et gérer votre première application serverless avec Azure Logic Apps et Azure Functions dans Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.custom: vs-azure
ms.topic: article
ms.date: 08/01/2018
ms.openlocfilehash: a69c129d5ae1405462e3a54a24cd2edbad2a86a7
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126775"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Créer votre première application serverless avec Azure Logic Apps et Azure Functions - Visual Studio

Vous pouvez développer et déployer rapidement des applications cloud à l’aide des fonctionnalités et outils serverless dans Azure, comme [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et [Azure Functions](../azure-functions/functions-overview.md). Cet article explique comment démarrer la création d’une application serverless, celle-ci utilise une application logique qui effectue un appel à une fonction Azure, dans Visual Studio. Pour en savoir plus sur les solutions serverless dans Azure, consultez [Azure Serverless avec Functions et Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Prérequis

Pour créer une application serverless dans Visual Studio, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* [Visual Studio 2017](https://www.visualstudio.com/vs/) ou Visual Studio 2015 : Communauté, Professionnel ou Entreprise

* [Microsoft Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 ou version ultérieure)

* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

* [Outils Azure Logic Apps pour Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) ou [Visual Studio 2015](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio)

  Vous pouvez télécharger et installer les outils Azure Logic Apps directement à partir de Visual Studio Marketplace ou [en apprendre davantage sur l’installation de cette extension dans Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
  Veillez à redémarrer Visual Studio après l’installation. 

* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) pour déboguer Functions localement

* Accès au web lors de l’utilisation du concepteur d’application logique intégré à Visual Studio

  Le Concepteur requiert une connexion Internet pour créer des ressources dans Azure et pour lire les propriétés et les données à partir de connecteurs dans votre application logique. 
  Par exemple, si vous utilisez le connecteur Dynamics CRM Online, le Concepteur recherche les propriétés par défaut et personnalisées disponibles dans votre instance CRM.

## <a name="create-resource-group-project"></a>Créer un projet de groupe de ressources

Pour commencer, créez un [projet de groupe de ressources Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) pour votre application serverless. Dans Azure, vous créez des ressources au sein d’un groupe de ressources, en fait dans une collection logique que vous utilisez pour organiser, gérer et déployer des ressources pour une application entière, en tant que composant unique. Pour une application serverless dans Azure, votre groupe de ressources comprend des ressources Azure Logic Apps et Azure Functions. En savoir plus sur [les ressources et groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md).

1. Démarrez Visual Studio et connectez-vous avec votre compte Azure. 

1. Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**. 

   ![Création de projet dans Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Sous **Installé**, sélectionnez **Visual C#** ou **Visual Basic**. Sélectionnez **Cloud** > **Groupe de ressources Azure**.

   Si la catégorie **Cloud** ou le projet **Groupe de ressources Azure** n’existe pas, vérifiez que vous avez installé le kit SDK Azure pour Visual Studio.

1. Attribuez un nom et un emplacement au projet, puis choisissez **OK**. 

   Visual Studio vous invite à sélectionner un modèle. 
   Vous pouvez démarrer avec un modèle Logic App vide, ou un autre modèle, mais cet exemple utilise un modèle de démarrage rapide Azure pour créer une application serverless comprenant une application logique et un appel à une fonction Azure.

   Pour créer seulement une application logique dans Visual Studio, sélectionnez le modèle d’**application logique**. Ce modèle crée une application logique vide qui s’ouvre dans le concepteur d’application logique, sans que vous ayez besoin de déployer au préalable votre solution dans un groupe de ressources Azure.

1. Dans la zone **Afficher les modèles à partir de cet emplacement**, sélectionnez **Démarrage rapide Azure (github/Azure/azure-quickstart-templates)**. 

1. Dans la zone de recherche, entrez « logic-app » comme filtre, puis sélectionnez le modèle de démarrage rapide serverless suivant et choisissez **OK** : **101-logic-app-and-function-app**

   ![Sélection du modèle de démarrage rapide Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio crée et ouvre une solution pour votre projet de groupe de ressources. 
   Le modèle de démarrage rapide que vous avez sélectionné crée un modèle de déploiement nommé `azuredeploy.json` dans votre projet de groupe de ressources. 
   Ce modèle de déploiement inclut la définition d’une application logique simple qui se déclenche sur une requête HTTP, appelle une fonction Azure et retourne le résultat sous la forme d’une réponse HTTP. 
   
   ![Nouvelle solution serverless](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Vous devez ensuite déployer votre solution sur Azure avant de pouvoir ouvrir le modèle de déploiement et passer en revue les ressources de votre application serverless. 

## <a name="deploy-your-solution"></a>Déployer votre solution

Avant de pouvoir ouvrir votre application logique avec le concepteur d’application logique dans Visual Studio, vous devez disposer d’un groupe de ressources Azure déjà déployé dans Azure. Le concepteur peut alors créer des connexions aux services et aux ressources dans votre application logique. Pour cette tâche, déployez votre solution depuis Visual Studio sur le portail Azure.

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel de votre projet de ressources et sélectionnez **Déployer** > **Nouveau**.

   ![Création du déploiement pour le groupe de ressources](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Si cela n’est pas déjà fait, sélectionnez votre abonnement Azure et le groupe de ressources vers lequel vous voulez déployer. Choisissez **Déployer**.

   ![Paramètres de déploiement](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Si le champ **Modifier les paramètres** s’affiche, indiquez le nom de la ressource à utiliser pour votre application logique et votre application de fonction Azure au moment du déploiement, puis enregistrez vos paramètres. Veillez à utiliser un nom global unique pour votre application de fonction.

   ![Attribution du nom de l’application logique et de celui de l’application de fonction](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Lorsque Visual Studio commence le déploiement vers votre groupe de ressources spécifié, l’état de déploiement de votre solution s’affiche dans la fenêtre Visual Studio **Sortie**. 
   À l’issue du déploiement, votre application logique est en ligne dans le portail Azure.

## <a name="edit-logic-app-in-visual-studio"></a>Modifier l’application logique dans Visual Studio

Maintenant que votre solution est déployée dans votre groupe de ressources, ouvrez votre application logique avec le concepteur d’application logique, afin de modifier et de transformer votre application logique.

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel du fichier `azuredeploy.json` et sélectionnez **Ouvrir avec le concepteur d’application logique**.

   ![Ouverture de « azuredeploy.json » dans le concepteur d’application logique](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. Lorsque la boîte de dialogue **Propriétés de l’application logique** s’affiche, et s’il n’est pas déjà sélectionné, dans la zone **Abonnement**, sélectionnez votre abonnement Azure. Sous **Groupe de ressources**, sélectionnez le groupe de ressources et l’emplacement où vous avez déployé votre solution, puis choisissez **OK**.

   ![Propriétés de l’application logique](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Une fois le concepteur d’application logique ouvert, vous pouvez continuer à ajouter des étapes ou à modifier le flux de travail, et enregistrer vos mises à jour.

   ![Application logique ouverte dans le concepteur d’application logique](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Créer un projet Azure Functions

Si vous voulez créer votre projet Functions pour qu’il fonctionne avec JavaScript, Python, F#, PowerShell, Batch ou Bash, suivez les étapes décrites dans l’article [Utiliser Azure Functions Core Tools](../azure-functions/functions-run-local.md). Pour développer votre fonction Azure avec C# à l’intérieur de votre solution, vous pouvez utiliser une bibliothèque de classes C# en suivant les étapes décrites dans l’article [Publier une bibliothèque de classes .NET comme application de fonction](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Déployer des fonctions depuis Visual Studio

Votre modèle de déploiement met en œuvre toutes les fonctions Azure qui existent dans votre solution à partir du dépôt Git spécifié par les variables du fichier `azuredeploy.json`. Si vous créez votre projet Functions dans votre solution, vous pouvez vérifier ce projet dans le contrôle de code source Git, par exemple GitHub ou Visual Studio Team Services, puis mettre à jour la variable `repo` afin que le modèle déploie votre fonction Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gérer les applications logiques et afficher l’historique des exécutions

Pour les applications logiques déjà déployées dans Azure, vous pouvez toujours modifier, gérer, afficher l’historique des exécutions et désactiver ces applications à partir de Visual Studio. 

1. Dans le menu **Affichage** de Visual Studio, ouvrez **Cloud Explorer**. 

1. Sous **Tous les abonnements**, sélectionnez l’abonnement Azure associé aux applications logiques que vous souhaitez gérer, puis choisissez **Appliquer**.

1. Dans la zone **Logic Apps**, sélectionnez votre application logique. À partir du menu contextuel de l’application, sélectionnez **Ouvrir avec l’éditeur d’application logique**. 

Vous pouvez à présent télécharger l’application logique déjà publiée dans votre projet de groupe de ressources. En effet, même si vous avez démarré une application logique dans le portail Azure, vous avez toujours la possibilité de l’importer pour la gérer dans Visual Studio. Pour plus d’informations, consultez [Gérer des applications logiques avec Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer un tableau de bord de réseau social serverless](logic-apps-scenario-social-serverless.md)
* [Gérer des applications logiques avec Visual Studio](manage-logic-apps-with-visual-studio.md)
* [Langage de définition de flux de travail d’application logique](logic-apps-workflow-definition-language.md)