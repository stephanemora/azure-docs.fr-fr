---
title: Créer un pipeline CI/CD pour votre code existant avec Azure DevOps Project | Tutoriel Azure DevOps Services
description: DevOps Project facilite la prise en main d’Azure. Il vous permet d’utiliser votre propre code et le référentiel GitHub pour lancer une application sur le service Azure de votre choix en quelques étapes rapides.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 4e0e28ff9ea14e42e1df7ce35bb90e8720a0d0b6
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407295"
---
# <a name="create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>Créer un pipeline CI/CD pour votre code existant avec Azure DevOps Project

Azure DevOps Project présente une expérience simplifiée où vous apportez votre code et référentiel Git existants, ou choisissez parmi les exemples d’applications pour créer un pipeline d’intégration continue (CI) et de livraison continue (CD) vers Azure.

Vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer un projet Azure DevOps
> * Configurer l’accès à votre référentiel GitHub et choisir une infrastructure
> * Configurer Azure DevOps Services et un abonnement Azure 
> * Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure
> * Examiner le pipeline CI/CD Azure DevOps Services
> * Configurer la surveillance d’Azure Application Insights

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Vous pouvez en obtenir un gratuit via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Accédez à un référentiel GitHub ou Git externe qui contient .NET, Java, PHP, Node, Python ou du code web statique.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Azure DevOps Project crée un pipeline CI/CD dans Azure DevOps Services.  Vous pouvez créer une nouvelle organisation **Azure DevOps Services** ou utiliser une **organisation existante**.  Azure DevOps Projects crée également des **ressources Azure** dans **l’abonnement Azure** de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Sélectionnez l’icône **+ Nouveau** dans la barre de navigation gauche, puis recherchez **Projet DevOps**.  Cliquez sur **Créer**.

    ![Démarrage de la livraison continue](_img/azure-devops-project-github/fullbrowser.png)

1. Sélectionnez **Apportez votre propre code**.  Une fois que vous avez terminé, sélectionnez **Suivant**.

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>Configurer l’accès à votre référentiel GitHub et choisir une infrastructure

1. Sélectionnez **GitHub**.  Si vous le souhaitez, vous pouvez choisir un **référentiel Git externe**.  Choisissez votre **référentiel**, ainsi que la **branche** où se trouve votre application.

1. Sélectionnez votre **infrastructure web**.  Une fois que vous avez terminé, sélectionnez **Suivant**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

1. L’infrastructure d’application, que vous avez choisie dans les étapes précédentes, détermine le type de cible de déploiement de service Azure disponible ici.  Sélectionnez le **service cible** de votre choix.  Une fois que vous avez terminé, sélectionnez **Suivant**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurer Azure DevOps Services et un abonnement Azure 

1. Créez une **nouvelle** organisation Azure DevOps Services ou choisissez une organisation **existante**.  Choisissez un **nom** pour votre Azure DevOps Project.  Sélectionnez votre **abonnement Azure**, **l’emplacement**, puis le **nom** de votre application.  Une fois que vous avez terminé, sélectionnez **Terminé**.

