---
title: Utiliser le centre de déploiement dans Azure App Service
description: Le centre de déploiement dans Azure DevOps simplifie la configuration d’un pipeline Azure DevOps robuste pour votre application.
ms.author: puagarw
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 8d1e467906b74c97c8b4f4e5c14af0814dd098f7
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67861513"
---
# <a name="deployment-center-launcher"></a>Lanceur du centre de déploiement

Le centre de déploiement dans Azure DevOps simplifie la configuration d’un pipeline DevOps robuste pour votre application. Par défaut, un pipeline DevOps est configuré pour déployer vos mises à jour d’application sur le cluster kubernetes. Vous pouvez étendre le pipeline DevOps configuré par défaut et ajouter les fonctionnalités DevOps enrichies : approbations avant le déploiement, provisionnement de ressources Azure supplémentaires, exécution de scripts, mise à niveau de votre application ou même exécution de tests de validation supplémentaires.

Ce tutoriel présente les procédures suivantes :

> [!div class="checklist"]
> * Configurer un pipeline DevOps pour déployer vos mises à jour d’application sur le cluster k8s
> * Examiner le pipeline CI
> * Examiner le pipeline CD
> * Nettoyer les ressources

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Vous pouvez en obtenir un gratuit par le biais de [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

* Un cluster Azure Kubernetes Service (AKS)

## <a name="create-aks-cluster"></a>Créer un cluster ACS

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez le bouton [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) dans le menu en haut à droite du portail Azure.

1. Pour créer le cluster AKS, exécutez les commandes suivantes.

    ```cmd
    # The below command creates Resource Group in the south india location

    az group create --name azooaks --location southindia

    # The below command creates a cluster named azookubectl with one node. 

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-k8s-cluster"></a>Déployer des mises à jour d’application sur un cluster K8s

1. Accédez au groupe de ressources créé ci-dessus.

1. Sélectionnez le cluster AKS et cliquez sous les paramètres du panneau gauche sur **Centre de déploiement (préversion)** . Cliquez sur **Prise en main**.

   ![paramètres](media/deployment-center-launcher/settings.png)

1. Choisissez l’emplacement du code, puis cliquez sur **Suivant**. Actuellement, les dépôts pris en charge sont **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** et **GitHub**. Vous pouvez suivre les étapes ci-dessous en fonction de la sélection du dépôt.

    Azure Repos est un ensemble d’outils de gestion de versions que vous pouvez utiliser pour gérer votre code. Quelle que soit la taille de votre projet logiciel, nous vous conseillons d’appliquer la gestion de versions le plus tôt possible.

    - **Azure Repos** : choisissez un dépôt parmi les dépôts existants de votre projet et de votre organisation.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub** : autorisez et sélectionnez le dépôt pour votre compte GitHub.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Nous allons analyser le dépôt et détecter votre Dockerfile. Si vous souhaitez le mettre à jour, vous pouvez modifier le numéro de port identifié.

    ![Paramètres de l’application](media/deployment-center-launcher/application-settings.png)

    Si le dépôt ne contient pas le Dockerfile, le système affiche un message vous invitant à en valider un. 

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Sélectionnez ou créez un registre de conteneurs existant, puis cliquez sur **Terminer**. Le pipeline est créé automatiquement et met une build en file d’attente dans [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Azure Pipelines est un service cloud que vous pouvez utiliser pour générer et tester automatiquement votre projet de code et le mettre à la disposition d’autres utilisateurs. Azure Pipelines combine l’intégration continue (CI) et la livraison continue (CD) afin de tester et générer de manière constante et cohérente votre code et de l’expédier vers n’importe quelle cible.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Cliquez sur le lien pour voir le pipeline en cours.

1. Vous verrez les journaux comme indiqué une fois le déploiement terminé.

    ![Journaux](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Examiner le pipeline CI

Le centre de déploiement configure automatiquement le pipeline CI/CD de votre organisation Azure DevOps. Le pipeline peut être exploré et personnalisé. 

1. Accédez au tableau de bord du centre de déploiement.  

1. Cliquez sur le numéro de build dans la liste des journaux pour voir le pipeline de build de votre projet. 

1. Cliquez sur les points de suspension (...) en haut à droite. Un menu affiche plusieurs options, comme la mise en file d’attente d’une nouvelle build, la conservation d’une build et la modification du pipeline de build. Choisissez **Modifier le pipeline**. 

1. Vous pouvez examiner les différentes tâches de votre pipeline de build dans ce volet. La build effectue différentes tâches, telles que la collecte de sources à partir du dépôt Git, la création d’une image, l’envoi (push) d’une image vers le registre de conteneurs et la publication des sorties utilisées pour les déploiements.

1. Sélectionnez le nom du pipeline de build en haut du pipeline de build.

1. Remplacez le nom de votre pipeline de build par un nom plus descriptif, sélectionnez **Enregistrer et mettre en file d’attente**, puis sélectionnez **Enregistrer**.

1. Sélectionnez **Historique** sous votre pipeline de build. Ce volet affiche une piste d’audit de vos modifications de build récentes. Azure DevOps supervise les modifications apportées au pipeline de build et vous permet de comparer les versions.

1. Choisissez **Déclencheurs**. Si vous le souhaitez, les branches peuvent être incluses ou exclues du processus d’intégration continue.

1. Choisissez **Conservation**. Vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds en fonction de votre scénario.

## <a name="examine-the-cd-pipeline"></a>Examiner le pipeline CD

Le centre de déploiement crée et configure automatiquement les étapes nécessaires entre votre organisation Azure DevOps et votre abonnement Azure. Ces étapes comprennent la configuration d’une connexion au service Azure pour authentifier votre abonnement Azure auprès d’Azure DevOps. L’automatisation crée également un pipeline de mise en production, qui fournit le déploiement continu vers Azure.

1. Choisissez **Pipelines**, puis **Mises en production**.

1. Pour modifier le pipeline de mise en production, cliquez sur **Modifier**.

1. Sélectionnez **Déposer** dans **Artefacts**. Lors des étapes précédentes, le pipeline de build que vous avez examiné génère la sortie utilisée pour l’artefact. 

1. Sélectionnez le déclencheur **Déploiement continu** à droite de l’icône **Déposer**. Ce pipeline de mise en production est doté d’un déclencheur de déploiement continu activé qui exécute un déploiement chaque fois qu’un nouvel artefact de build est disponible. Si vous le souhaitez, vous pouvez désactiver le déclencheur afin d’exiger une exécution manuelle pour vos déploiements.

1. Pour examiner toutes les tâches de votre pipeline, cliquez sur **Tâches**. La mise en production définit l’environnement Tiller, configure imagePullSecrets, installe les outils Helm et déploie les graphiques Helm sur le cluster K8s.

1. Pour voir l’historique des mises en production, cliquez sur **Afficher les mises en production**. 

1. Pour voir la synthèse, cliquez sur **Mise en production**. Cliquez sur l’une des étapes pour explorer plusieurs menus, comme une synthèse des mises en production, les éléments de travail associés et les tests. 

1. Sélectionnez **Validations**. Cette vue montre les validations de code liées à ce déploiement. Comparez les mises en production pour voir les différences de validation entre les déploiements.

1. Sélectionnez **Journaux**. Les journaux contiennent des informations utiles sur les déploiements. Vous pouvez les voir pendant et après les déploiements.

## <a name="clean-up-resources"></a>Supprimer les ressources

Vous pouvez supprimer les ressources associées que vous avez créées quand vous n’en avez plus besoin. Utilisez la fonctionnalité Supprimer du tableau de bord DevOps Projects.

## <a name="next-steps"></a>Étapes suivantes

Si vous le souhaitez, vous pouvez modifier ces pipelines de build et de mise en production afin qu’ils répondent aux besoins de votre équipe. Vous pouvez également utiliser ce modèle CI/CD comme modèle pour vos autres pipelines. Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer un pipeline DevOps pour déployer vos mises à jour d’application sur le cluster k8s
> * Examiner le pipeline CI
> * Examiner le pipeline CD
> * Nettoyer les ressources
