---
title: 'Didacticiel : Créer un pipeline CI/CD pour votre code existant avec Azure DevOps Projects'
description: Azure DevOps Projects facilite la prise en main d’Azure. DevOps Projects vous permet d’utiliser votre propre code et votre dépôt GitHub pour lancer une application sur le service Azure de votre choix en quelques étapes rapides.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 4a48b5c413f6dfb58a1ba3f896ded49250cde306
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286316"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Didacticiel : Créer un pipeline CI/CD pour votre code existant avec Azure DevOps Projects

Azure DevOps Projects offre une expérience simplifiée dans laquelle vous pouvez apporter vos code et dépôt Git existants ou choisir un exemple d’application pour créer un pipeline d’intégration continue (CI) et de livraison continue (CD) dans Azure.

Vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Utiliser DevOps Projects pour créer un pipeline CI/CD
> * Configurer l’accès à votre dépôt GitHub et choisir un framework
> * Configurer Azure DevOps et un abonnement Azure 
> * Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure
> * Examiner le pipeline CI/CD d’Azure Pipelines
> * Supprimer des ressources

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Vous pouvez en obtenir un gratuit via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Accédez à un dépôt GitHub ou Git externe qui contient .NET, Java, PHP, Node, Python ou du code web statique.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Azure DevOps Projects crée un pipeline CI/CD dans Azure Pipelines. Vous pouvez créer une organisation Azure DevOps ou utiliser une organisation existante. Azure DevOps Projects crée également des ressources Azure dans l’abonnement Azure de votre choix.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le volet gauche, sélectionnez **+Créer une ressource** dans la barre de navigation gauche, puis recherchez **DevOps Projects** et sélectionnez **Créer**.

   ![Le tableau de bord DevOps Projects](_img/azure-devops-project-github/azuredashboard.png)

3. Sélectionnez **Apportez votre propre code**, puis **Suivant**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Configurer l’accès à votre dépôt GitHub et choisir un framework

1. Sélectionnez **GitHub**  ou un dépôt de code **Git** externe. Sélectionnez **GitHub** pour ce tutoriel. Vous pouvez être amené à vous authentifier auprès de GitHub la première fois pour autoriser Azure à accéder à votre dépôt GitHub.

2. Terminez en sélectionnant le **dépôt** et la **branche**, puis **Suivant**.

3. Si vous utilisez des conteneurs Docker, remplacez **Application dockerisée** par **OUI** ; dans le cadre de ce tutoriel, laissez **NON** sélectionné et choisissez **Suivant**. Pour plus d’informations sur l’utilisation des conteneurs Docker, pointez sur l’icône **i**.

   ![.NET Framework](_img/azure-devops-project-github/appframework.png)

4. Dans les listes déroulantes, sélectionnez le **runtime d’application** et le **framework**, puis **Suivant**. Le framework d’application que vous choisissez détermine le type de cible de déploiement de service Azure disponible.

5. Sélectionnez le **service Azure** sur lequel déployer l’application, puis **Suivant**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurer Azure DevOps et un abonnement Azure

1. Entrez un **Nom de projet**.

2. Créez gratuitement une **organisation Azure DevOps** ou choisissez une organisation existante dans la liste déroulante.

3. Sélectionnez votre **abonnement Azure**, entrez un nom pour votre **application web** ou prenez la valeur par défaut. Sélectionnez un **emplacement**, puis **Terminé**. Après quelques minutes, la vue d’ensemble du déploiement du projet DevOps s’affiche dans le portail Azure.

