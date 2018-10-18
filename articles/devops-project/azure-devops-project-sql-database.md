---
title: Déployer votre application ASP.NET et Azure SQL Database à l’aide du projet Azure DevOps | Tutoriel Azure DevOps Services
description: DevOps Project facilite la prise en main d’Azure. Le projet Azure DevOps permet de déployer facilement votre application ASP.NET à l’aide d’Azure SQL Database en quelques étapes rapides.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: d9c7c94e344daee5af87ce40ddf4dcb686696ded
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297331"
---
# <a name="tutorial--deploy-your-aspnet-app-and-azure-sql-database-with-the-azure-devops-project"></a>Didacticiel : Déployer votre application ASP.NET dans Azure SQL Database à l’aide d’Azure DevOps Project

Azure DevOps Project présente une expérience simplifiée où vous apportez votre code et référentiel Git existants, ou choisissez parmi les exemples d’applications pour créer un pipeline d’intégration continue (CI) et de livraison continue (CD) vers Azure.  Le projet DevOps crée automatiquement des ressources Azure telles qu’une base de données Azure SQL Database, crée et configure un pipeline de mise en production dans Azure DevOps comprenant un pipeline de build pour CI (intégration continue), configure un pipeline de mise en production pour CD (déploiement continu), puis crée une ressource Azure Application Insights pour la surveillance.

Vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer un projet Azure DevOps pour une application ASP.NET et Azure SQL Database
> * Configurer Azure DevOps Services et un abonnement Azure 
> * Examiner le pipeline CI Azure DevOps Services
> * Examiner le pipeline CD Azure DevOps Services
> * Valider les modifications apportées à Azure DevOps Services et les déployer automatiquement dans Azure
> * Se connecter à la base de données Azure SQL Server 
> * Supprimer les ressources

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Vous pouvez en obtenir un gratuit via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app-and-azure-sql-database"></a>Créer un projet Azure DevOps pour une application ASP.NET et Azure SQL Database

Le projet Azure DevOps crée un pipeline CI/CD dans Azure.  Vous pouvez créer une nouvelle organisation **Azure DevOps Services** ou utiliser une **organisation existante**.  Azure DevOps Project crée également des **ressources Azure** telles qu’une base de données Azure SQL Database dans **l’abonnement Azure** de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Sélectionnez l’icône **Créer une ressource** dans la barre de navigation gauche, puis recherchez **Projet DevOps**.  Cliquez sur **Créer**.

    ![Démarrage de la livraison continue](_img/azure-devops-project-github/fullbrowser.png)

1. Sélectionnez **.NET**, puis **Suivant**.

1. Pour **Choisir une infrastructure d’application**, sélectionnez **ASP.NET**.

1. Sélectionnez **Ajouter une base de données**, puis **Suivant**.  

1. L’infrastructure d’application, que vous avez choisie dans les étapes précédentes, détermine le type de cible de déploiement de service Azure disponible ici.  Sélectionnez **Suivant**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurer Azure DevOps Services et un abonnement Azure

1. Créez une **nouvelle** organisation Azure DevOps Services ou choisissez une organisation **existante**.  Choisissez un **nom** pour votre projet Azure DevOps.  

1. Sélectionnez votre **abonnement Azure**.

1. Si vous le souhaitez, sélectionnez le lien **Modifier** pour afficher d’autres paramètres de configuration Azure et identifier le **nom d’utilisateur** dans la section **Détails de connexion au serveur de base de données**.  **Enregistrez** le **nom d’utilisateur** pour le réutiliser dans les étapes suivantes de ce didacticiel.
 
1. Si vous avez effectué l’étape ci-dessus, quittez la zone de configuration Azure, puis choisissez **Terminé**.  Sinon, sélectionnez simplement **Terminé**.

1. Ce processus peut prendre plusieurs minutes.  Une fois terminé, le **tableau de bord du projet** Azure DevOps est chargé dans le portail Azure.  Vous pouvez également accéder au **tableau de bord Azure DevOps Project** directement à partir de **Toutes les ressources** dans le **portail Azure**.  Sur la droite du tableau de bord, sélectionnez **Parcourir** pour afficher votre application en cours d’exécution.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Examiner le pipeline CI Azure DevOps Services

Le projet Azure DevOps configure automatiquement un pipeline CI/CD Azure complet dans votre organisation Azure DevOps Services.  Vous pouvez explorer et personnaliser le pipeline.  Suivez les étapes ci-dessous pour vous familiariser avec le pipeline de build Azure DevOps Services.

1. Accédez au **tableau de bord Azure DevOps Project**.

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

