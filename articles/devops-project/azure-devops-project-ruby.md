---
title: Créer un pipeline CI/CD pour Ruby on Rails avec Azure DevOps Projects | Démarrage rapide
description: DevOps Projects facilite la prise en main d’Azure. Ce composant vous aide à lancer une application web Ruby sur un service Azure en quelques étapes rapides.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
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
ms.openlocfilehash: ebc36aed11ebef56e04067616a7d65cf94367665
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405078"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-with-the-azure-devops-project"></a>Créer un pipeline CI/CD pour Ruby on Rails avec Azure DevOps Projects

Configurez l’intégration continue (CI) et la livraison continue (CD) pour votre application Ruby on Rails avec **Azure DevOps Projects**.  Le projet Azure DevOps simplifie la configuration initiale d’un pipeline de mise en production et de build Azure DevOps Services.

Si vous ne disposez pas d’un abonnement Azure, vous pouvez en obtenir un gratuitement via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Le projet Azure DevOps crée un pipeline CI/CD dans Azure DevOps Services.  Vous pouvez créer une **nouvelle organisation Azure DevOps Services** ou utiliser une **organisation existante**.  Azure DevOps Projects crée également des **ressources Azure** dans **l’abonnement Azure** de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Sélectionnez l’icône **Créer une ressource** dans la barre de navigation gauche, puis recherchez **Projet DevOps**.  Cliquez sur **Créer**.

    ![Démarrage de la livraison continue](_img/azure-devops-project-ruby/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Sélectionner un exemple d’application et le service Azure

1. Sélectionnez l’exemple d’application **Ruby**.

1. Sélectionnez l’infrastructure d’application **Ruby on Rails**.  Une fois que vous avez terminé, sélectionnez **Suivant**.

1. La cible de déploiement par défaut est **Web App sur Linux**.  Si vous le souhaitez, vous pouvez choisir Web App pour conteneurs.  L’infrastructure d’application, que vous avez choisie dans les étapes précédentes, détermine le type de cible de déploiement de service Azure disponible ici.  Sélectionnez le **service cible** de votre choix.  Une fois que vous avez terminé, sélectionnez **Suivant**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurer Azure DevOps Services et un abonnement Azure 

1. Créez gratuitement une **nouvelle** organisation Azure DevOps Services ou choisissez une organisation **existante**.  Choisissez un **nom** pour votre projet Azure DevOps.  Sélectionnez votre **abonnement Azure**, **l’emplacement**, puis le **nom** de votre application.  Une fois que vous avez terminé, sélectionnez **Terminé**.

1. En l’espace de quelques minutes, le **tableau de bord du projet Azure DevOps** est chargé dans le portail Azure.  Un exemple d’application est configuré dans un référentiel dans votre organisation Azure DevOps Services, une build est exécutée, et votre application est déployée dans Azure.  Ce tableau de bord permet de visualiser votre **référentiel de code**, votre **pipeline Azure CI/CD** et votre **application dans Azure**.  Sur la droite du tableau de bord, sélectionnez **Parcourir** pour afficher votre application en cours d’exécution.

    ![Vue du tableau de bord](_img/azure-devops-project-ruby/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Valider les modifications de code et exécuter CI/CD

Le projet Azure DevOps a créé un référentiel Git dans votre organisation Azure DevOps Services ou compte GitHub.  Suivez les étapes ci-dessous pour afficher le référentiel et modifier le code dans votre application.

1. Sur la gauche du tableau de bord du projet DevOps, sélectionnez le lien correspondant à votre branche **maîtresse**.  Ce lien ouvre une vue sur le référentiel Git récemment créé.

1. Pour afficher l’URL du clone du référentiel, sélectionnez **Clone** en haut à droite du navigateur. Vous pouvez cloner votre référentiel Git dans votre environnement de développement intégré favori.  Dans les prochaines étapes, vous allez utiliser le navigateur web pour effectuer des modifications de code directement dans la branche maîtresse et les valider.

1. Sur la gauche du navigateur, accédez au fichier **app/views/pages/home.html.erb**.

1. Sélectionnez **Modifier** et modifiez un texte.  Par exemple, modifiez le texte situé à l’intérieur d’une des balises div.

1. Choisissez **Valider** et enregistrez vos modifications.

1. Dans votre navigateur, accédez au **tableau de bord du projet Azure DevOps**.  Vous devez maintenant voir qu’une build est en cours.  Les modifications que vous venez d’effectuer sont automatiquement générées et déployées via un pipeline Azure CI/CD.

## <a name="examine-the-azure-cicd-pipeline"></a>Examiner le pipeline Azure CI/CD

Le projet Azure DevOps a configuré automatiquement un pipeline Azure CI/CD complet dans votre organisation Azure DevOps Services.  Explorez et personnalisez le pipeline selon vos besoins.  Suivez les étapes ci-dessous pour vous familiariser avec les pipelines de build et de mise en production d’Azure DevOps Services.

1. Sélectionnez **Pipelines de build** en **haut** du tableau de bord du projet Azure DevOps.  Ce lien ouvre un onglet de navigateur ainsi que le pipeline de build Azure DevOps Services pour votre nouveau projet.

1. Sélectionnez les **points de suspension**.  Cette action ouvre un menu où vous pouvez démarrer plusieurs activités, telles que mettre une nouvelle build en file d’attente, suspendre une build, et modifier un pipeline de build.

1. Sélectionnez **Modifier**.

1. Dans cette vue, **examinez les différentes tâches** de votre pipeline de build.  La build exécute différentes tâches, telles que la récupération (fetch) de sources à partir du référentiel Git, la restauration de dépendances et la publication de sorties utilisées pour les déploiements.

1. En haut du pipeline de build, sélectionnez le **nom du pipeline de build**.

1. Modifiez le **nom** de votre pipeline de build pour le remplacer par un nom plus descriptif.  Sélectionnez **Enregistrer et mettre en file d’attente**, puis **Enregistrer**.

1. Sous le nom de votre pipeline de build, sélectionnez **Historique**.  Vous pouvez observer une piste d’audit des modifications que vous avez apportées récemment à la build.  Azure DevOps Services suit les modifications apportées au pipeline de build et vous permet de comparer les versions.

1. Sélectionnez **Déclencheurs**.  Le projet Azure DevOps a créé automatiquement un déclencheur d’intégration continue, et chaque validation dans le référentiel démarre une nouvelle build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

1. Sélectionnez **Rétention**.  Selon votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

1. Sélectionnez **Build et mise en production**, puis **Versions**.  Le projet Azure DevOps a créé un pipeline de mise en production Azure DevOps Services pour gérer les déploiements dans Azure.

1. Sur la gauche du navigateur, sélectionnez les **points de suspension** en regard de votre pipeline de mise en production, puis **Modifier**.

1. Le pipeline de mise en production contient un **pipeline**, qui définit le processus de mise en production.  Sous **Artefacts**, sélectionnez **Déposer**.  Le pipeline de build que vous avez examiné dans les étapes précédentes génère la sortie utilisée pour l’artefact. 

1. À droite de l’icône **Déposer**, sélectionnez le **déclencheur de déploiement continu**.  Ce pipeline de mise en production possède un déclencheur CD actif, qui exécute un déploiement à chaque fois qu’un nouvel artefact de build est disponible.  Si vous le souhaitez, vous pouvez désactiver le déclencheur afin que vos déploiements nécessitent une exécution manuelle. 

1. À gauche du navigateur, sélectionnez **Tâches**.  Il s’agit des activités effectuées par votre processus de déploiement.  Dans cet exemple, une tâche a été créée pour déployer un **service Azure App**.

1. À droite du navigateur, sélectionnez **Afficher les mises en production**.  Cette vue affiche un historique des mises en production.

1. Sélectionnez les **points de suspension** situés en regard de vos mises en production, puis **Ouvrir**.  Il y a plusieurs menus à explorer dans cette vue, comme un résumé des mises en production, les éléments de travail associés et les tests.

1. Sélectionnez **Validations**.  Cette vue présente les validations de code associées au déploiement spécifique. 

1. Sélectionnez **Journaux**.  Les journaux contiennent des informations utiles sur le processus de déploiement.  Ils peuvent être affichés pendant et après les déploiements.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez supprimer le service Azure App et les ressources associées créées dans ce guide de démarrage rapide à l’aide de la fonction **Supprimer** du tableau de bord du projet Azure DevOps.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la modification des pipelines de mise en production et de build afin de répondre aux besoins de votre équipe, consultez ce tutoriel :

> [!div class="nextstepaction"]
> [Personnaliser le processus CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
