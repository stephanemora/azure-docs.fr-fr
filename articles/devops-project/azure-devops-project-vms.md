---
title: Déployer votre application ASP.NET vers des machines virtuelles Azure à l’aide du projet Azure DevOps | Tutoriel Azure DevOps Services
description: DevOps Project facilite la prise en main d’Azure. Le projet Azure DevOps permet de déployer facilement votre application ASP.NET vers des machines virtuelles Azure en quelques étapes rapides.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b05e2c2c46aa9bfa8c92d3d3c5c83d018c547b9f
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299132"
---
# <a name="tutorial--deploy-your-aspnet-app-to-azure-virtual-machines-with-the-azure-devops-project"></a>Tutoriel : Déployer votre application ASP.NET vers des machines virtuelles Azure à l’aide d’Azure DevOps Project

Azure DevOps Project présente une expérience simplifiée où vous apportez votre code et référentiel Git existants, ou choisissez parmi les exemples d’applications pour créer un pipeline d’intégration continue (CI) et de livraison continue (CD) vers Azure.  Le projet DevOps crée automatiquement des ressources Azure telles qu’une nouvelle machine virtuelle Azure, crée et configure un pipeline de mise en production dans Azure DevOps comprenant un pipeline de build pour CI (intégration continue), configure un pipeline de mise en production pour CD (déploiement continu), puis crée une ressource Azure Application Insights pour la surveillance.

Vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer un projet Azure DevOps pour une application ASP.NET
> * Configurer Azure DevOps Services et un abonnement Azure 
> * Examiner le pipeline CI Azure DevOps Services
> * Examiner le pipeline CD Azure DevOps Services
> * Valider les modifications apportées à Azure DevOps Services et les déployer automatiquement dans Azure
> * Configurer la surveillance d’Azure Application Insights
> * Supprimer les ressources

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Vous pouvez en obtenir un gratuit via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app"></a>Créer un projet Azure DevOps pour une application ASP.NET

Le projet Azure DevOps crée un pipeline CI/CD dans Azure.  Vous pouvez créer une nouvelle organisation **Azure DevOps Services** ou utiliser une **organisation existante**.  Azure DevOps Project crée également des **ressources Azure** telles que des machines virtuelles dans l’**abonnement Azure** de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Sélectionnez l’icône **+ Nouveau** dans la barre de navigation gauche, puis recherchez **Projet DevOps**.  Cliquez sur **Créer**.

    ![Démarrage de la livraison continue](_img/azure-devops-project-github/fullbrowser.png)

1. Sélectionnez **.NET**, puis **Suivant**.

1. Pour **Choisir une infrastructure d’application**, sélectionnez **ASP.NET**, puis **Suivant**. 

1. L’infrastructure d’application, que vous avez choisie dans les étapes précédentes, détermine le type de cible de déploiement de service Azure disponible ici.  Sélectionnez la **machine virtuelle**, puis **Suivant**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurer Azure DevOps Services et un abonnement Azure

1. Créez une **nouvelle** organisation Azure DevOps Services ou choisissez une organisation **existante**.  Choisissez un **nom** pour votre projet Azure DevOps.  

1. Sélectionnez les **services de votre abonnement Azure**.  Vous pouvez éventuellement sélectionner le lien **Modifier**, puis entrer plus de détails de configuration telles que la modification de l’emplacement des ressources Azure.
 
1. Entrez un **nom de machine virtuelle**, un **nom d’utilisateur**, et un **mot de passe** pour votre nouvelle ressource de machine virtuelle Azure, puis sélectionnez **Terminé**.

1. La machine virtuelle Azure sera prête après quelques minutes.  Un exemple d’application ASP.NET est configuré dans un référentiel dans votre organisation Azure DevOps Services, une build et une mise en production sont exécutées, et votre application est déployée sur la nouvelle machine virtuelle Azure créée.  

    Lorsque vous avez terminé, le **tableau de bord du projet** Azure DevOps est chargé dans le portail Azure.  Vous pouvez également accéder au **tableau de bord Azure DevOps Project** directement à partir de **Toutes les ressources** dans le **portail Azure**.  

    Ce tableau de bord vous permet de visualiser votre **référentiel de code** Azure DevOps Services, votre **pipeline CI/CD Azure** et votre **application exécutée sur Azure**.    

    ![Vue du tableau de bord](_img/azure-devops-project-vms/dashboardnopreview.png)