Le projet Azure DevOps crée et configure automatiquement les étapes nécessaires au déploiement à partir de votre organisation Azure DevOps Services vers votre abonnement Azure.  Ces étapes comprennent la configuration d’une connexion au service Azure pour authentifier Azure DevOps Services auprès de votre abonnement Azure.  L’automatisation crée également une définition de mise en production Azure DevOps Services. Cette mise en production apporte le déploiement continu à Azure.  Suivez les étapes ci-dessous pour examiner de plus près la définition de mise en production Azure DevOps Services.

1. Sélectionnez **Build et mise en production**, puis **Versions**.  Le projet Azure DevOps a créé un pipeline de mise en production Azure DevOps Services pour gérer les déploiements dans Azure.

1. Sur la gauche du navigateur, sélectionnez les **points de suspension** en regard de votre pipeline de mise en production, puis **Modifier**.

1. Le pipeline de mise en production contient un **pipeline**, qui définit le processus de mise en production.  Sous **Artefacts**, sélectionnez **Déposer**.  Le pipeline de build que vous avez examiné dans les étapes précédentes génère la sortie utilisée pour l’artefact. 

1. À droite de l’icône **Déposer**, sélectionnez **l’icône** **Déclencheur de déploiement continu** (qui s’affiche sous la forme d’un éclair).  Ce pipeline de mise en production comporte un déclencheur de déploiement continu activé.  Le déclencheur démarre un déploiement à chaque fois qu’un nouvel artefact de build est disponible.  Si vous le souhaitez, vous pouvez désactiver le déclencheur afin que vos déploiements nécessitent une exécution manuelle. 

1. Le projet Azure DevOps a défini un mot de passe SQL aléatoire et utilisé ce mot de passe pour le pipeline de mise en production.  À gauche du navigateur, sélectionnez **Variables**. 

1. **Effectuez cette étape uniquement si vous avez modifié le mot de passe SQL Server.**  Il existe une seule variable **Mot de passe**.  À droite de la zone de texte **Valeur**, sélectionnez l’icône de **cadenas**.  **Entrez** le nouveau mot de passe, puis sélectionnez **Enregistrer**.

1. À gauche du navigateur, sélectionnez **Tâches**, puis choisissez votre **environnement**.  

1. Les tâches correspondent aux activités effectuées par votre processus de déploiement, regroupées par **phases**.  Il existe une seule phase pour ce pipeline de mise en production.  La phase contient une tâche de **déploiement Azure App Service** et une tâche de **déploiement de base de données Azure SQL Database**.

1. Sélectionnez la tâche **Exécuter Azure SQL** et examinez les diverses propriétés utilisées pour le déploiement SQL.  Sous **Package de déploiement**, notez que la tâche utilise un **fichier SQL DACPAC**.

1. À droite du navigateur, sélectionnez **Afficher les mises en production**.  Cette vue affiche un historique des mises en production.

1. Sélectionnez les **points de suspension** situés en regard de vos mises en production, puis **Ouvrir**.  Il y a plusieurs menus à explorer dans cette vue, comme un **résumé des mises en production**, les **éléments de travail associés** et les **tests**.

1. Sélectionnez **Validations**.  Cette vue présente les validations de code associées au déploiement spécifique. Vous pouvez comparer les mises en production pour afficher les différences de validation entre les déploiements.

1. Sélectionnez **Journaux**.  Les journaux contiennent des informations utiles sur le processus de déploiement.  Ils peuvent être affichés pendant et après les déploiements.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Valider les modifications apportées à Azure DevOps Services et les déployer automatiquement dans Azure 

 > [!NOTE]
 > Les étapes ci-dessous permettent de tester le pipeline CI/CD en modifiant simplement le texte.  Vous pouvez éventuellement apporter une modification du schéma SQL Server à la table pour tester le processus de déploiement SQL.

Vous êtes maintenant prêt à collaborer avec une équipe sur votre application avec un processus d’intégration et de déploiement continus (CI/CD) qui déploie automatiquement votre dernier travail sur votre site web.  Chaque modification apportée au référentiel Git Azure DevOps Services démarre une build dans Azure DevOps Services, et un pipeline CD Azure DevOps Services exécute un déploiement dans Azure.  Suivez les étapes ci-dessous ou utilisez d’autres techniques pour valider les modifications apportées à votre référentiel.  Les modifications de code démarrent le processus CI/CD et déploient automatiquement vos nouvelles modifications dans Azure.

1. Dans le menu Azure DevOps Services, sélectionnez **Code**, puis accédez à votre référentiel.

