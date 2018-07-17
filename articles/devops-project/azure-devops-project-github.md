---
title: Créer un pipeline CI/CD pour votre code existant avec Azure DevOps Project | Didacticiel VSTS
description: DevOps Projects facilite la prise en main d’Azure. Il vous permet d’utiliser votre propre code et le référentiel GitHub pour lancer une application sur le service Azure de votre choix en quelques étapes rapides.
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
ms.openlocfilehash: 8c92b45cd3949e56515286c963b035e3c449835b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967345"
---
# <a name="create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>Créer un pipeline CI/CD pour votre code existant avec Azure DevOps Project

Azure DevOps Project présente une expérience simplifiée où vous apportez votre code et référentiel Git existants, ou choisissez parmi les exemples d’applications pour créer un pipeline d’intégration continue (CI) et de livraison continue (CD) vers Azure.

Vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer un projet Azure DevOps
> * Configurer l’accès à votre référentiel GitHub et choisir une infrastructure
> * Configurer VSTS et un abonnement Azure 
> * Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure
> * Examiner le pipeline VSTS CI/CD
> * Configurer la surveillance d’Azure Application Insights

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Vous pouvez en obtenir un gratuit via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Accédez à un référentiel GitHub ou Git externe qui contient .NET, Java, PHP, Node, Python ou du code web statique.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Azure DevOps Projects crée un pipeline CI/CD dans VSTS.  Vous pouvez créer un **compte VSTS** ou utiliser un **compte existant**.  Azure DevOps Project crée également des **ressources Azure** dans l’**abonnement Azure** de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Sélectionnez l’icône **+ Nouveau** dans la barre de navigation gauche, puis recherchez **Projet DevOps**.  Cliquez sur **Créer**.

    ![Démarrage de la livraison continue](_img/azure-devops-project-github/fullbrowser.png)

1. Sélectionnez **Apportez votre propre code**.  Une fois que vous avez terminé, sélectionnez **Suivant**.

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>Configurer l’accès à votre référentiel GitHub et choisir une infrastructure

1. Sélectionnez **GitHub**.  Si vous le souhaitez, vous pouvez choisir un **référentiel Git externe**.  Choisissez votre **référentiel**, ainsi que la **branche** où se trouve votre application.

1. Sélectionnez votre **infrastructure web**.  Une fois que vous avez terminé, sélectionnez **Suivant**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

1. L’infrastructure d’application, que vous avez choisie dans les étapes précédentes, détermine le type de cible de déploiement de service Azure disponible ici.  Sélectionnez le **service cible** de votre choix.  Une fois que vous avez terminé, sélectionnez **Suivant**.

## <a name="configure-vsts-and-an-azure-subscription"></a>Configurer VSTS et un abonnement Azure 

1. Créez un **nouveau** compte VSTS ou choisissez un compte **existant**.  Choisissez un **nom** pour votre projet VSTS.  Sélectionnez votre **abonnement Azure**, **l’emplacement**, puis le **nom** de votre application.  Une fois que vous avez terminé, sélectionnez **Terminé**.

    ![Entrer les informations VSTS](_img/azure-devops-project-github/vstsazureinfo.png)