4. Sélectionnez **Accéder à la ressource** pour afficher le tableau de bord du projet DevOps. Dans le coin supérieur droit, épinglez le **projet** à votre tableau de bord pour y accéder rapidement. Azure DevOps Projects configure automatiquement un déclencheur de build et de mise en production CI. Votre code reste dans votre dépôt GitHub ou un autre dépôt Git externe. Un exemple d’application est défini dans un référentiel dans votre **organisation Azure DevOps**. Un build est exécuté et votre application est déployée sur Azure.

   ![Vue du tableau de bord DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

5. Le tableau de bord donne une visibilité sur votre dépôt de code, votre pipeline CI/CD et votre application dans Azure. À droite, sous les ressources Azure, sélectionnez **Parcourir** pour voir votre application en cours d’exécution.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure

Vous êtes maintenant prêt à collaborer avec une équipe sur votre application, par le biais d’un processus d’intégration et de déploiement continus (CI/CD) qui déploie automatiquement votre dernier travail sur votre site web. Chaque modification apportée au dépôt GitHub démarre une build dans Azure DevOps tandis qu’un pipeline CD exécute un déploiement dans Azure.

1. Dans le tableau de bord de votre projet DevOps, sélectionnez **Dépôts**. Votre dépôt GitHub s’ouvre dans un nouvel onglet de navigateur. Apportez une modification à votre application, puis cliquez sur **Valider les modifications**.

2. Après quelques instants, une build démarre dans Azure Pipelines. Vous pouvez superviser l’état de la build dans le tableau de bord DevOps Projects ou dans votre organisation Azure DevOps en sélectionnant l’onglet **Pipelines de build**  dans le tableau de bord du projet.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examiner le pipeline CI/CD d’Azure Pipelines

Azure DevOps Projects configure automatiquement un pipeline CI/CD dans Azure Pipelines. Explorez et personnalisez le pipeline selon vos besoins. Pour vous familiariser avec les pipelines de build et de mise en production, effectuez les étapes suivantes :

1. Dans le tableau de bord DevOps Projects, sélectionnez **Pipelines de build**.

2. Une fois la page **Azure Pipelines** ouverte, vous verrez un historique des builds les plus récentes et l’état de chaque build.

   ![Builds du pipeline Azure DevOps](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. Dans le coin supérieur droit de la page **Builds**, vous verrez des options pour **modifier** la build actuelle, **File d’attente** pour mettre en file d’attente une nouvelle build et les points de suspension ( **&#8942;** ) pour ouvrir un menu avec plus d’options ; sélectionnez **Modifier**.

4. La build effectue diverses opérations, comme la récupération de sources dans le dépôt, la restauration de dépendances et la publication de sorties utilisées pour les déploiements. À droite sous **Nom**, remplacez le nom du pipeline de build par un nom plus descriptif. Sélectionnez **Enregistrer et mettre en file d’attente**, puis **Enregistrer**, laissez un commentaire, puis sélectionnez à nouveau **Enregistrer**.

   ![Page des builds Azure DevOps](_img/azure-devops-project-github/buildpage.png)

5. Pour voir une piste d’audit des modifications que vous avez apportées récemment à la build, sélectionnez l’onglet **Historique**. Azure DevOps effectue le suivi des modifications apportées au pipeline de build et vous permet de comparer les versions.

6. Sélectionnez l’onglet **Déclencheurs**. Le projet Azure DevOps crée automatiquement un déclencheur CI avec des paramètres par défaut. Des déclencheurs comme **Activer l’intégration continue** peuvent être définis pour exécuter une build chaque fois qu’une modification de code a été validée, ou planifier l’exécution de builds à des moments spécifiques.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand ils ne sont plus nécessaires, vous pouvez supprimer le service Azure App Service et les ressources associées que vous avez créés dans ce tutoriel. Pour ce faire, utilisez la fonctionnalité **Supprimer** du tableau de bord DevOps Projects.

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous avez configuré votre processus CI/CD dans ce tutoriel, des pipelines de mise en production et de build ont été automatiquement créés dans Azure DevOps Projects. Vous pouvez le modifier afin qu’il réponde aux besoins de votre équipe. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
>  * Utiliser DevOps Projects pour créer un pipeline CI/CD
> * Configurer l’accès à votre dépôt GitHub et choisir un framework
> * Configurer Azure DevOps et un abonnement Azure
> * Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure
> * Examiner le pipeline CI/CD d’Azure Pipelines
> * Supprimer des ressources

Pour en savoir plus sur le pipeline CI/CD, consultez :

> [!div class="nextstepaction"]
> [Définir votre pipeline de déploiement continu (CD) multiétape](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Pour en savoir plus sur la supervision des applications, consultez :
  
 > [!div class="nextstepaction"]
 > [Qu’est-ce qu’Azure Monitor ?](https://docs.microsoft.com/azure/azure-monitor/overview)
