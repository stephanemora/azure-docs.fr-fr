---
title: (DÉPRÉCIÉ) CI/CD avec le moteur Azure Container Service et le mode Swarm
description: Utiliser le moteur Azure Container Service avec le mode Docker Swarm, un Azure Container Registry, et Azure DevOps pour fournir en continu une application .NET Core multiconteneur
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277912"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(DÉPRÉCIÉ) Pipeline CI/CD complet pour déployer une application à plusieurs conteneurs sur Azure Container Service, avec le moteur ACS et le mode Docker Swarm par le biais d’Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Cet article s’appuie sur le document [Pipeline CI/CD complet pour déployer une application multiconteneur sur Azure Container Service avec Docker Swarm à l’aide d’Azure DevOps](container-service-docker-swarm-setup-ci-cd.md).*

De nos jours, l’un des plus grands défis lors du déploiement d’applications modernes pour le cloud est de pouvoir fournir ces applications en continu. Dans cet article, vous verrez comment implémenter un pipeline d’intégration et de déploiement continus (CI/CD) complet à l’aide du : 
* Moteur Azure Container Service avec le mode Docker Swarm
* Azure Container Registry
* Azure DevOps


![Exemple d’application MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

L’objectif est de fournir en continu cette application dans un cluster en mode Docker Swarm, à l’aide d’Azure DevOps. La figure suivante présente ce pipeline de distribution en continu :

![Exemple d’application MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Voici une brève explication de ces étapes :

1. Les modifications du code sont validées dans le référentiel de code source (ici, GitHub). 
2. GitHub déclenche une build dans Azure DevOps 
3. Azure DevOps obtient la version la plus récente des sources et crée toutes les images qui composent l’application 
4. Azure DevOps envoie chaque image à un registre Docker créé à l’aide du service de Azure Container Registry 
5. Azure DevOps déclenche une nouvelle mise en production 
6. La version exécute quelques commandes à l’aide de SSH sur le nœud principal du cluster Azure Container Service. 
7. Le mode Docker Swarm sur le cluster extrait la dernière version des images 
8. La nouvelle version de l’application est déployée à l’aide de Docker Stack 

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer ce didacticiel, vous devez effectuer les tâches suivantes :

- [Création d’un cluster du Mode Swarm dans Azure Container Service avec le moteur ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Connexion avec le cluster Swarm dans Azure Container Service](../container-service-connect.md)
- [Utilisation d’un Registre de conteneurs Azure](../../container-registry/container-registry-get-started-portal.md)
- [Faire créer une organisation Azure DevOps et un projet](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Bifurcation du référentiel GitHub sur votre compte GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> L’orchestrateur Docker Swarm d’Azure Container Service utilise Swarm autonome hérité. Pour le moment, le [mode Swarm](https://docs.docker.com/engine/swarm/) intégré (dans Docker 1.12 et supérieur) n’est pas un orchestrateur pris en charge dans Azure Container Service. Ainsi, nous utilisons le [moteur ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), un [modèle de démarrage rapide](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/) créé par la communauté, ou une solution Docker dans la [Place de marché Azure](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Étape 1 : configurer votre organisation Azure DevOps 

Dans cette section, vous configurez votre organisation Azure DevOps. Pour configurer les points de terminaison d’Azure DevOps Services, dans votre projet Azure DevOps, cliquez sur l’icône **Paramètres** dans la barre d’outils, puis sélectionnez **Services**.

![Ouvrez Point de terminaison de services](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Connecter Azure DevOps et un compte Azure

Configurez une connexion entre votre projet Azure DevOps et votre compte Azure.

1. À gauche, cliquez sur **Nouveau point de terminaison de service** > **Azure Resource Manager**.
2. Pour autoriser Azure DevOps à fonctionner avec votre compte Azure, sélectionnez votre **abonnement**, puis cliquez sur **OK**.

    ![Azure DevOps - Autoriser Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Connecter Azure DevOps et GitHub

Configurez une connexion entre votre projet Azure DevOps et votre compte GitHub.

1. À gauche, cliquez sur **Nouveau point de terminaison du service** > **GitHub**.
2. Pour autoriser Azure DevOps à fonctionner avec votre compte GitHub, cliquez sur **Autoriser**, puis suivez la procédure décrite dans la fenêtre qui s’ouvre.

    ![Azure DevOps - Autoriser GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Connectez Azure DevOps à votre cluster Azure Container Service

Les dernières étapes avant d’aborder le pipeline CI/CD consistent à configurer les connexions externes à votre cluster Docker Swarm dans Azure. 

1. Pour le cluster Docker Swarm, ajoutez un point de terminaison de type **SSH**. Ensuite, entrez les informations de connexion SSH de votre cluster Swarm (nœud principal).

    ![Azure DevOps - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Toute la configuration est à présent effectuée. Dans les étapes suivantes, vous allez créer le pipeline CI/CD qui génère et déploie l’application sur le cluster Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Étape 2 : créer le pipeline de build

Dans cette étape, vous configurez un pipeline de build pour votre projet Azure DevOps, et définissez le flux de travail de build pour vos images conteneur.

### <a name="initial-pipeline-setup"></a>Configuration du pipeline initial

1. Pour créer un pipeline de build, connectez-vous à votre projet Azure DevOps, puis cliquez sur **Build & Release** (Générer et publier). Dans la section **Définitions de build** , cliquez sur **+ Nouveau**. 

    ![Azure DevOps - Nouveau pipeline de build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Sélectionnez le **processus Vide**.

    ![Azure DevOps - Nouveau pipeline de build vide](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Cliquez ensuite sur l’onglet **Variables** et créez deux nouvelles variables : **RegistryURL** et **AgentURL**. Collez les valeurs du Registre et du DNS d’agents de cluster.

    ![Azure DevOps - Configuration des variables de build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Dans la page **Définitions de build**, ouvrez l’onglet **Déclencheurs** et configurez la build pour utiliser l’intégration continue avec la bifurcation du projet MyShop que vous avez préalablement créé. Sélectionnez ensuite **Modifications de lot**. Veillez à sélectionner *docker-linux* comme **Spécification de branche**.

    ![Azure DevOps - Configuration de dépôt de build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Enfin, cliquez sur l’onglet **Options** et définissez la file d’attente d’agent par défaut sur **Aperçu du système Linux hébergé**.

    ![Azure DevOps - Configuration de l’agent hôte](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Définition du flux de travail de la build
Les étapes suivantes définissent le flux de travail de la build. Tout d’abord, vous devez configurer la source du code. Pour ce faire, sélectionnez **GitHub** ainsi que le **référentiel** et la **branche** (docker-linux).

![Azure DevOps - Configurer le source de code](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Il existe cinq images de conteneur à générer pour l’application *MyShop*. Chaque image est générée à l’aide du fichier Docker situé dans les dossiers du projet :

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Il vous faut deux étapes Docker pour chaque image, une pour créer l’image et une autre pour transmettre l’image dans le registre de conteneurs Azure Container Registry. 

1. Pour ajouter une étape dans le flux de travail de la build, cliquez sur **+ Ajouter une étape de build** et sélectionnez **Docker**.

    ![Azure DevOps - Ajouter des étapes de build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Pour chaque image, configurez une étape qui utilise la commande `docker build`.

    ![Azure DevOps - Build Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Pour l’opération de build, sélectionnez votre registre de conteneurs Azure Container Registry, l’action **Générer une image** et le fichier Docker qui définit chaque image. Définissez le **Répertoire de travail** comme répertoire racine du fichier Docker, choisissez le **Nom de l’image**, et sélectionnez **Inclure la dernière balise**.
    
    Le nom de l’image doit être dans ce format : ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Remplacer **[NOM]** par le nom de l’image :
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Pour chaque image, configurez une seconde étape qui utilise la commande `docker push`.

    ![Azure DevOps - Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Pour l’opération de transmission de type push, sélectionnez votre registre de conteneurs Azure Container Registry, l’action **Envoyer une image**, puis entrez le **Nom de l’image** généré à l’étape précédente puis sélectionnez **Inclure la dernière balise**.

4. Après avoir configuré les étapes de build et de transmission de type push pour chacune des cinq images, ajoutez trois étapes supplémentaires dans le workflow de génération.

   ![Azure DevOps - Ajouter une tâche en ligne de commande](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Une tâche de ligne de commande qui utilise un script bash pour remplacer l’occurrence *RegistryURL* dans le fichier docker-compose.yml par la variable RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps - Mettre à jour le fichier Compose avec l’URL du Registre](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Une tâche de ligne de commande qui utilise un script bash pour remplacer l’occurrence *AgentURL* dans le fichier docker-compose.yml par la variable AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Une tâche qui supprime le fichier Compose mis à jour comme un artefact de build afin qu’il puisse être utilisé dans la mise en production. Consultez l’écran suivant pour plus de détails.

      ![Azure DevOps - Publier un artefact](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps - Publier le fichier Compose](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Cliquez sur **Enregistrer et mettre en file d’attente** pour tester le pipeline de build.

   ![Azure DevOps - Enregistrer et mettre en file d’attente](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps - Nouvelle file d’attente](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Si la **build** est correcte, cet écran doit appaître :

   ![Azure DevOps - Build réussie](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Étape 3 : créer le pipeline de mise en production

Azure DevOps vous permet de [gérer les mises en production dans les différents environnements](https://www.visualstudio.com/team-services/release-management/). Vous pouvez activer le déploiement continu pour vous assurer que votre application est déployée sur vos différents environnements (par exemple, développement, test, pré-production et production) de façon fluide. Vous pouvez créer un environnement qui représente votre cluster Docker Swarm Azure Container Service.

![Azure DevOps - Mise en production dans ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuration de la mise en production initiale

1. Pour créer un pipeline de mise en production, cliquez sur **Mises en production** >  **+ Mise en production**.

2. Pour configurer la source de l’artefact, cliquez sur **Artefacts** > **Lier une source d’artefact**. Ici, liez ce nouveau pipeline de mise en production à la build que vous avez définie à l’étape précédente. Ainsi, le fichier docker-compose.yml devient disponible dans le processus de mise en production.

    ![Azure DevOps - Artefacts de mise en production](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Pour configurer le déclencheur de la mise en production, cliquez sur **Déclencheurs** et sélectionnez **Déploiement continu**. Définissez le déclencheur sur la même source d’artefact. Ce paramètre garantit qu’une nouvelle mise en production commence lorsque la build se termine correctement.

    ![Azure DevOps - Déclencheurs de mise en production](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Pour configurer les variables de mise en production, cliquez sur **Variables** et sélectionnez **+ Variable** pour créer trois nouvelles variables à l’aide des informations du registre : **docker.username**, **docker.password** et **docker.registry**. Collez les valeurs du Registre et du DNS d’agents de cluster.

    ![Azure DevOps - Configuration de dépôt de build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Comme indiqué sur l’écran précédent, cochez la case **Verrou** dans docker.password. Ce paramètre est important et permet de limiter le mot de passe.
    >

### <a name="define-the-release-workflow"></a>Définition du flux de travail de mise en production

Le flux de travail de mise en production est composé de deux tâches que vous ajoutez.

1. Configurez une tâche pour copier en toute sécurité le fichier compose dans un dossier *deploy* sur le nœud principal Docker Swarm à l’aide de la connexion SSH que vous avez configurée précédemment. Consultez l’écran suivant pour plus de détails.
    
    Dossier source : ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps - SCP de mise en production](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Configurez une deuxième tâche pour exécuter une commande bash afin d’exécuter les commandes `docker` et `docker stack deploy` sur le nœud principal. Consultez l’écran suivant pour plus de détails.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps - Bash de mise en production](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    La commande exécutée sur le nœud principal utilise les interfaces de ligne de commande Docker et Docker-Compose pour effectuer les tâches suivantes :

   - Connexion au registre de conteneurs Azure Container Registry (il utilise trois variables de build qui sont définies sous l’onglet **Variables**)
   - définition de la variable **DOCKER_HOST** pour fonctionner avec le point de terminaison Swarm (:2375) ;
   - accès au dossier *deploy* qui a été créé par la tâche de copie sécurisée précédente et qui contient le fichier docker-compose.yml ; 
   - Exécution des commandes `docker stack deploy` qui extraient les nouvelles images et créent les conteneurs.

     >[!IMPORTANT]
     > Comme indiqué dans l’écran précédent, laissez la case **Échec sur STDERR** désactivée. Ce paramètre permet de compléter le processus de mise en production, car `docker-compose` imprime plusieurs messages de diagnostic, par exemple pour les conteneurs en cours d’arrêt ou de suppression, sur la sortie d’erreur standard. Si vous activez la case à cocher, Azure DevOps signale que des erreurs se sont produites lors de la mise en production, même si tout s’est bien passé.
     >
3. Enregistrez ce nouveau pipeline de mise en production.

## <a name="step-4-test-the-cicd-pipeline"></a>Étape 4 : Test du pipeline CI/CD

Maintenant que vous avez terminé la configuration, il est temps de tester ce nouveau pipeline CI/CD. La façon la plus simple de tester consiste à mettre à jour le code source et à valider les modifications dans votre référentiel GitHub. Quelques secondes après avoir envoyé le code, vous verrez une nouvelle build s’exécutant dans Azure DevOps. Une fois l’opération terminée avec succès, une nouvelle build est déclenchée et déployée dans la nouvelle version de l’application sur le cluster Azure Container Service.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la CI/CD avec Azure DevOps, consultez l’article [Documentation Azure Pipelines](/azure/devops/pipelines/?view=azure-devops).
* Pour en savoir plus sur le moteur ACS, consultez le [référentiel GitHub du moteur ACS](https://github.com/Azure/acs-engine).
* Pour en savoir plus sur le mode Docker Swarm, consultez la [vue d’ensemble du mode Docker Swarm](https://docs.docker.com/engine/swarm/).
