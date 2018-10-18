---
title: Déployer une application ASP.NET Core sur Azure Kubernetes Service (AKS) avec Azure DevOps Projects | Tutoriel Azure DevOps
description: Azure DevOps Projects facilite la prise en main d’Azure. Azure DevOps Projects permet de déployer facilement une application ASP.NET Core avec Azure Kubernetes Service (AKS) en quelques étapes rapides.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 55ea101b3a03fdb7fc375c4594cab36d4cd79978
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299115"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-kubernetes-service-aks-with-the-azure-devops-project"></a>Didacticiel : Déployer votre application ASP.NET Core dans Azure Kubernetes Service (AKS) à l’aide d’Azure DevOps Projects

Azure DevOps Project présente une expérience simplifiée où vous apportez votre code et référentiel Git existants, ou choisissez parmi les exemples d’applications pour créer un pipeline d’intégration continue (CI) et de livraison continue (CD) vers Azure.  DevOps Projects crée automatiquement des ressources Azure comme AKS, crée et configure dans Azure DevOps Services un pipeline de build et de mise en production pour l’intégration et le développement continus (CI/CD), puis crée une ressource Azure Application Insights pour le monitoring.

Vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer un projet Azure DevOps Projects pour une application ASP.NET Core et AKS
> * Configurer Azure DevOps Services et un abonnement Azure 
> * Examiner le cluster AKS
> * Examiner le pipeline CI Azure DevOps Services
> * Examiner le pipeline CD Azure DevOps Services
> * Valider les modifications dans git et les déployer automatiquement dans Azure
> * Supprimer les ressources

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Vous pouvez en obtenir un gratuit via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-aks"></a>Créer un projet Azure DevOps Projects pour une application ASP.NET Core et AKS

Azure DevOps Projects crée un pipeline CI/CD sur Azure.  Vous pouvez créer une organisation **Azure DevOps Services** ou utiliser une **organisation existante**.  Azure DevOps Projects crée également des **ressources Azure** telles qu’un cluster AKS dans **l’abonnement Azure** de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Choisissez l’icône **Créer une ressource** dans la barre de navigation gauche, puis recherchez **DevOps Projects**.  Cliquez sur **Créer**.

    ![Démarrage de la livraison continue](_img/azure-devops-project-aks/fullbrowser.png)

1. Sélectionnez **.NET**, puis **Suivant**.

1. Pour **Choisir une infrastructure d’application**, sélectionnez **ASP.NET**, puis **Suivant**.

1. Sélectionnez **Service Kubernetes**, puis **Suivant**.  

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurer Azure DevOps Services et un abonnement Azure

1. Créez une **nouvelle** organisation Azure DevOps ou choisissez une organisation **existante**.  Choisissez un **nom** pour votre projet.  

1. Sélectionnez votre **abonnement Azure**.

1. Sélectionnez le lien **Modifier** pour afficher des paramètres de configuration Azure supplémentaires, puis déterminez le **nombre de nœuds** du **cluster Kubernetes**.  Ici, différentes options permettent de configurer le type et l’emplacement des services Azure.
 
1. Quittez la zone de configuration Azure, puis sélectionnez **Terminé**.

1. Ce processus peut prendre plusieurs minutes.  Un exemple d’application ASP.NET Core est configuré dans un référentiel git Azure Repos de votre organisation Azure DevOps Services, un cluster AKS est créé, un pipeline CI/CD est exécuté et votre application est déployée sur Azure.  

    Ensuite, le **tableau de bord** Azure DevOps Projects est chargé sur le Portail Azure.  Vous pouvez également accéder au **tableau de bord Azure DevOps Project** directement à partir de **Toutes les ressources** dans le **portail Azure**.  

    Ce tableau de bord vous permet de visualiser votre **référentiel de code** Azure Repos, votre **pipeline CI/CD Azure DevOps Services** et votre **cluster AKS**.  Vous pouvez configurer d’autres options de CI/CD dans votre pipeline Azure DevOps Services.  À droite du tableau de bord, sélectionnez **Parcourir** pour afficher votre application en cours d’exécution.

## <a name="examine-the-aks-cluster"></a>Examiner le cluster AKS

Azure DevOps Projects configure automatiquement un cluster AKS.  Vous pouvez explorer et personnaliser le cluster.  Suivez les étapes ci-dessous pour vous familiariser avec le service AKS.

1. Accédez au **tableau de bord Azure DevOps Project**.

