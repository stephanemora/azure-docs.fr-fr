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
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 2abe24ad65e1e8997b48a28b35ec0e65162022f2
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898013"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Didacticiel : Créer un pipeline CI/CD pour votre code existant avec Azure DevOps Projects

Azure DevOps Projects offre une expérience simplifiée dans laquelle vous pouvez apporter vos code et dépôt Git existants ou choisir un exemple d’application pour créer un pipeline d’intégration continue (CI) et de livraison continue (CD) dans Azure.

Vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Utiliser DevOps Projects pour créer un pipeline CI/CD
> * Configurer l’accès à votre dépôt GitHub et choisir un framework
> * Configurer Azure DevOps et un abonnement Azure 
> * Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure
> * Examiner le pipeline CI/CD Azure Pipelines
> * Configurer la surveillance d’Azure Application Insights
> * Supprimer des ressources

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Vous pouvez en obtenir un gratuit via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Accédez à un dépôt GitHub ou Git externe qui contient .NET, Java, PHP, Node, Python ou du code web statique.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Azure DevOps Projects crée un pipeline CI/CD dans Azure Pipelines. Vous pouvez créer une organisation Azure DevOps ou utiliser une organisation existante. Azure DevOps Projects crée également des ressources Azure dans l’abonnement Azure de votre choix.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le volet gauche, sélectionnez **Nouveau...** .