1. Le projet Azure DevOps configure automatiquement une build d’intégration continue et un déclencheur de mise en production qui déploie automatiquement n’importe quelle modification de code dans votre référentiel.  Vous pouvez configurer des options supplémentaires dans Azure DevOps.  Sur la droite du tableau de bord, sélectionnez **Parcourir** pour afficher votre application en cours d’exécution.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Examiner le pipeline CI Azure DevOps Services
 
Le projet Azure DevOps configure automatiquement un pipeline CI/CD Azure complet dans votre organisation Azure DevOps Services.  Vous pouvez explorer et personnaliser le pipeline.  Suivez les étapes ci-dessous pour vous familiariser avec le pipeline de build Azure DevOps Services.

1. Sélectionnez **Pipelines de build** en **haut** du **tableau de bord du projet Azure DevOps**.  Ce lien ouvre un onglet de navigateur ainsi que le pipeline de build Azure DevOps Services pour votre nouveau projet.

1. Déplacez le curseur de la souris vers la droite du pipeline de build en regard du champ **État**. Sélectionnez les **points de suspension** affichés.  Cette action ouvre un menu où vous pouvez effectuer plusieurs activités, telles que **mettre une nouvelle build en file d’attente**, **suspendre une build** et **modifier un pipeline de build**.

1. Sélectionnez **Modifier**.

1. Dans cette vue, **examinez les différentes tâches** de votre pipeline de build.  La build exécute différentes tâches, telles que la récupération (fetch) de sources à partir du référentiel Git Azure DevOps Services, la restauration de dépendances et la publication de sorties utilisées pour les déploiements.

1. En haut du pipeline de build, sélectionnez le **nom du pipeline de build**.

1. Remplacez le **nom** de votre pipeline de build par un nom plus descriptif.  Sélectionnez **Enregistrer et mettre en file d’attente**, puis **Enregistrer**.

1. Sous le nom de votre pipeline de build, sélectionnez **Historique**.  Vous pouvez observer une piste d’audit des modifications que vous avez apportées récemment à la build.  Azure DevOps Services suit les modifications apportées au pipeline de build et vous permet de comparer les versions.

1. Sélectionnez **Déclencheurs**.  Le projet Azure DevOps a créé automatiquement un déclencheur d’intégration continue, et chaque validation dans le référentiel démarre une nouvelle build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

1. Sélectionnez **Rétention**.  Selon votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Examiner le pipeline CD Azure DevOps Services

Le projet Azure DevOps crée et configure automatiquement les étapes nécessaires au déploiement à partir de votre organisation Azure DevOps Services vers votre abonnement Azure.  Ces étapes comprennent la configuration d’une connexion au service Azure pour authentifier Azure DevOps Services auprès de votre abonnement Azure.  L’automatisation crée également un pipeline CD Azure DevOps Services, qui apporte le déploiement continu à la machine virtuelle Azure.  Suivez les étapes ci-dessous pour examiner de plus près le pipeline CD Azure DevOps Services.

1. Sélectionnez **Build et mise en production**, puis **Versions**.  Le projet Azure DevOps a créé un pipeline de mise en production Azure DevOps Services pour gérer les déploiements dans Azure.

1. Sur la gauche du navigateur, sélectionnez les **points de suspension** en regard de votre pipeline de mise en production, puis **Modifier**.

1. Le pipeline de mise en production contient un **pipeline**, qui définit le processus de mise en production.  Sous **Artefacts**, sélectionnez **Déposer**.  Le pipeline de build que vous avez examiné dans les étapes précédentes génère la sortie utilisée pour l’artefact. 