1. Sur le côté droit du tableau de bord DevOps Projects, sélectionnez le **service Kubernetes**.

1. Un panneau s’ouvre pour le cluster AKS.  À partir de cette vue, vous pouvez effectuer diverses actions, par exemple **surveiller l’intégrité des conteneurs**, **effectuer des recherches dans les journaux** et ouvrir le **tableau de bord Kubernetes**.

1. Sur la droite de l’écran, sélectionnez **Voir le tableau de bord Kubernetes**.  Si vous le souhaitez, suivez les étapes pour ouvrir le tableau de bord Kubernetes.

## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Examiner le pipeline CI Azure DevOps Services

Azure DevOps Projects configure automatiquement un pipeline CI/CD Azure dans votre organisation Azure DevOps.  Vous pouvez explorer et personnaliser le pipeline.  Suivez les étapes ci-dessous pour vous familiariser avec le pipeline CI/CD Azure DevOps Services.

1. Accédez au **tableau de bord Azure DevOps Project**.

1. Sélectionnez **Pipelines de build** en **haut** du **tableau de bord Azure DevOps Projects**.  Ce lien ouvre un onglet de navigateur ainsi que le pipeline de build Azure DevOps Services de votre nouveau projet.

1. Déplacez le pointeur de souris à droite du pipeline de build, à côté du champ **État**. Sélectionnez les **points de suspension** affichés.  Cette action ouvre un menu permettant d’effectuer plusieurs activités, comme **mettre un nouveau build en file d’attente**, **suspendre un build** et **modifier le pipeline de build**.

1. Sélectionnez **Modifier**.

1. Dans cette vue, **examinez les différentes tâches** disponibles.  Le build effectue différentes tâches, notamment récupérer (fetch) des sources dans le référentiel git Azure DevOps Services Repos, restaurer les dépendances et publier les sorties utilisées pour les déploiements.

1. En haut du pipeline de build, sélectionnez le **nom du pipeline de build**.

1. Remplacez le **nom** de votre pipeline de build par un nom plus descriptif.  Sélectionnez **Enregistrer et mettre en file d’attente**, puis **Enregistrer**.

1. Sous le nom de votre pipeline de build, sélectionnez **Historique**.  Vous pouvez observer une piste d’audit des modifications que vous avez apportées récemment à la build.  Azure DevOps Services suit les modifications apportées au pipeline de build et permet de comparer les versions.

1. Sélectionnez **Déclencheurs**.  Azure DevOps Projects a créé automatiquement un déclencheur CI, et chaque validation dans le référentiel lance un nouveau build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

1. Sélectionnez **Rétention**.  Selon votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

## <a name="examine-the-azure-devops-services-cd-release-pipeline"></a>Examiner le pipeline de mise en production CD Azure DevOps Services

Azure DevOps Projects crée et configure automatiquement les étapes nécessaires au déploiement entre votre organisation Azure DevOps Services et votre abonnement Azure.  Ces étapes comprennent la configuration d’une connexion au service Azure pour authentifier Azure DevOps Services avec votre abonnement Azure.  L’automatisation crée également un pipeline de mise en production Azure DevOps Services, qui apporte la livraison continue à Azure.  Suivez les étapes ci-dessous pour examiner de plus près le pipeline de mise en production Azure DevOps Services.

1. Sélectionnez **Build et mise en production**, puis **Versions**.  Azure DevOps Projects a créé un pipeline de mise en production Azure DevOps Services pour gérer les déploiements sur Azure.

1. Sur le côté gauche du navigateur, sélectionnez les **points de suspension** à côté de votre pipeline de mise en production, puis choisissez **Modifier**.

1. Le pipeline de mise en production contient un **pipeline** qui définit le processus de mise en production.  Sous **Artefacts**, sélectionnez **Déposer**.  Le pipeline de build que vous avez examiné aux étapes précédentes génère la sortie utilisée pour l’artefact. 

1. À droite de l’icône **Déposer**, sélectionnez **l’icône** **Déclencheur de déploiement continu** (qui s’affiche sous la forme d’un éclair).  Ce pipeline de mise en production comporte un déclencheur CD activé.  Le déclencheur crée un déploiement à chaque fois qu’un nouvel artefact de build est disponible.  Si vous le souhaitez, vous pouvez désactiver le déclencheur afin que vos déploiements nécessitent une exécution manuelle. 

1. À droite du navigateur, sélectionnez **Afficher les mises en production**.  Cette vue affiche un historique des mises en production.