1. Dans la zone de recherche, tapez **DevOps Projects**, puis sélectionnez **Créer**.

    ![Le tableau de bord DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Sélectionnez **Apportez votre propre code**, puis sélectionnez **Suivant**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Configurer l’accès à votre dépôt GitHub et choisir un framework

1. Sélectionnez **GitHub** ou un dépôt Git externe, puis sélectionnez votre dépôt et la branche qui contient votre application.

1. Sélectionnez votre framework web, puis sélectionnez **Suivant**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    Le framework d’application, que vous avez choisi précédemment, détermine le type de cible de déploiement de service Azure disponible ici. 
    
1. Sélectionnez le service cible, puis sélectionnez **Suivant**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurer Azure DevOps et un abonnement Azure 

1. Créez une organisation Azure DevOps ou sélectionnez une organisation existante.

    a. Indiquez un nom pour votre projet Azure DevOps. 
    
    b. Sélectionnez votre abonnement et un emplacement Azure, entrez un nom pour votre application, puis sélectionnez **Terminé**.

    Après quelques minutes, le tableau de bord DevOps Projects s’affiche dans le portail Azure. Un exemple d’application est configuré dans un dépôt de votre organisation Azure DevOps, une build est exécutée et votre application est déployée sur Azure. Ce tableau de bord donne une visibilité sur votre dépôt de code GitHub, le pipeline CI/CD et votre application dans Azure. 
    
1. Sélectionnez **Parcourir** pour voir votre application en cours d’exécution.

    ![Vue du tableau de bord DevOps Projects](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Projects configure automatiquement un déclencheur de build et de mise en production CI. Votre code reste dans votre dépôt GitHub ou un autre dépôt Git externe. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure 

Vous êtes maintenant prêt à collaborer avec une équipe sur votre application, par le biais d’un processus d’intégration et de déploiement continus (CI/CD) qui déploie automatiquement votre dernier travail sur votre site web. Chaque modification apportée au dépôt GitHub démarre une build dans Azure DevOps tandis qu’un pipeline CD exécute un déploiement dans Azure.

1. Effectuez une modification sur votre application, puis validez-la dans votre dépôt GitHub.  
    Après quelques instants, une build démarre dans Azure Pipelines. Vous pouvez superviser l’état de la build dans le tableau de bord DevOps Projects, ou dans le navigateur avec votre organisation Azure DevOps.

1. Une fois la build terminée, actualisez votre application pour vérifier vos modifications.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examiner le pipeline CI/CD Azure Pipelines

Azure DevOps Projects configure automatiquement un pipeline CI/CD dans Azure Pipelines. Explorez et personnalisez le pipeline selon vos besoins. Pour vous familiariser avec les pipelines de build et de mise en production, effectuez les étapes suivantes :

1. En haut du tableau de bord DevOps Projects, sélectionnez **Pipelines de build**.  
    Un onglet du navigateur affiche le pipeline de build de votre nouveau projet.

1. Pointez sur le champ **État**, puis sélectionnez les points de suspension (...).  
    Un menu affiche plusieurs options, telles que de la mise en file d’attente d’une nouvelle build, la mise en pause d’une build et la modification du pipeline de build.

1. Sélectionnez **Modifier**.

1. Dans ce volet, vous pouvez examiner les différentes tâches de votre pipeline de build.  
    La build effectue diverses opérations, comme la récupération (fetch) de sources dans le dépôt Git, la restauration de dépendances et la publication de sorties utilisées pour les déploiements.

1. En haut du pipeline de build, sélectionnez le nom du pipeline de build.

1. Remplacez le nom de votre pipeline de build par un nom plus descriptif, sélectionnez **Enregistrer et mettre en file d’attente**, puis sélectionnez **Enregistrer**.

1. Sous le nom de votre pipeline de build, sélectionnez **Historique**.  
    Vous pouvez observer une piste d’audit des modifications que vous avez apportées récemment à la build. Azure DevOps suit les modifications apportées au pipeline de build et permet de comparer les versions.

1. Sélectionnez **Déclencheurs**.  
    Azure DevOps Projects crée automatiquement un déclencheur CI tandis que chaque validation du dépôt lance une nouvelle build. Si vous le souhaitez, vous pouvez choisir d’inclure ou d’exclure des branches dans le processus CI.

1. Sélectionnez **Rétention**.  
        En fonction de votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

1. Sélectionnez **Build et mise en production**, puis **Mises en production**.  
    Azure DevOps Projects crée un pipeline de mise en production pour gérer les déploiements sur Azure.

1. Sélectionnez les points de suspension (...) en regard de votre pipeline de mise en production, puis sélectionnez **Modifier**.  
    Le pipeline de mise en production contient un *pipeline* qui définit le processus de mise en production. 
    
1. Sous **Artefacts**, sélectionnez **Déposer**.  
    Le pipeline de build que vous avez examiné aux étapes précédentes produit la sortie qui est utilisée pour l’artefact. 

1. En regard de l’icône **Déposer**, sélectionnez **Déclencheur de déploiement continu**.  
    Ce pipeline de mise en production possède un déclencheur CD actif, qui exécute un déploiement à chaque fois qu’un nouvel artefact de build est disponible. Si vous le souhaitez, vous pouvez désactiver le déclencheur afin que vos déploiements nécessitent une exécution manuelle. 

1. À gauche, sélectionnez **Tâches**.  
    Les tâches correspondent aux activités exécutées par votre processus de déploiement. Dans cet exemple, une tâche a été créée pour déployer sur le service Azure App.

1. À droite, sélectionnez **Afficher les mises en production** pour voir l’historique des mises en production.

1. Sélectionnez les points de suspension (...) en regard d’une mise en production, puis sélectionnez **Ouvrir**.  
    Il y a plusieurs menus à explorer, comme un résumé des mises en production, les éléments de travail associés et les tests.

1. Sélectionnez **Validations**.  
    Cette vue montre les validations de code qui sont associées à ce déploiement. 

1. Sélectionnez **Journaux d’activité**.  
    Les journaux d’activité contiennent des informations utiles sur le processus de déploiement. Vous pouvez les voir à la fois pendant et après les déploiements.

## <a name="configure-azure-application-insights-monitoring"></a>Configurer la surveillance d’Azure Application Insights

Azure Application Insights vous permet d’analyser facilement les performances et l’utilisation de votre application. Azure DevOps Projects configure automatiquement une ressource Application Insights pour votre application. Vous pouvez configurer différentes capacités d’alertes et de surveillance, si besoin.

1. Dans le portail Azure, accédez au tableau de bord DevOps Projects. 

1. En bas à droite, sélectionnez le lien **Application Insights** pour votre application.  
    Le volet **Application Insights** s’ouvre. Cette vue contient les informations sur la surveillance de l’utilisation, des performances et de la disponibilité de votre application.

    ![Le volet Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Sélectionnez **Intervalle de temps**, puis **Dernière heure**. Pour filtrer les résultats, sélectionnez **Mettre à jour**.  
    Vous pouvez voir à présent toutes les activités des 60 dernières minutes. Pour quitter l’intervalle de temps, sélectionnez **x**.

1. Sélectionnez **Alertes**, puis **Ajouter une alerte Métrique**. 

1. Entrez un nom pour l’alerte.

1. Dans la liste déroulante **Alerte pour**, sélectionnez votre **ressource App Service.** <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. Dans la liste déroulante **Métrique**, examinez les différents métriques d’alerte.  
    L’alerte par défaut est pour un **temps de réponse serveur supérieur à 1 seconde**. Vous pouvez aisément configurer une variété d’alertes pour améliorer les capacités de surveillance de votre application.

1. Cochez la case **Envoyer un e-mail aux propriétaires, aux contributeurs et aux lecteurs**.  
    Si vous le souhaitez, lorsqu’une alerte est affichée, vous pouvez effectuer des actions supplémentaires en exécutant une application logique Azure.

1. Sélectionnez **OK** pour créer l’alerte.  
    Au bout de quelques instants, l’alerte apparaît comme étant active sur le tableau de bord.
    
1. Quittez la zone **Alertes** et revenez au volet **Application Insights**.

1. Sélectionnez **Disponibilité** puis **Ajouter un test**. 

1. Entrez un nom de test, puis sélectionnez **Créer**.  
    Un test Ping basique est créé pour vérifier la disponibilité de votre application. Au bout de quelques instants, les résultats de test sont disponibles, et le tableau de bord Application Insights affiche le statut de disponibilité.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand ils ne sont plus nécessaires, vous pouvez supprimer le service Azure App Service et les ressources associées que vous avez créés dans ce tutoriel. Pour ce faire, utilisez la fonctionnalité **Supprimer** du tableau de bord DevOps Projects.

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous avez configuré votre processus CI/CD dans ce tutoriel, des pipelines de mise en production et de build ont été automatiquement créés dans Azure DevOps Projects. Vous pouvez le modifier afin qu’il réponde aux besoins de votre équipe. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Utiliser DevOps Projects pour créer un pipeline CI/CD
> * Configurer l’accès à votre dépôt GitHub et choisir un framework
> * Configurer Azure DevOps et un abonnement Azure 
> * Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure
> * Examiner le pipeline CI/CD Azure Pipelines
> * Configurer la surveillance d’Azure Application Insights
> * Supprimer des ressources

Pour en savoir plus sur le pipeline CI/CD, consultez :

> [!div class="nextstepaction"]
> [Définir votre pipeline de déploiement continu (CD) multiétape](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