1. En quelques minutes, le **tableau de bord du projet** est chargé dans le portail Azure.  Un exemple d’application est configuré dans un référentiel dans votre compte VSTS, une build est exécutée, et votre application est déployée dans Azure.  Ce tableau de bord fournit une visibilité dans votre **référentiel de code GitHub**, votre **pipeline VSTS CI/CD** et votre **application dans Azure**.  Sur le côté droit du tableau de bord, sélectionnez **Parcourir** pour afficher votre application en cours d’exécution.

    ![Vue du tableau de bord](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Le projet Azure DevOps configure automatiquement une build ainsi qu’un déclencheur de mise en production.  Votre code demeure dans votre référentiel GitHub ou Git externe.  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure 

Vous êtes maintenant prêt à collaborer avec une équipe sur votre application avec un processus CI/CD qui déploie automatiquement votre dernier travail sur votre site web.  Chaque modification apportée au référentiel GitHub démarre une build dans VSTS, et une définition de gestion des mises en production VSTS exécute un déploiement sur Azure.

1.  Effectuez une modification sur votre application, et **validez**-la dans votre référentiel GitHub.
2.  En quelques instants, une build démarre dans VSTS.  Vous pouvez surveiller l’état de la build avec le tableau de bord DevOps Project ou dans le navigateur avec votre compte VSTS.
3.  Une fois la build terminée, **actualisez votre application** dans le navigateur pour vérifier les modifications apportées.

## <a name="examine-the-vsts-cicd-pipeline"></a>Examiner le pipeline VSTS CI/CD

Azure DevOps Project a configuré automatiquement un pipeline VSTS CI/CD complet dans votre compte VSTS.  Explorez et personnalisez le pipeline selon vos besoins.  Suivez les étapes ci-dessous pour vous familiariser avec les définitions de mise en production et de build VSTS.

1. Sélectionnez **Pipelines de build** en **haut** du tableau de bord Azure DevOps Project.  Ce lien ouvre un onglet de navigateur ainsi que la définition de build VSTS pour votre nouveau projet.

1. Déplacez le curseur de souris vers la droite de la définition de build en regard du champ **État**. Sélectionnez les **points de suspension** affichés.  Cette action ouvre un menu où vous pouvez démarrer plusieurs activités, telles que mettre une nouvelle build en file d'attente, suspendre une build, et modifier une définition de build.

1. Sélectionnez **Modifier**.

1. Dans cette vue, **examinez les différentes tâches** de votre définition de build.  La build effectue différentes tâches, telles que la récupération (fetch) de sources dans le référentiel Git, la restauration de dépendances et la publication de sorties utilisées pour les déploiements.

1. En haut de la définition de build, sélectionnez le **nom de la définition de build**.

1. Modifiez le **nom** de votre définition de build pour le remplacer par un nom plus descriptif.  Sélectionnez **Enregistrer et mettre en file d’attente**, puis **Enregistrer**.

1. Sous le nom de votre définition de build, sélectionnez **Historique**.  Vous pouvez observer une piste d’audit des modifications que vous avez apportées récemment à la build.  VSTS suit toutes modifications apportées à la définition de build et vous permet de comparer les versions.

1. Sélectionnez **Déclencheurs**.  Le projet Azure DevOps a créé automatiquement un déclencheur d’intégration continue, et chaque validation dans le référentiel démarre une nouvelle build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

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

## <a name="configure-azure-application-insights-monitoring"></a>Configurer la surveillance d’Azure Application Insights

Azure Application Insights vous permet d’analyser facilement les performances et l’utilisation de votre application.  Le projet Azure DevOps a configuré automatiquement une ressource Application Insights pour votre application.  Vous pouvez configurer différentes capacités d’alertes et de surveillance, si besoin.

1. Accédez au tableau de bord **Azure DevOps Projects** dans le portail Azure.  En bas à droite du tableau de bord, choisissez le lien **Application Insights** pour votre application.

1. Le panneau **Application Insights** s’ouvre dans le portail Azure.  Cette vue contient les informations sur la surveillance de l’utilisation, des performances et de la disponibilité de votre application.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Sélectionnez **Période**, puis **Dernière heure**.  Sélectionnez **Mise à jour** pour filtrer les résultats.  Toutes les activités des 60 dernières minutes sont affichées.  Sélectionnez la **X** pour quitter la période.

1. Sélectionnez **Alertes** puis **+Ajouter une alerte métrique**.  

1. Entrer un **nom** pour l’alerte.

1. Sélectionnez la liste déroulante pour **Source Alter on**.  Choisissez votre **ressource App Service**.

1. L’alerte par défaut est pour un **temps de réponse serveur supérieur à 1 seconde**.  Sélectionnez la liste déroulante **Métrique** pour examiner les différentes métriques d’alerte.  Vous pouvez aisément configurer une variété d’alertes pour améliorer les capacités de surveillance de votre application.

1. Sélectionnez la case à cocher **Notifier via e-mail les propriétaires, contributeurs et lecteurs**.  Si vous le souhaitez, vous pouvez effectuer des actions supplémentaires lorsqu’une alerte est émise en exécutant une application logique Azure.

1. Sélectionnez **OK** pour créer l’alerte.  En quelques instants, l’alerte apparaît active sur le tableau de bord.  **Quittez** la zone Alertes, et retournez au **panneau Application Insights**.

1. Sélectionnez **Disponibilité** puis **+Ajouter un test**. 

1. Saisissez un **nom de test**, puis choisissez **Créer**.  Un test Ping basique est créé pour vérifier la disponibilité de votre application.  Au bout de quelques instants, les résultats de test sont disponibles, et le tableau de bord Application Insights affiche le statut de disponibilité.

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, vous pouvez supprimer le service Azure App et les ressources associées dans ce guide de démarrage rapide à l’aide de la fonctionnalité **Supprimer** sur le tableau de bord Azure DevOps Project.

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous avez configuré votre processus CI/CD dans ce didacticiel, une définition de mise en production et de build ont été automatiquement créées dans votre projet VSTS. Vous pouvez modifier ces définitions afin qu’elles répondent aux besoins de votre équipe. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet Azure DevOps
> * Configurer l’accès à votre référentiel GitHub et choisir une infrastructure
> * Configurer VSTS et un abonnement Azure 
> * Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure
> * Examiner le pipeline VSTS CI/CD
> * Configurer la surveillance d’Azure Application Insights

Pour en savoir plus sur le pipeline VSTS, consultez ce didacticiel :

> [!div class="nextstepaction"]
> [Personnaliser le processus CD](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