1. À droite de l’icône **Déposer**, sélectionnez **l’icône** **Déclencheur de déploiement continu** (qui s’affiche sous la forme d’un éclair).  Ce pipeline de mise en production comporte un déclencheur de déploiement continu activé.  Le déclencheur démarre un déploiement à chaque fois qu’un nouvel artefact de build est disponible.  Si vous le souhaitez, vous pouvez désactiver le déclencheur afin que vos déploiements nécessitent une exécution manuelle. 

1. À gauche du navigateur, sélectionnez **Tâches**, puis choisissez votre **environnement**.  

1. Les tâches correspondent aux activités exécutées par votre processus de déploiement, regroupées par **phases**.  Il existe deux **phases** pour ce pipeline de mise en production.  La première phase contient une tâche de **déploiement de groupe de ressources Azure** qui configure le déploiement de la machine virtuelle et ajoute la nouvelle machine virtuelle à un **groupe de déploiement Azure DevOps**.  Le groupe de déploiement de la machine virtuelle dans Azure DevOps gère des groupes logiques de machines **cibles de déploiement**.

1. Dans cette deuxième phase, une tâche de **gestion d’application web IIS** a été créée pour créer un site web IIS sur la machine virtuelle.  Une seconde tâche de **déploiement d’application web IIS** a été créée pour déployer le site.

1. À droite du navigateur, sélectionnez **Afficher les mises en production**.  Cette vue affiche un historique des mises en production.

1. Sélectionnez les **points de suspension** situés en regard de vos mises en production, puis **Ouvrir**.  Il y a plusieurs menus à explorer dans cette vue, comme un **résumé des mises en production**, les **éléments de travail associés** et les **tests**.

1. Sélectionnez **Validations**.  Cette vue présente les validations de code associées au déploiement spécifique. Comparez les mises en production pour afficher les différences de validation entre les déploiements.

1. Sélectionnez **Journaux**.  Les journaux contiennent des informations utiles sur le processus de déploiement.  Ils peuvent être affichés pendant et après les déploiements.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Valider les modifications apportées à Azure DevOps Services et les déployer automatiquement dans Azure 

Vous êtes maintenant prêt à collaborer avec une équipe sur votre application avec un processus d’intégration et de déploiement continus (CI/CD) qui déploie automatiquement votre dernier travail sur votre site web.  Chaque modification apportée au référentiel Git Azure DevOps Services démarre une build dans Azure DevOps Services, et un pipeline CD Azure DevOps Services exécute un déploiement sur votre machine virtuelle Azure.  Suivez les étapes ci-dessous ou utilisez d’autres techniques pour valider les modifications apportées à votre référentiel.  Les modifications de code lancent le processus d’intégration et de déploiement continus (CI/CD) et déploient automatiquement vos nouvelles modifications sur le site web IIS de la machine virtuelle Azure.

1. Dans le menu Azure DevOps Services, sélectionnez **Code**, puis accédez à votre référentiel.

1. Accédez au répertoire **Views\Home**, sélectionnez les **points de suspension** situés en regard du fichier **Index.cshtml**, puis choisissez **Modifier**.

1. Modifiez le fichier, par exemple le texte situé dans l’une des **balises div**.  En haut à droite, sélectionnez **Valider**.  Sélectionnez de nouveau **Valider** pour envoyer (push) votre modification. 

1. En quelques instants, une **build est démarrée dans Azure DevOps Services**, puis une mise en production est exécutée pour déployer les modifications.  Surveillez **l’état de la build** avec le tableau de bord du projet DevOps ou dans le navigateur avec votre organisation Azure DevOps Services.

1. Une fois la mise en production terminée, **actualisez votre application** dans le navigateur pour vérifier les modifications apportées.

## <a name="configure-azure-application-insights-monitoring"></a>Configurer la surveillance d’Azure Application Insights

Azure Application Insights vous permet d’analyser facilement les performances et l’utilisation de votre application.  Le projet Azure DevOps a configuré automatiquement une ressource Application Insights pour votre application.  Vous pouvez configurer différentes capacités d’alertes et de surveillance, si besoin.