1. Après quelques minutes, le **tableau de bord Azure DevOps Project** est chargé dans le portail Azure.  Un exemple d’application est configuré dans un référentiel de votre compte Azure DevOps Services, une build est exécutée, et votre application est déployée dans Azure.  Ce tableau de bord fournit une visibilité dans votre **référentiel de code GitHub**, votre **pipeline CI/CD Azure DevOps Services** et votre **application dans Azure**.  Sur la droite du tableau de bord, sélectionnez **Parcourir** pour afficher votre application en cours d’exécution.

    ![Vue du tableau de bord](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Project configure automatiquement une build CI ainsi qu’un déclencheur de mise en production.  Votre code demeure dans votre référentiel GitHub ou Git externe.  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure 

Vous êtes maintenant prêt à collaborer avec une équipe sur votre application avec un processus d’intégration et de déploiement continus (CI/CD) qui déploie automatiquement votre dernier travail sur votre site web.  Chaque modification apportée au référentiel GitHub démarre une build dans Azure DevOps, et un pipeline CD Azure DevOps exécute un déploiement dans Azure.

1.  Effectuez une modification sur votre application, et **validez**-la dans votre référentiel GitHub.
2.  Après quelques instants, une build démarre dans Azure DevOps Services.  Vous pouvez surveiller l’état de la build avec le tableau de bord Azure DevOps Project ou dans le navigateur avec votre organisation Azure DevOps Services.
3.  Une fois la build terminée, **actualisez votre application** dans le navigateur pour vérifier les modifications apportées.

## <a name="examine-the-azure-devops-services-cicd-pipeline"></a>Examiner le pipeline CI/CD Azure DevOps Services

Azure DevOps Project a automatiquement configuré un pipeline CI/CD Azure DevOps Services dans votre organisation Azure DevOps Services.  Explorez et personnalisez le pipeline selon vos besoins.  Suivez les étapes ci-dessous pour vous familiariser avec les pipelines de build et de mise en production Azure DevOps Services.

1. Sélectionnez **Pipelines de build** en **haut** du tableau de bord Azure DevOps Project.  Ce lien ouvre un onglet de navigateur ainsi que le pipeline de build Azure DevOps Services pour votre nouveau projet.

1. Déplacez le curseur de la souris vers la droite du pipeline de build en regard du champ **État**. Sélectionnez les **points de suspension** affichés.  Cette action ouvre un menu où vous pouvez démarrer plusieurs activités, telles que mettre une nouvelle build en file d’attente, suspendre une build, et modifier un pipeline de build.

1. Sélectionnez **Modifier**.

1. Dans cette vue, **examinez les différentes tâches** de votre pipeline de build.  La build effectue différentes tâches, telles que la récupération (fetch) de sources dans le référentiel Git, la restauration de dépendances et la publication de sorties utilisées pour les déploiements.

1. En haut du pipeline de build, sélectionnez le **nom du pipeline de build**.

1. Remplacez le **nom** de votre pipeline de build par un nom plus descriptif.  Sélectionnez **Enregistrer et mettre en file d’attente**, puis **Enregistrer**.

1. Sous le nom de votre pipeline de build, sélectionnez **Historique**.  Vous pouvez observer une piste d’audit des modifications que vous avez apportées récemment à la build.  Azure DevOps Services suit les modifications apportées au pipeline de build et vous permet de comparer les versions.

1. Sélectionnez **Déclencheurs**.  Azure DevOps Project a créé automatiquement un déclencheur d’intégration continue, et chaque validation dans le référentiel démarre une nouvelle build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

1. Sélectionnez **Rétention**.  Selon votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

1. Sélectionnez **Build et mise en production**, puis **Versions**.  Azure DevOps Project a créé un pipeline de mise en production Azure DevOps Services pour gérer les déploiements dans Azure.

1. Sur la gauche du navigateur, sélectionnez les **points de suspension** en regard de votre pipeline de mise en production, puis **Modifier**.

1. Le pipeline de mise en production contient un **pipeline**, qui définit le processus de mise en production.  Sous **Artefacts**, sélectionnez **Déposer**.  Le pipeline de build que vous avez examiné dans les étapes précédentes génère la sortie utilisée pour l’artefact. 

1. À droite de l’icône **Déposer**, sélectionnez le **déclencheur de déploiement continu**.  Ce pipeline de mise en production possède un déclencheur CD actif, qui exécute un déploiement à chaque fois qu’un nouvel artefact de build est disponible.  Si vous le souhaitez, vous pouvez désactiver le déclencheur afin que vos déploiements nécessitent une exécution manuelle. 

1. À gauche du navigateur, sélectionnez **Tâches**.  Il s’agit des activités effectuées par votre processus de déploiement.  Dans cet exemple, une tâche a été créée pour déployer un **service Azure App**.

1. À droite du navigateur, sélectionnez **Afficher les mises en production**.  Cette vue affiche un historique des mises en production.

1. Sélectionnez les **points de suspension** situés en regard de vos mises en production, puis **Ouvrir**.  Il y a plusieurs menus à explorer dans cette vue, comme un résumé des mises en production, les éléments de travail associés et les tests.

1. Sélectionnez **Validations**.  Cette vue présente les validations de code associées au déploiement spécifique. 

1. Sélectionnez **Journaux**.  Les journaux contiennent des informations utiles sur le processus de déploiement.  Ils peuvent être affichés pendant et après les déploiements.

## <a name="configure-azure-application-insights-monitoring"></a>Configurer la surveillance d’Azure Application Insights

Azure Application Insights vous permet d’analyser facilement les performances et l’utilisation de votre application.  Azure DevOps Project a configuré automatiquement une ressource Application Insights pour votre application.  Vous pouvez configurer différentes capacités d’alertes et de surveillance, si besoin.

1. Accédez au tableau de bord **Azure DevOps Projects** dans le portail Azure.  En bas à droite du tableau de bord, choisissez le lien **Application Insights** pour votre application.

1. Le panneau **Application Insights** s’ouvre dans le portail Azure.  Cette vue contient les informations sur la surveillance de l’utilisation, des performances et de la disponibilité de votre application.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Sélectionnez **Période**, puis **Dernière heure**.  Sélectionnez **Mise à jour** pour filtrer les résultats.  Toutes les activités des 60 dernières minutes sont affichées.  Sélectionnez la **X** pour quitter la période.

1. Sélectionnez **Alertes** puis **+Ajouter une alerte métrique**.  

1. Entrer un **nom** pour l’alerte.

1. Sélectionnez la liste déroulante pour **Source Alter on**.  Choisissez votre **ressource App Service.
<!-- Could you please confirm if this should be "Source Alter on" instead of "Source Alert on"? -->**
1. L’alerte par défaut est pour un **temps de réponse serveur supérieur à 1 seconde**.  Sélectionnez la liste déroulante **Métrique** pour examiner les différentes métriques d’alerte.  Vous pouvez aisément configurer une variété d’alertes pour améliorer les capacités de surveillance de votre application.

1. Sélectionnez la case à cocher **Notifier via e-mail les propriétaires, contributeurs et lecteurs**.  Si vous le souhaitez, vous pouvez effectuer des actions supplémentaires lorsqu’une alerte est émise en exécutant une application logique Azure.

1. Sélectionnez **OK** pour créer l’alerte.  En quelques instants, l’alerte apparaît comme étant active sur le tableau de bord.  **Quittez** la zone Alertes, et retournez au **panneau Application Insights**.

1. Sélectionnez **Disponibilité** puis **+ Ajouter un test**. 

1. Saisissez un **nom de test**, puis choisissez **Créer**.  Un test Ping basique est créé pour vérifier la disponibilité de votre application.  Au bout de quelques instants, les résultats de test sont disponibles, et le tableau de bord Application Insights affiche le statut de disponibilité.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez supprimer le service Azure App et les ressources associées dans ce guide de démarrage rapide à l’aide de la fonctionnalité **Supprimer** sur le tableau de bord Azure DevOps Project.

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous avez configuré votre processus CI/CD dans ce tutoriel, des pipelines de mise en production et de build ont été automatiquement créés dans votre projet Azure DevOps Project. Vous pouvez modifier ces pipelines afin qu’ils répondent aux besoins de votre équipe. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet Azure DevOps
> * Configurer l’accès à votre référentiel GitHub et choisir une infrastructure
> * Configurer Azure DevOps Services et un abonnement Azure 
> * Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure
> * Examiner le pipeline CI/CD Azure DevOps Services
> * Configurer la surveillance d’Azure Application Insights

Pour en savoir que plus sur le pipeline CI/CD Azure DevOps Services, consultez ce tutoriel :

> [!div class="nextstepaction"]
> [Personnaliser le processus CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
