---
title: Créer un pipeline CI/CD pour .NET avec Azure DevOps Project | Guide de démarrage rapide
description: DevOps Project facilite la prise en main d’Azure. Il vous aide à lancer une application .NET sur le service Azure de votre choix en quelques étapes rapides.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 085a5e59beb3bd8ddd219e66ec0d81e9772ac62b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407601"
---
# <a name="create-a-cicd-pipeline-for-net-with-the-azure-devops-project"></a>Créer un pipeline CI/CD pour .NET avec Azure DevOps Project

Configurez l’intégration continue (CI) et la livraison continue (CD) pour votre application .NET Core ou ASP.NET avec **Azure DevOps Projects**.  Azure DevOps Projects simplifie la configuration initiale d’un pipeline de build et de mise en production Azure DevOps Services.

Si vous ne disposez pas d’un abonnement Azure, vous pouvez en obtenir un gratuitement via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Azure DevOps Projects crée un pipeline CI/CD dans Azure DevOps Services.  Vous pouvez créer une organisation **Azure DevOps Services** ou utiliser une **organisation existante**.  Azure DevOps Projects crée également des **ressources Azure** dans **l’abonnement Azure** de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Sélectionnez l’icône **Créer une ressource** dans la barre de navigation gauche, puis recherchez **DevOps Projects**.  Cliquez sur **Créer**.

    ![Démarrage de la livraison continue](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Sélectionner un exemple d’application et le service Azure

1. Sélectionnez Exemple d’application **.NET**.  Les exemples .NET offrent le choix entre une infrastructure ASP.NET open source et une infrastructure .NET Core multiplateforme.

    ![.NET Framework](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

1. Sélectionnez l’infrastructure d'application **.NET Core**.  Cet exemple est une application MVC ASP.NET Core. Une fois que vous avez terminé, sélectionnez **Suivant**.

1. La cible de déploiement par défaut est **Web App sur Windows**.  Si vous le souhaitez, vous pouvez choisir Web App sur Linux ou Web App pour conteneurs.  L’infrastructure d’application, que vous avez choisie dans les étapes précédentes, détermine le type de cible de déploiement de service Azure disponible ici.  Laissez le service par défaut, puis choisissez **Suivant**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurer Azure DevOps Services et un abonnement Azure 

1. Créez gratuitement une **nouvelle** organisation Azure DevOps Services ou choisissez une organisation **existante**.  Choisissez un **nom** pour votre projet Azure DevOps.  Sélectionnez votre **abonnement Azure**, **l’emplacement**, puis le **nom** de votre application.  Une fois que vous avez terminé, sélectionnez **Terminé**.

1. Le **tableau de bord DevOps Projects** se charge en quelques minutes sur le Portail Azure.  Un exemple d’application est configuré dans un référentiel de votre organisation Azure DevOps Services, un build s’exécute et votre application se déploie sur Azure.  Ce tableau de bord vous permet de visualiser votre **référentiel de code**, votre **pipeline CI/CD Azure DevOps Services** et votre **application sur Azure**.  Sur la droite du tableau de bord, sélectionnez **Parcourir** pour afficher votre application en cours d’exécution.

    ![Vue du tableau de bord](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Valider les modifications de code et exécuter CI/CD

Azure DevOps Projects a créé un référentiel Git dans votre organisation Azure DevOps Services ou votre compte GitHub.  Suivez les étapes ci-dessous pour afficher le référentiel et modifier le code dans votre application.

1. Sur la gauche du tableau de bord DevOps Projects, sélectionnez le lien correspondant à votre branche **maîtresse**.  Ce lien ouvre une vue sur le référentiel Git récemment créé.

1. Pour afficher l’URL du clone du référentiel, sélectionnez **Clone** en haut à droite du navigateur. Vous pouvez cloner votre référentiel Git dans votre environnement de développement intégré favori.  Dans les prochaines étapes, vous allez utiliser le navigateur web pour effectuer des modifications directement dans la branche maîtresse et les valider.

1. Sur la gauche du navigateur, accédez au fichier **Views/Home/index.cshtml**.

1. Sélectionnez **Modifier** et modifiez le titre h2.  Par exemple, tapez **Prise en main directe d’Azure DevOps Project** ou autre chose.

    ![Modifications du code](_img/azure-devops-project-aspnet-core/codechange.png)

1. Choisissez **Valider** et enregistrez vos modifications.

1. Dans le navigateur, accédez au **tableau de bord Azure DevOps Projects**.  Vous devez maintenant voir qu’une build est en cours.  Les modifications que vous avez effectuées sont automatiquement générées et déployées via un pipeline CI/CD Azure DevOps Services.

## <a name="examine-the-azure-devops-services-cicd-pipeline"></a>Examiner le pipeline CI/CD Azure DevOps Services

Azure DevOps Projects a automatiquement configuré un pipeline CI/CD Azure DevOps Services complet dans votre organisation Azure DevOps Services.  Explorez et personnalisez le pipeline selon vos besoins.  Suivez les étapes ci-dessous pour vous familiariser avec les pipelines de build et de mise en production Azure DevOps Services.

1. Sélectionnez **Pipelines de build** en **haut** du tableau de bord Azure DevOps Projects.  Ce lien ouvre un onglet de navigateur ainsi que le pipeline de build Azure DevOps Services de votre nouveau projet.

1. Sélectionnez les **points de suspension**.  Cette action ouvre un menu permettant de lancer plusieurs activités, comme mettre un nouveau build en file d’attente, suspendre un build et modifier le pipeline de build.

1. Sélectionnez **Modifier**.

    ![Pipeline de build](_img/azure-devops-project-aspnet-core/builddef.png)

1. Dans cette vue, **examinez les différentes tâches** de votre pipeline de build.  Le build effectue différentes tâches, notamment récupérer (fetch) des sources dans le référentiel Git Azure Repos, restaurer les dépendances et publier les sorties utilisées pour les déploiements.

1. En haut du pipeline de build, sélectionnez le **nom du pipeline de build**.

1. Remplacez le **nom** de votre pipeline de build par un nom plus descriptif.  Sélectionnez **Enregistrer et mettre en file d’attente**, puis **Enregistrer**.

1. Sous le nom de votre pipeline de build, sélectionnez **Historique**.  Vous pouvez observer une piste d’audit des modifications que vous avez apportées récemment à la build.  Azure DevOps Services suit les modifications apportées au pipeline de build et permet de comparer les versions.

1. Sélectionnez **Déclencheurs**.  Azure DevOps Projects a créé automatiquement un déclencheur CI, et chaque validation dans le référentiel crée un nouveau build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

1. Sélectionnez **Rétention**.  Selon votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

1. Sélectionnez **Build et mise en production**, puis **Versions**.  Azure DevOps Projects a créé un pipeline de mise en production Azure DevOps Services pour gérer les déploiements sur Azure.

1. Sur le côté gauche du navigateur, sélectionnez les **points de suspension** à côté de votre pipeline de mise en production, puis choisissez **Modifier**.

1. Le pipeline de mise en production contient un **pipeline** qui définit le processus de mise en production.  Sous **Artefacts**, sélectionnez **Déposer**.  Le pipeline de build que vous avez examiné aux étapes précédentes génère la sortie utilisée pour l’artefact. 

1. À droite de l’icône **Déposer**, sélectionnez le **déclencheur de déploiement continu**.  Ce pipeline de mise en production possède un déclencheur CD actif, qui exécute un déploiement à chaque fois qu’un nouvel artefact de build est disponible.  Si vous le souhaitez, vous pouvez désactiver le déclencheur afin que vos déploiements nécessitent une exécution manuelle. 

1. À gauche du navigateur, sélectionnez **Tâches**.  Il s’agit des activités effectuées par votre processus de déploiement.  Dans cet exemple, une tâche a été créée pour déployer un **service Azure App**.

1. À droite du navigateur, sélectionnez **Afficher les mises en production**.  Cette vue affiche un historique des mises en production.

1. Sélectionnez les **points de suspension** situés en regard de vos mises en production, puis **Ouvrir**.  Il y a plusieurs menus à explorer dans cette vue, comme un résumé des mises en production, les éléments de travail associés et les tests.

1. Sélectionnez **Validations**.  Cette vue présente les validations de code associées au déploiement spécifique. 

1. Sélectionnez **Journaux**.  Les journaux contiennent des informations utiles sur le processus de déploiement.  Ils peuvent être affichés pendant et après les déploiements.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez supprimer le service Azure App et les ressources associées créées dans ce guide de démarrage rapide à l’aide de la fonction **Supprimer** du tableau de bord Azure DevOps Projects.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la modification des pipelines de build et de mise en production dans le but de répondre aux besoins de votre équipe, consultez ce tutoriel :

> [!div class="nextstepaction"]
> [Personnaliser le processus CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>vidéos

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
