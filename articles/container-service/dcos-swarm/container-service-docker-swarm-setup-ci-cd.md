---
title: (DÉPRÉCIÉ) CI/CD avec Azure Container Service et Swarm
description: Utiliser Azure Container Service avec Docker Swarm, un Azure Container Registry, et Azure DevOps pour fournir en continu une application .NET Core multiconteneur
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549051"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(DÉPRÉCIÉ) Pipeline CI/CD complet pour déployer une application à plusieurs conteneurs sur Azure Container Service, avec Docker Swarm par le biais d’Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Un des plus grands défis lors du développement d’applications modernes pour le cloud est de pouvoir fournir ces applications en continu. Dans cet article, vous allez apprendre à implémenter un pipeline d’intégration et de déploiement complets et continus (CI/CD) à l’aide d’Azure Container Service avec gestion de Docker Swarm, d’Azure Container Registry et d’Azure Pipelines.


![Exemple d’application MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

L’objectif est de fournir en continu cette application dans un cluster Docker Swarm, à l’aide d’Azure DevOps Services. La figure suivante présente ce pipeline de distribution en continu :

![Exemple d’application MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Voici une brève explication de ces étapes :

1. Les modifications du code sont validées dans le référentiel de code source (ici, GitHub). 
1. GitHub déclenche une build dans Azure DevOps Services 
1. Azure DevOps Services obtient la version la plus récente des sources et crée toutes les images qui composent l’application 
1. Azure DevOps envoie chaque image à un registre Docker créé à l’aide du service de Azure Container Registry 
1. Azure DevOps Services déclenche une nouvelle mise en production 
1. La version exécute quelques commandes à l’aide de SSH sur le nœud principal du cluster Azure Container Service. 
1. Docker Swarm sur le cluster extrait la dernière version des images. 
1. La nouvelle version de l’application est déployée à l’aide de Docker Compose. 

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer ce didacticiel, vous devez effectuer les tâches suivantes :

- [Création d’un cluster Swarm dans Azure Container Service](container-service-deployment.md)
- [Connexion avec le cluster Swarm dans Azure Container Service](../container-service-connect.md)
- [Utilisation d’un Registre de conteneurs Azure](../../container-registry/container-registry-get-started-portal.md)
- [Faire créer une organisation Azure DevOps Services et un projet](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Bifurcation du référentiel GitHub sur votre compte GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Vous devez également posséder un ordinateur Ubuntu (14.04 ou 16.04) avec Docker installé. Cet ordinateur est utilisé par Azure DevOps Services pendant les processus d’Azure Pipelines. Pour créer cette machine, vous pouvez utiliser, par exemple, l’image disponible sur la Place de marché Azure. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Étape 1 : Configurer votre organisation Azure DevOps Services 

Dans cette section, vous configurez votre organisation Azure DevOps Services.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Configurer un agent de build Linux Azure DevOps Services

Pour créer des images Docker et envoyer ces images dans un Registre de conteneurs Azure à partir d’une build Azure DevOps Services, vous devez inscrire un agent Linux. Vous avez le choix entre les options d’installation suivantes :

* [Déploiement d’un agent sur Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Utiliser Docker pour exécuter l’agent Azure DevOps Services](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Installer l’extension Azure DevOps Services de l’intégration Docker

Microsoft fournit une extension Azure DevOps Services fonctionnant avec Docker dans les processus d’Azure Pipelines. Cette extension est disponible sur la [Place de marché Azure DevOps Services](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Cliquez sur **installer** pour ajouter cette extension à votre organisation Azure DevOps Services :

![Installation de l’Intégration à Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Vous êtes invité à vous connecter à votre organisation Azure DevOps Services en utilisant vos informations d’identification. 

### <a name="connect-azure-devops-services-and-github"></a>Connecter Azure DevOps Services et GitHub

Configurez une connexion entre votre projet Azure DevOps Services et votre compte GitHub.

1. Dans votre projet Azure DevOps Services, cliquez sur l’icône **Paramètres** dans la barre d’outils, puis sélectionnez **Services**.

    ![Azure DevOps Services - Connexion externe](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. À gauche, cliquez sur **Nouveau point de terminaison du service** > **GitHub**.

    ![Azure DevOps Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Pour autoriser Azure DevOps Services à fonctionner avec votre compte GitHub, cliquez sur **Autoriser**, puis suivez la procédure décrite dans la fenêtre qui s’ouvre.

    ![Azure DevOps Services - Autoriser GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Connecter Azure DevOps Services à votre registre de conteneurs Azure et à votre cluster Azure Container Service

Les dernières étapes avant d’aborder le pipeline CI/CD consistent à configurer les connexions externes à votre Registre de conteneurs et votre cluster Docker Swarm dans Azure. 

1. Dans les paramètres **Services** de votre projet Azure DevOps Services, ajoutez un point de terminaison de service de type **Registre Docker**. 

1. Dans le menu contextuel qui s’ouvre, entrez l’URL et les informations d’identification de votre Registre de conteneurs Azure.

    ![Azure DevOps Services - Registre Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Pour le cluster Docker Swarm, ajoutez un point de terminaison de type **SSH**. Ensuite, entrez les informations de connexion SSH de votre cluster Swarm.

    ![Azure DevOps Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Toute la configuration est à présent effectuée. Dans les étapes suivantes, vous allez créer le pipeline CI/CD qui génère et déploie l’application sur le cluster Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Étape 2 : Créer le pipeline de build

Dans cette étape, vous configurez un pipeline de build pour votre projet Azure DevOps Services, et définissez le flux de travail de build pour vos images conteneur

### <a name="initial-pipeline-setup"></a>Configuration du pipeline initial

1. Pour créer un pipeline de build, connectez-vous à votre projet Azure DevOps Services, puis cliquez sur **Build & Release** (Générer et publier). 

1. Dans la section **Définitions de build** , cliquez sur **+ Nouveau**. Sélectionnez le modèle **Vide**.

    ![Azure DevOps - Nouveau pipeline de build](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Configurez la nouvelle build avec une source de référentiel GitHub, cochez **Intégration continue**, puis sélectionnez la file d’attente de l’agent où vous avez enregistré votre agent Linux. Cliquez sur **Créer** pour créer le pipeline de build.

    ![Azure DevOps Services - Créer un pipeline de build](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Dans la page **Définitions de build**, ouvrez d’abord l’onglet **Référentiel** et configurez la build pour utiliser la bifurcation du projet MyShop que vous avez préalablement créé. Assurez-vous de sélectionner *acs-docs* comme **Branche par défaut**.

    ![Azure DevOps Services - Configuration de dépôt de build](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Sous l’onglet **Déclencheurs**, configurez la build devant être déclenchée après chaque validation. Sélectionnez **Intégration continue** et **Modifications par lots**.

    ![Azure DevOps Services - Configuration de déclencheur de build](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Définition du flux de travail de la build
Les étapes suivantes définissent le flux de travail de la build. Il existe cinq images de conteneur à générer pour l’application *MyShop*. Chaque image est générée à l’aide du fichier Docker situé dans les dossiers du projet :

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Vous devez ajouter deux étapes Docker pour chaque image, une pour créer l’image et une autre pour envoyer l’image dans le Registre de conteneurs Azure. 

1. Pour ajouter une étape dans le flux de travail de la build, cliquez sur **+ Ajouter une étape de build** et sélectionnez **Docker**.

    ![Azure DevOps Services - Ajouter des étapes de build](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Pour chaque image, configurez une étape qui utilise la commande `docker build`.

    ![Azure DevOps Services - Build Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Pour l’opération de génération, sélectionnez votre Registre de conteneurs Azure, l’action **Build an image** (Générer une image) et le fichier Docker qui définit chaque image. Choisissez comme **Build context** (Contexte de génération) le répertoire racine du fichier Docker et définissez le **Nom de l’image**. 
    
    Comme indiqué dans l’écran précédent, faites commencer le nom de l’image par l’URI de votre Registre de conteneurs Azure. (Vous pouvez également utiliser une variable de build pour paramétrer la balise de l’image, telle que l’identificateur de build dans cet exemple).

1. Pour chaque image, configurez une seconde étape qui utilise la commande `docker push`.

    ![Azure DevOps Services - Push Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Pour l’opération push, sélectionnez votre Registre de conteneurs Azure, l’action **Push an image** (Envoyer une image), puis entrez le **Nom de l’image** généré à l’étape précédente.

1. Après avoir configuré les étapes de build et push pour chacune des cinq images, ajoutez deux étapes supplémentaires dans le flux de travail de la build.

    a. Une tâche de ligne de commande qui utilise un script bash pour remplacer l’occurrence *BuildNumber* dans le fichier docker-compose.yml par l’ID de la build actuelle. Consultez l’écran suivant pour plus de détails.

    ![Azure DevOps Services - Mettre à jour le fichier Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Une tâche qui supprime le fichier Compose mis à jour comme un artefact de build afin qu’il puisse être utilisé dans la mise en production. Consultez l’écran suivant pour plus de détails.

    ![Azure DevOps Services - Publier le fichier Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Cliquez sur **Enregistrer** et nommez votre pipeline de build.

## <a name="step-3-create-the-release-pipeline"></a>Étape 3 : Créer le pipeline de mise en production

Azure DevOps Services vous permet de [gérer les mises en production dans les différents environnements](https://www.visualstudio.com/team-services/release-management/). Vous pouvez activer le déploiement continu pour vous assurer que votre application est déployée sur vos différents environnements (par exemple, développement, test, pré-production et production) de façon fluide. Vous pouvez créer un nouvel environnement qui représente votre cluster Docker Swarm Azure Container Service.

![Azure DevOps Services - Mise en production dans ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuration de la mise en production initiale

1. Pour créer un pipeline de mise en production, cliquez sur **Mises en production** >  **+ Mise en production**.

1. Pour configurer la source de l’artefact, cliquez sur **Artefacts** > **Lier une source d’artefact**. Ici, liez ce nouveau pipeline de mise en production à la build que vous avez définie à l’étape précédente. Ainsi, le fichier docker-compose.yml devient disponible dans le processus de mise en production.

    ![Azure DevOps Services - Artefacts de mise en production](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Pour configurer le déclencheur de la mise en production, cliquez sur **Déclencheurs** et sélectionnez **Déploiement continu**. Définissez le déclencheur sur la même source d’artefact. Ce paramètre garantit qu’une nouvelle mise en production commence dès que la build se termine correctement.

    ![Azure DevOps Services - Déclencheurs de mise en production](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Définition du flux de travail de mise en production

Le flux de travail de mise en production est composé de deux tâches que vous ajoutez.

1. Configurez une tâche pour copier en toute sécurité le fichier compose dans un dossier *deploy* sur le nœud principal Docker Swarm à l’aide de la connexion SSH que vous avez configurée précédemment. Consultez l’écran suivant pour plus de détails.

    ![Azure DevOps Services - SCP de mise en production](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Configurez une deuxième tâche pour exécuter une commande bash afin d’exécuter les commandes `docker` et `docker-compose` sur le nœud principal. Consultez l’écran suivant pour plus de détails.

    ![Azure DevOps Services - Bash de mise en production](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    La commande exécutée sur le nœud principal utilise les interfaces de ligne de commande Docker et Docker-Compose pour effectuer les tâches suivantes :

   - connexion au Registre de conteneurs Azure (il utilise trois variables de build qui sont définies sous l’onglet **Variables**) ;
   - définition de la variable **DOCKER_HOST** pour fonctionner avec le point de terminaison Swarm (:2375) ;
   - accès au dossier *deploy* qui a été créé par la tâche de copie sécurisée précédente et qui contient le fichier docker-compose.yml ; 
   - exécution des commandes `docker-compose` qui extraient les nouvelles images, arrêtent les services, suppriment les services et créent les conteneurs.

     >[!IMPORTANT]
     > Comme indiqué dans l’écran précédent, laissez la case **Échec sur STDERR** désactivée. C’est un paramètre important, car `docker-compose` imprime plusieurs messages de diagnostic, par exemple pour les conteneurs en cours d’arrêt ou de suppression, sur la sortie d’erreur standard. Si vous activez la case à cocher, Azure DevOps Services signale que des erreurs se sont produites lors de la mise en production, même si tout s’est bien passé.
     >
1. Enregistrez ce nouveau pipeline de mise en production.


>[!NOTE]
>Ce déploiement inclut des temps d’arrêt, car nous arrêtons les anciens services pour exécuter le nouveau. Il est possible d’éviter ce problème en effectuant un déploiement bleu-vert.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Étape 4. test du pipeline CI/CD

Maintenant que vous avez terminé la configuration, il est temps de tester ce nouveau pipeline CI/CD. La façon la plus simple de tester consiste à mettre à jour le code source et à valider les modifications dans votre référentiel GitHub. Quelques secondes après avoir envoyé le code, vous verrez une nouvelle build s’exécutant dans Azure DevOps Services. Une fois l’opération terminée avec succès, une nouvelle build sera déclenchée et déploiera la nouvelle version de l’application sur le cluster Azure Container Service.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la CI/CD avec Azure DevOps Services, consultez l’article [Documentation Azure Pipelines](/azure/devops/pipelines/?view=azure-devops).
