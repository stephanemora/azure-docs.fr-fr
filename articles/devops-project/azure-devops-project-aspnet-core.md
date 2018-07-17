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
ms.openlocfilehash: c858288768e9a3d93015881bf8ebbb4e9d4dc008
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37966977"
---
# <a name="create-a-cicd-pipeline-for-net-with-the-azure-devops-project"></a>Créer un pipeline CI/CD pour .NET avec Azure DevOps Project

Configurez l’intégration continue (CI) et la livraison continue (CD) pour votre application .NET core ou ASP.NET avec **Azure DevOps Project**.  Azure DevOps Project simplifie la configuration initiale d’un pipeline de mise en production et de build.

Si vous ne disposez pas d’un abonnement Azure, vous pouvez en obtenir un gratuitement via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Azure DevOps Projects crée un pipeline CI/CD dans VSTS.  Vous pouvez créer un **compte VSTS** ou utiliser un **compte existant**.  Azure DevOps Project crée également des **ressources Azure** dans l’**abonnement Azure** de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Sélectionnez l’icône **Créer une ressource** dans la barre de navigation gauche, puis recherchez **Projet DevOps**.  Cliquez sur **Créer**.

    ![Démarrage de la livraison continue](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Sélectionner un exemple d’application et le service Azure

1. Sélectionnez Exemple d’application **.NET**.  Les exemples .NET offrent le choix entre une infrastructure ASP.NET open source et une infrastructure .NET Core multiplateforme.

    ![.NET Framework](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

1. Sélectionnez l’infrastructure d'application **.NET Core**.  Cet exemple est une application MVC ASP.NET Core. Une fois que vous avez terminé, sélectionnez **Suivant**.

1. La cible de déploiement par défaut est **Web App sur Windows**.  Si vous le souhaitez, vous pouvez choisir Web App sur Linux ou Web App pour conteneurs.  L’infrastructure d’application, que vous avez choisie dans les étapes précédentes, détermine le type de cible de déploiement de service Azure disponible ici.  Laissez le service par défaut, puis choisissez **Suivant**.

## <a name="configure-vsts-and-an-azure-subscription"></a>Configurer VSTS et un abonnement Azure 

1. Créez un **nouveau** compte VSTS gratuit ou choisissez un compte **existant**.  Choisissez un **nom** pour votre projet VSTS.  Sélectionnez votre **abonnement Azure**, **l’emplacement**, puis le **nom** de votre application.  Une fois que vous avez terminé, sélectionnez **Terminé**.

    ![Entrer les informations VSTS](_img/azure-devops-project-aspnet-core/vstsazureinfo.png)

1. En quelques minutes, le **tableau de bord du projet** est chargé dans le portail Azure.  Un exemple d’application est configuré dans un référentiel dans votre compte VSTS, une build est exécutée, et votre application est déployée dans Azure.  Ce tableau de bord fournit une visibilité dans votre **référentiel de code**, votre **pipeline VSTS CI/CD** et votre **application dans Azure**.  Sur le côté droit du tableau de bord, sélectionnez **Parcourir** pour afficher votre application en cours d’exécution.

    ![Vue du tableau de bord](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Valider les modifications de code et exécuter CI/CD

Azure DevOps Project a créé un référentiel Git dans votre compte VSTS ou GitHub.  Suivrez les étapes suivantes pour voir le référentiel et modifier le code dans votre application.

1. Sur la gauche du tableau de bord DevOps Project, sélectionnez le lien pour votre branche **maîtresse**.  Ce lien ouvre une vue dans le référentiel Git récemment créé.

1. Pour afficher l’URL clone du référentiel, sélectionnez **Clone** en haut à droite du navigateur. Vous pouvez cloner votre référentiel Git dans votre environnement de développement intégré préféré.  Dans les prochaines étapes, vous allez utiliser le navigateur web pour effectuer des modifications directement dans la branche maîtresse et les valider.

1. Sur la gauche du navigateur, accédez au fichier **Views/Home/index.cshtml**.

1. Sélectionnez **Modifier** et modifiez le titre h2.  Par exemple, tapez **Prise en main directe d’Azure DevOps Project** ou autre chose.

    ![Modifications du code](_img/azure-devops-project-aspnet-core/codechange.png)

1. Choisissez **Valider** et enregistrez vos modifications.

1. Dans votre navigateur, accédez au **tableau de bord Azure DevOps Projects**.  Vous devez maintenant voir qu’une build est en cours.  Les modifications effectuées sont automatiquement générées et déployées via un pipeline VSTS CI/CD.

## <a name="examine-the-vsts-cicd-pipeline"></a>Examiner le pipeline VSTS CI/CD

Azure DevOps Project a configuré automatiquement un pipeline VSTS CI/CD complet dans votre compte VSTS.  Explorez et personnalisez le pipeline selon vos besoins.  Suivez les étapes ci-dessous pour vous familiariser avec les définitions de mise en production et de build VSTS.

1. Sélectionnez **Pipelines de build** en **haut** du tableau de bord Azure DevOps Project.  Ce lien ouvre un onglet de navigateur ainsi que la définition de build VSTS pour votre nouveau projet.

1. Sélectionnez les **points de suspension**.  Cette action ouvre un menu où vous pouvez démarrer plusieurs activités, telles que mettre une nouvelle build en file d'attente, suspendre une build, et modifier une définition de build.

1. Sélectionnez **Modifier**.

    ![Définition de build](_img/azure-devops-project-aspnet-core/builddef.png)

1. Dans cette vue, **examinez les différentes tâches** de votre définition de build.  La build effectue différentes tâches, telles que la récupération (fetch) de sources dans le référentiel Git, la restauration de dépendances et la publication de sorties utilisées pour les déploiements.

1. En haut de la définition de build, sélectionnez le **nom de la définition de build**.

1. Modifiez le **nom** de votre définition de build pour le remplacer par un nom plus descriptif.  Sélectionnez **Enregistrer et mettre en file d’attente**, puis **Enregistrer**.

1. Sous le nom de votre définition de build, sélectionnez **Historique**.  Vous pouvez observer une piste d’audit des modifications que vous avez apportées récemment à la build.  VSTS suit toutes modifications apportées à la définition de build et vous permet de comparer les versions.

1. Sélectionnez **Déclencheurs**.  Le projet Azure DevOps a créé automatiquement un déclencheur d’intégration continue, et chaque validation dans le référentiel crée une build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

1. Sélectionnez **Rétention**.  Selon votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

1. Sélectionnez **Build et mise en production**, puis **Versions**.  Le projet Azure DevOps a créé une définition de mise en production VSTS pour gérer les déploiements dans Azure.

1. Sur le côté gauche du navigateur, sélectionnez les **points de suspension** en regard de votre définition de mise en production, puis **Modifier**.

1. La définition de mise en production contient un **pipeline**, qui définit le processus de mise en production.  Sous **Artefacts**, sélectionnez **Déposer**.  La définition de build que vous avez examinée dans les étapes précédentes génère la sortie utilisée pour l’artefact. 

1. À droite de l’icône **Déposer**, sélectionnez le **déclencheur de déploiement continu**.  Cette définition de mise en production possède un déclencheur CD actif, qui exécute un déploiement à chaque fois qu’un nouvel artefact de build est disponible.  Si vous le souhaitez, vous pouvez désactiver le déclencheur, afin que vos déploiements nécessitent une exécution manuelle. 

1. À gauche du navigateur, sélectionnez **Tâches**.  Il s’agit des activités effectuées par votre processus de déploiement.  Dans cet exemple, une tâche a été créée pour déployer un **service Azure App**.

1. Sur le côté droit du navigateur, sélectionnez **Afficher les mises en production**.  Cette vue affiche un historique des mises en production.

1. Sélectionnez les **points de suspension** situés en regard de vos mises en production, puis **Ouvrir**.  Il y a plusieurs menus à explorer dans cette vue, comme un résumé des mises en production, les éléments de travail associés et les tests.

1. Sélectionnez **Validations**.  Cette vue présente les validations de code associées au déploiement spécifique. 

1. Sélectionnez **Journaux**.  Les journaux contiennent des informations utiles sur le processus de déploiement.  Ils peuvent être affichés pendant et après les déploiements.

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, vous pouvez supprimer le service Azure App et les ressources associées dans ce guide de démarrage rapide à l’aide de la fonction **Supprimer** sur le tableau de bord Azure DevOps Project.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la modification des définitions de mise en production et de build afin de répondre aux besoins de votre équipe, consultez ce didacticiel :

> [!div class="nextstepaction"]
> [Personnaliser le processus CD](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>vidéos

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
