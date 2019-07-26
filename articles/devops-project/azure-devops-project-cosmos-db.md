---
title: 'Tutoriel : Déployer des applications node.js alimentées par Azure Cosmos DB avec Azure DevOps Projects'
description: Azure DevOps Projects facilite la prise en main d’Azure. Avec DevOps Projects, vous pouvez déployer votre application node.js alimentée par Azure Cosmos DB sur Windows Web App en quelques étapes rapides.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4310807423600b96078ee48a04a5ad6dab68cd7e
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813115"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Déployer des applications node.js alimentées par Azure Cosmos DB avec DevOps Projects

Azure DevOps Projects offre une expérience rationalisée dans laquelle vous pouvez apporter vos code et dépôt Git existants ou sélectionner un exemple d’application pour créer un pipeline d’intégration continue (CI) et de livraison continue (CD) dans Azure.

De plus, DevOps Projects :

* Crée automatiquement des ressources Azure, telles qu’Azure Cosmos DB, Application Insights, App Service et un plan App Service.

* Crée et configure un pipeline de mise en production dans Azure DevOps pour CI/CD.

Ce tutoriel présente les procédures suivantes :

> [!div class="checklist"]
> * Utiliser DevOps Projects pour déployer une application Node.js alimentée par Azure Cosmos DB
> * Configurer Azure DevOps et un abonnement Azure
> * Examiner la base de données Azure Cosmos DB
> * Examiner le pipeline CI
> * Examiner le pipeline CD
> * Valider les modifications dans Git et les déployer automatiquement dans Azure
> * Nettoyer les ressources

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Vous pouvez en obtenir un gratuit par le biais de [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Utiliser DevOps Projects pour déployer l’application Node.js

DevOps Projects crée un pipeline CI/CD dans Azure Pipelines. Vous pouvez créer une organisation Azure DevOps ou utiliser une organisation existante. DevOps Projects crée également des ressources Azure, telles qu’Azure Cosmos DB, Application Insights, App Service et un plan App Service, dans l’abonnement Azure de votre choix.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le volet gauche, sélectionnez la section **Créer une ressource**.

1. Dans la zone de recherche, tapez **DevOps Projects**, puis cliquez sur **Ajouter**.

   ![DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Sélectionnez **Node.js** comme runtime, puis cliquez sur **Suivant**. Sous **Choisir un framework d’application**, sélectionnez **Express.js**.

1. Activez la section **Ajouter une base** pour **Cosmos DB**, puis cliquez sur **Suivant**.

    ![Ajouter une base de données](_img/azure-devops-project-cosmos-db/add-database.png)

    Cosmos DB prend en charge différents frameworks d’application, tels qu’**Express.js**, **Sample Node.js app** et **Sail.js**. Dans ce tutoriel, nous allons utiliser **Express.js**.

1. Sélectionnez un service Azure pour déployer l’application. Différents services sont disponibles, comme Windows Web App, Kubernetes service et Web App pour conteneurs. Pour ce tutoriel, nous allons utiliser **Windows Web App**. Cliquez sur **Suivant**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurer Azure DevOps et l’abonnement Azure

1. Indiquez un nom pour votre projet Azure DevOps.

1. Créez une organisation Azure DevOps ou sélectionnez une organisation existante.

1. Sélectionnez votre abonnement Azure.

1. Pour voir d’autres paramètres de configuration Azure et pour identifier le niveau tarifaire et l’emplacement, cliquez sur Paramètres supplémentaires. Ce volet regroupe différentes options pour la configuration du niveau tarifaire et de l’emplacement des services Azure.

1. Quittez la zone de configuration Azure, puis sélectionnez **Terminé**.

1. Le processus est achevé au bout de quelques minutes. Un exemple d’application Node.js est configuré dans un dépôt Git de votre organisation Azure DevOps, une base de données Azure Cosmos DB, App Service, un plan App Service et Application Insights sont créés, un pipeline CI/CD est exécuté et votre application est déployée sur Azure.

   Une fois toutes ces opérations terminées, le tableau de bord Azure DevOps Projects s’affiche dans le portail Azure. Vous pouvez également accéder au tableau de bord DevOps Projects directement à partir de **Toutes les ressources** dans le portail Azure.

   Ce tableau de bord permet de visualiser votre dépôt de code Azure DevOps, votre pipeline CI/CD et votre base de données Azure Cosmos DB. Vous pouvez configurer d’autres options de CI/CD dans votre pipeline Azure DevOps. Sur la droite, sélectionnez **Azure Cosmos DB** pour l’afficher.

## <a name="examine-the-azure-cosmos-db"></a>Examiner la base de données Azure Cosmos DB

DevOps Projects configure automatiquement Cosmos DB, que vous pouvez explorer et personnaliser. Pour vous familiariser avec la base de données Cosmos DB, effectuez les étapes suivantes :

1. Accédez au tableau de bord DevOps Projects.

    ![Tableau de bord DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. Sur la droite, sélectionnez la base de données Cosmos DB. Un volet s’ouvre pour la base de données Cosmos DB. À partir de cette vue, vous pouvez effectuer diverses actions, par exemple superviser les opérations et effectuer des recherches dans les journaux.

    ![Function App](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Examiner le pipeline CI

DevOps Projects configure automatiquement un pipeline CI/CD dans votre organisation Azure DevOps. Vous pouvez explorer et personnaliser le pipeline. Pour vous familiariser avec celui-ci, procédez comme suit :

1. Accédez au tableau de bord DevOps Projects.

1. Cliquez sur le lien hypertexte sous **Build**. Un onglet du navigateur affiche le pipeline de build de votre nouveau projet.

    ![Créer](_img/azure-devops-project-cosmos-db/build.png)

1. Sélectionnez **Modifier**. Dans ce volet, vous pouvez examiner les différentes tâches de votre pipeline de build. La build effectue différentes tâches, telles que l’extraction du code source à partir du dépôt git, la génération de l’application, l’exécution de tests unitaires et la publication des sorties utilisées pour les déploiements.

1. Sélectionnez **Déclencheurs**. DevOps Projects crée automatiquement un déclencheur CI, et chaque validation dans le dépôt lance une nouvelle build. Si vous le souhaitez, vous pouvez choisir d’inclure ou d’exclure des branches dans le processus CI.

1. Sélectionnez **Rétention**. En fonction de votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

1. En haut du pipeline de build, sélectionnez le nom du pipeline de build.

1. Remplacez le nom de votre pipeline de build par un nom plus descriptif, puis sélectionnez **Enregistrer** dans le menu déroulant **Enregistrer et mettre en file d’attente**.

1. Sous le nom de votre pipeline de build, sélectionnez **Historique**. Ce volet montre une piste d’audit des modifications que vous avez apportées récemment à la build. Azure DevOps suit les modifications apportées au pipeline de build et permet de comparer les versions.

## <a name="examine-the-cd-release-pipeline"></a>Examiner le pipeline de mise en production CD

DevOps Projects crée et configure automatiquement les étapes nécessaires au déploiement entre votre organisation Azure DevOps et votre abonnement Azure. Ces étapes comprennent la configuration d’une connexion au service Azure pour authentifier Azure DevOps auprès de votre abonnement Azure. L’automatisation crée également un pipeline de mise en production, qui fournit le déploiement continu vers Azure. Pour en savoir plus sur le pipeline de mise en production, suivez ces étapes :

1. Accédez à **Pipelines | Mises en production**.

1. Cliquez sur **Modifier**.

1. Sous **Artefacts**, sélectionnez **Déposer**. Le pipeline de build que vous avez examiné aux étapes précédentes produit la sortie qui est utilisée pour l’artefact.

1. À droite de l’icône **Déposer**, sélectionnez **Déclencheur de déploiement continu**. Ce pipeline de mise en production est doté d’un déclencheur de livraison continue activé, qui exécute un déploiement chaque fois qu’un nouvel artefact de build est disponible. Si vous le souhaitez, vous pouvez désactiver le déclencheur afin que vos déploiements nécessitent une exécution manuelle.

1. À droite, sélectionnez la section **Afficher les mises en production** pour voir l’historique des mises en production.

1. Cliquez sur la mise en production pour afficher le pipeline. Cliquez sur n’importe quel environnement pour vérifier **la Synthèse, les Validations** de la mise en production et les **Éléments de travail associés**.

1. Sélectionnez **Validations**. Cette vue montre les validations de code qui sont associées à ce déploiement. Comparez les mises en production pour afficher les différences de validation entre les déploiements.

1. Sélectionnez **Afficher les journaux**. Les journaux d’activité contiennent des informations utiles sur le processus de déploiement. Vous pouvez les voir à la fois pendant et après les déploiements.

## <a name="commit-code-changes-and-execute-cicd"></a>Valider les modifications de code et exécuter CI/CD

> [!NOTE]
> La procédure suivante teste le pipeline CI/CD en apportant une modification de texte simple.

Vous êtes maintenant prêt à collaborer avec une équipe sur votre application, par le biais d’un processus d’intégration et de livraison continus (CI/CD) qui déploie automatiquement votre dernier travail sur votre Azure App Service. Chaque modification apportée au dépôt Git démarre une build dans Azure DevOps tandis qu’un pipeline CD exécute un déploiement dans Azure. Suivez la procédure décrite dans cette section, ou utilisez une autre technique pour valider les modifications dans votre dépôt. Par exemple, vous pouvez cloner le dépôt Git dans votre outil ou environnement de développement intégré favori, puis envoyer (push) les modifications vers ce dépôt.

1. Dans le menu Azure DevOps, sélectionnez **Dépôts | Fichiers**, puis accédez à votre dépôt.

1. Le dépôt contient déjà du code basé sur le langage de l’application que vous avez sélectionné lors du processus de création. Ouvrez le fichier **Application/views/index.pug**.

1. Sélectionnez **Modifier**, puis apportez une modification à la **ligne numéro 15**. Par exemple, vous pouvez la mettre à jour avec le texte **Mon premier déploiement sur Azure App Service avec Azure Cosmos DB**.

1. En haut à droite, sélectionnez **Valider**, puis **Valider** à nouveau pour transmettre votre modification.

     Au bout de quelques instants, une build est démarrée dans Azure DevOps tandis qu’une mise en production est exécutée pour déployer les modifications. Supervisez l’état de la build dans le tableau de bord DevOps Projects, ou dans le navigateur avec votre organisation Azure DevOps.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer les ressources associées que vous avez créées. Utilisez la fonctionnalité **Supprimer** du tableau de bord DevOps Projects.

## <a name="next-steps"></a>Étapes suivantes

Si vous le souhaitez, vous pouvez modifier ces pipelines de build et de mise en production afin qu’ils répondent aux besoins de votre équipe. Vous pouvez également utiliser ce modèle CI/CD comme modèle pour vos autres pipelines. Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Utiliser DevOps Projects pour déployer une application Node.js alimentée par Azure Cosmos DB
> * Configurer Azure DevOps et un abonnement Azure 
> * Examiner la base de données Azure Cosmos DB
> * Examiner le pipeline CI
> * Examiner le pipeline CD
> * Valider les modifications dans Git et les déployer automatiquement dans Azure
> * Supprimer des ressources