1. Accédez au tableau de bord **Azure DevOps Project** dans le **portail Azure**.  En bas à droite du tableau de bord, choisissez le lien **Application Insights** pour votre application.

1. Le panneau **Application Insights** s’ouvre dans le portail Azure.  Cette vue contient les informations sur la surveillance de l’utilisation, des performances et de la disponibilité de votre application.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Sélectionnez **Période**, puis **Dernière heure**.  Sélectionnez **Mise à jour** pour filtrer les résultats.  Toutes les activités des 60 dernières minutes sont affichées.  Sélectionnez la **X** pour quitter la période.

1. Vous pouvez trouver **Alertes** et plusieurs autres liens utiles en haut du tableau de bord.  Sélectionnez **Alertes** puis **+ Ajouter une alerte métrique**.

1. Entrer un **nom** pour l’alerte.

1. L’alerte par défaut est pour un **temps de réponse serveur supérieur à 1 seconde**.  Sélectionnez la liste déroulante **Métrique** pour examiner les différentes métriques d’alerte.  Par exemple, vous pouvez configurer la **durée d’exécution de la requête ASP.NET** pour une application ASP.NET.  Vous pouvez aisément configurer une variété d’alertes pour améliorer les capacités de surveillance de votre application.

1. Sélectionnez la case à cocher **Notifier via e-mail les propriétaires, contributeurs et lecteurs**.  Si vous le souhaitez, vous pouvez effectuer des actions supplémentaires lorsqu’une alerte est émise en exécutant une application logique Azure.

1. Sélectionnez **OK** pour créer l’alerte.  En quelques instants, l’alerte apparaît comme étant active sur le tableau de bord.  **Quittez** la zone Alertes, et retournez au **panneau Application Insights**.

1. Sélectionnez **Disponibilité** puis **+ Ajouter un test**. 

1. Saisissez un **nom de test**, puis choisissez **Créer**.  Cela crée un test Ping basique pour vérifier la disponibilité de votre application.  Au bout de quelques instants, les résultats de test sont disponibles, et le tableau de bord Application Insights affiche le statut de disponibilité.

## <a name="clean-up-resources"></a>Supprimer les ressources

 > [!NOTE]
 > Les étapes ci-dessous permettent de supprimer définitivement des ressources.  Utilisez cette fonctionnalité uniquement après avoir lu attentivement les invites.

Si vous êtes en phase de test, vous pouvez nettoyer les ressources afin d’éviter une augmentation des frais de facturation.  Lorsque vous n’en avez plus besoin, vous pouvez supprimer la machine virtuelle Azure et les ressources associées créées dans ce tutoriel à l’aide de la fonctionnalité **Supprimer** du tableau de bord Azure DevOps Project.  **Soyez prudent**, car la fonctionnalité de suppression détruit les données créées par le projet Azure DevOps dans Azure et Azure DevOps, et vous ne pourrez plus les récupérer par la suite.

1. Dans le **portail Azure**, accédez à **Azure DevOps Project**.
2. Dans la partie **supérieure droite** du tableau de bord, sélectionnez **Supprimer**.  Après avoir lu l’invite, sélectionnez **Oui** pour **supprimer définitivement** les ressources.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez éventuellement modifier ces pipelines de build et de mise en production afin qu’ils répondent aux besoins de votre équipe. Vous pouvez également utiliser ce modèle CI/CD en tant que modèle pour vos autres projets.  Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet Azure DevOps pour une application ASP.NET
> * Configurer Azure DevOps Services et un abonnement Azure 
> * Examiner le pipeline CI Azure DevOps Services
> * Examiner le pipeline CD Azure DevOps Services
> * Valider les modifications apportées à Azure DevOps Services et les déployer automatiquement dans Azure
> * Configurer la surveillance d’Azure Application Insights
> * Supprimer les ressources

Pour en savoir plus sur le pipeline CI/CD Azure, consultez ce tutoriel :

> [!div class="nextstepaction"]
> [Personnaliser le processus CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