1. Accédez au répertoire **SampleWebApplication\Views\Home**, sélectionnez les **points de suspension** situés en regard du fichier **Index.cshtml**, puis choisissez **Modifier**.

1. Modifiez le fichier, par exemple le texte situé dans l’une des **balises div**.  En haut à droite, sélectionnez **Valider**.  Sélectionnez de nouveau **Valider** pour envoyer (push) votre modification. 

1. En quelques instants, une **build est démarrée dans Azure DevOps Services**, puis une mise en production est exécutée pour déployer les modifications.  Vous pouvez surveiller **l’état de la build** avec le tableau de bord du projet DevOps ou dans le navigateur avec votre organisation Azure DevOps Services.

1. Une fois la mise en production terminée, **actualisez votre application** dans le navigateur pour vérifier les modifications apportées.

## <a name="connect-to-the-azure-sql-server-database"></a>Se connecter à la base de données Azure SQL Server

Vous devez disposer des autorisations appropriées pour vous connecter à la base de données Azure SQL Database.

1. Dans le tableau de bord Azure DevOps Project, sélectionnez la **base de données SQL Database** pour accéder à la page de gestion de la base de données SQL Database.
   
1. Sélectionnez **Définir le pare-feu du serveur**, puis **+ Ajouter une adresse IP de client**.  

1. Sélectionnez **Enregistrer**.  L’adresse IP de votre client est désormais autorisée à accéder à la **ressource Azure SQL Server**.

1. Revenez au panneau **SQL Database**. 

1. Sur le côté droit de l’écran, sélectionnez le **nom du serveur** pour accéder à la page de configuration pour **SQL Server**.

1. Sélectionnez **Réinitialiser le mot de passe**, entrez un mot de passe pour la **connexion d’administrateur SQL Server**, puis sélectionnez **Enregistrer**.  **Conservez** ce mot de passe pour le réutiliser dans les étapes suivantes de ce didacticiel.

1. Vous pouvez désormais éventuellement utiliser les outils clients tels que **SQL Server Management Studio** ou **Visual Studio** pour vous connecter à Azure SQL Server et Azure SQL Database.  Utilisez la propriété **Nom du serveur** pour vous connecter.

   Si vous n’avez pas modifié le nom d’utilisateur de la base de données lors de la configuration initiale de DevOps Project, votre nom d’utilisateur correspond à la partie locale de votre adresse e-mail.  Par exemple, si votre adresse e-mail est johndoe@microsoft.com, votre nom d’utilisateur est johndoe.

 > [!NOTE]
 > Si vous modifiez votre mot de passe pour la connexion SQL, vous devez modifier le mot de passe dans la variable du pipeline de mise en production Azure DevOps Services, comme décrit dans la section **Examiner le pipeline CD Azure DevOps Services**.

## <a name="clean-up-resources"></a>Supprimer les ressources

 > [!NOTE]
 > Les étapes ci-dessous permettent de supprimer définitivement des ressources.  Utilisez cette fonctionnalité uniquement après avoir lu attentivement les invites.

Si vous êtes en phase de test, vous pouvez nettoyer les ressources afin d’éviter une augmentation des frais de facturation.  Lorsque vous n’en avez plus besoin, vous pouvez supprimer la base de données Azure SQL Database et les ressources associées créés dans ce didacticiel à l’aide de la fonctionnalité **Supprimer** du tableau de bord Azure DevOps Project.  **Soyez prudent**, car la fonctionnalité de suppression détruit les données créées par le projet Azure DevOps dans Azure et Azure DevOps Services, et vous ne pourrez plus les récupérer par la suite.

1. Dans le **portail Azure**, accédez à **Azure DevOps Project**.
2. Dans la partie **supérieure droite** du tableau de bord, sélectionnez **Supprimer**.  Après avoir lu l’invite, sélectionnez **Oui** pour **supprimer définitivement** les ressources.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez éventuellement modifier ces pipelines de build et de mise en production afin qu’ils répondent aux besoins de votre équipe. Vous pouvez également utiliser ce modèle CI/CD en tant que modèle pour vos autres projets.  Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet Azure DevOps pour une application ASP.NET et Azure SQL Database
> * Configurer Azure DevOps Services et un abonnement Azure 
> * Examiner le pipeline CI Azure DevOps Services
> * Examiner le pipeline CD Azure DevOps Services
> * Valider les modifications apportées à Azure DevOps Services et les déployer automatiquement dans Azure
> * Se connecter à la base de données Azure SQL Server 
> * Supprimer les ressources

Pour en savoir plus sur le pipeline Azure, consultez ce tutoriel :

> [!div class="nextstepaction"]
> [Personnaliser le processus CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>vidéos

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]