1. Sélectionnez les **points de suspension** situés en regard de vos mises en production, puis **Ouvrir**.  Il y a plusieurs menus à explorer dans cette vue, comme un **résumé des mises en production**, les **éléments de travail associés** et les **tests**.

1. Sélectionnez **Validations**.  Cette vue présente les validations de code associées au déploiement spécifique. Vous pouvez comparer les mises en production pour afficher les différences de validation entre les déploiements.

1. Sélectionnez **Journaux**.  Les journaux contiennent des informations utiles sur le processus de déploiement.  Ils peuvent être affichés pendant et après les déploiements.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Valider les modifications apportées à Azure DevOps Services et les déployer automatiquement sur Azure 

 > [!NOTE]
 > Les étapes ci-dessous permettent de tester le pipeline CI/CD en modifiant simplement le texte de votre application web.

Vous êtes maintenant prêt à collaborer avec une équipe sur votre application avec un processus d’intégration et de déploiement continus (CI/CD) qui déploie automatiquement votre dernier travail sur votre site web.  Chaque modification apportée au référentiel git Azure DevOps Services démarre un build dans Azure DevOps Services, et un pipeline CD déploie ces modifications sur Azure.  Suivez les étapes ci-dessous ou utilisez d’autres techniques pour valider les modifications apportées à votre référentiel.  Par exemple, vous pouvez **cloner** le référentiel Git dans votre outil ou votre environnement de développement intégré favori, puis envoyer (push) les modifications vers ce référentiel.

1. Sélectionnez **Code**, puis **Fichier** dans le menu Azure DevOps Services. Ensuite, accédez à votre référentiel.
1. Accédez au répertoire **Views\Home**, sélectionnez les **points de suspension** situés en regard du fichier **Index.cshtml**, puis choisissez **Modifier**.

1. Modifiez le fichier, par exemple le texte situé dans l’une des **balises div**.  En haut à droite, sélectionnez **Valider**.  Sélectionnez de nouveau **Valider** pour envoyer (push) votre modification. 

1. Au bout de quelques instants, un **build est démarré dans Azure DevOps Services**, puis une mise en production est exécutée pour déployer les modifications.  Vous pouvez surveiller **l’état du build** avec le tableau de bord DevOps Projects ou dans le navigateur avec votre organisation Azure DevOps Services.

1. Une fois la mise en production terminée, **actualisez votre application** dans le navigateur pour vérifier les modifications apportées.

## <a name="clean-up-resources"></a>Supprimer les ressources

 > [!NOTE]
 > Les étapes ci-dessous permettent de supprimer définitivement des ressources.  Utilisez uniquement cette fonctionnalité après avoir lu attentivement les invites.

Si vous effectuez des tests, vous pouvez nettoyer les ressources pour éviter de créer des frais de facturation.  Lorsque vous n’en avez plus besoin, vous pouvez supprimer le cluster Azure Kubernetes et les ressources associées créées dans ce didacticiel à l’aide de la fonctionnalité **Supprimer** du tableau de bord du projet Azure DevOps.  **Faites attention**, car la fonctionnalité de suppression détruit les données créées par Azure DevOps Projects dans Azure et Azure DevOps Services, et vous ne pourrez plus les récupérer par la suite.

1. Dans le **portail Azure**, accédez à **Azure DevOps Project**.
2. Dans la partie **supérieure droite** du tableau de bord, sélectionnez **Supprimer**.  Après avoir lu l’invite, sélectionnez **Oui** pour **supprimer définitivement** les ressources.

## <a name="next-steps"></a>Étapes suivantes

Si vous le souhaitez, vous pouvez modifier ces pipelines de build et de mise en production afin qu’ils répondent aux besoins de votre équipe. Vous pouvez également utiliser ce modèle CI/CD comme modèle pour vos autres pipelines.  Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet Azure DevOps Projects pour une application ASP.NET Core et AKS
> * Configurer Azure DevOps Services et un abonnement Azure 
> * Examiner le cluster AKS
> * Examiner le pipeline CI Azure DevOps Services
> * Examiner le pipeline CD Azure DevOps Services
> * Valider les modifications dans git et les déployer automatiquement dans Azure
> * Supprimer les ressources

Pour en savoir plus sur l’utilisation du tableau de bord Kubernetes, voir ci-dessous :

> [!div class="nextstepaction"]
> [Utiliser le tableau de bord Kubernetes](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
