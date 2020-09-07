---
title: Démarrage rapide - Créer et déployer des applications sur Azure Spring Cloud
description: Décrit le déploiement d’applications sur Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8931c22c3656cf9708756153268ab1d9d87b8343
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050826"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Démarrage rapide : Créer et déployer des applications sur Azure Spring Cloud

Ce document explique comment créer et déployer des applications de microservices sur Azure Spring Cloud en utilisant :
* Azure CLI
* Plug-in Maven
* Intellij

Avant de procéder au déploiement avec Azure CLI ou Maven, effectuez les exemples qui [provisionnent une instance Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md) et qui [configurent le serveur de configuration](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Prérequis

* [Installez JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Souscrire à un abonnement Azure](https://azure.microsoft.com/free/)
* (Facultatif) [Installez Azure CLI version 2.0.67 ou ultérieure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) et installez l’extension Azure Spring Cloud avec la commande : `az extension add --name spring-cloud`
* (Facultatif) [Installez Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) et [connectez-vous](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

## <a name="deployment-procedures"></a>Procédures de déploiement

#### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Générer localement les applications de microservices

1. Clonez le dépôt des exemples d’application sur votre compte cloud Azure.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Changez de répertoire et générez le projet.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

La compilation du projet prend environ 5 minutes. Une fois l’opération terminée, vous devez avoir des fichiers JAR individuels pour chaque service dans leurs dossiers respectifs.

### <a name="create-and-deploy-the-apps"></a>Créer et déployer les applications

1. Définissez le nom du groupe de ressources et le nom du cluster par défaut en exécutant les commandes suivantes :

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Créez des microservices Azure Spring Cloud en utilisant les fichiers JAR générés à l’étape précédente. Vous allez créer trois applications : **gateway**, **auth-service** et **account-service**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Nous devons déployer les applications créées à l’étape précédente sur Azure. Utilisez les commandes suivantes pour déployer les trois applications :

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Affecter un point de terminaison public à la passerelle

Nous avons besoin d’un moyen d’accéder à l’application via un navigateur web. Notre application de passerelle a besoin d’un point de terminaison public.

1. Affectez le point de terminaison à l’aide de la commande suivante :

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. Interrogez l’application de **passerelle** pour obtenir son adresse IP publique, afin de pouvoir vérifier que l’application est en cours d’exécution :

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>Cloner et générer le dépôt d’exemples d’applications

1. Clonez le dépôt Git en exécutant la commande suivante :

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Changez d’annuaire, puis générez le projet en exécutant la commande suivante :

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Générer des configurations et déployer vers Azure Spring Cloud

1. Générez des configurations en exécutant la commande suivante dans le dossier racine de PiggyMetrics contenant le fichier POM parent. Si vous vous êtes déjà connecté avec Azure CLI, la commande sélectionne automatiquement les informations d’identification. Sinon, elle vous connecte avec des instructions d’invite. Pour plus d’informations, consultez notre [page wiki](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    Il vous sera demandé de sélectionner les éléments suivants :
    * **Modules :** Sélectionnez `gateway`,`auth-service` et `account-service`.
    * **Abonnement :** Il s’agit de votre abonnement utilisé pour créer une instance Azure Spring Cloud.
    * **Instance de service :** Il s’agit du nom de votre instance Azure Spring Cloud.
    * **Point de terminaison public :** Dans la liste des projets fournis, entrez le numéro qui correspond à `gateway`.  Ceci lui donne un accès public.

1. Le fichier POM contient désormais les dépendances et les configurations du plug-in. Déployez les applications en utilisant la commande suivante. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Importer l’exemple de projet dans IntelliJ

1. Téléchargez et décompressez le dépôt source pour ce tutoriel, ou clonez-le avec Git : `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Ouvrez la boîte de dialogue **Welcome** d’IntelliJ et sélectionnez **Import Project** pour ouvrir l’Assistant Importation.

1. Sélectionnez le dossier `piggymetric`.

    ![Importer un projet](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Déployer une application de passerelle sur Azure Spring Cloud
Pour pouvoir déployer sur Azure, vous devez vous connecter avec votre compte Azure auprès d’Azure Toolkit for IntelliJ et choisir votre abonnement. Pour plus d’informations sur la connexion, consultez [Installation et connexion](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Cliquez avec le bouton droit sur votre projet dans l’explorateur de projets IntelliJ, puis sélectionnez **Azure** -> **Deploy to Azure Spring Cloud**.

    ![Déployer sur Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. Dans le champ **Name** (Nom), l’ajout de *gateway* au **Nom** existant fait référence à la configuration.
1. Dans la zone de texte **Artifact**, sélectionnez *com.piggymetrics:gateway:1.0-SNAPSHOT*.
1. Dans la zone de texte **Subscription** (Abonnement), vérifiez votre abonnement.
1. Dans la zone de texte **Spring Cloud**, sélectionnez l’instance Azure Spring Cloud que vous avez créée dans [Provisionner une instance Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Définissez **Public Endpoint** (Point de terminaison public) sur *Enable* (Activer).
1. Dans la zone de texte **App:** , sélectionnez **Create app...** .
1. Entrez *gateway*, puis cliquez sur **OK**.

    ![Déployer sur Azure OK](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. Dans la section **Before launch** (Avant le lancement) de la boîte de dialogue, double-cliquez sur *Run Maven Goal* (Exécuter Maven Goal).
1. Dans la zone de texte **Working directory** (Répertoire de travail), accédez au dossier *piggymetrics/gateway*.
1. Dans zone de texte **Command line** (Ligne de commande), entrez *package -DskipTests*. Cliquez sur **OK**.
1. Démarrez le déploiement en cliquant sur le bouton **Run** (Exécuter) en bas de la boîte de dialogue **Deploy Azure Spring Cloud app** (Déployer l’application Azure Spring Cloud). Le plug-in va exécuter la commande `mvn package` sur l’application `gateway`, puis va déployer le fichier jar généré par la commande `package`.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Déployer les applications auth-service et account-service sur Azure Spring Cloud
Vous pouvez répéter les étapes ci-dessus pour déployer les applications `auth-service` et `account-service` sur Azure Spring Cloud :

1. Modifiez **Name** et **Artifact** de façon à identifier l’application `auth-service`.
1. Dans la zone de texte **App:** , sélectionnez **Create app...** pour créer l’application `auth-service`.
1. Vérifiez que l’option **Public Endpoint** est définie sur *Disabled* (Désactivé).
1. Dans la section **Before launch** de la boîte de dialogue, faites passer **Working directory** sur le dossier *piggymetrics/auth-service*.
1. Démarrez le déploiement en cliquant sur le bouton **Run** (Exécuter) en bas de la boîte de dialogue **Deploy Azure Spring Cloud app** (Déployer l’application Azure Spring Cloud). 
1. Répétez ces procédures pour configurer et déployer `account-service`.
---

Accédez à l’URL fournie dans la sortie des étapes précédentes pour accéder à l’application PiggyMetrics. Exemple : `https://<service instance name>-gateway.azuremicroservices.io`

![Accéder à PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Vous pouvez également accéder au Portail Azure pour trouver l’URL. 
1. Accéder au service
2. Sélectionner **Applications**
3. Sélectionner **passerelle**

    ![Naviguer dans l’application](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Recherchez l’URL dans la page **gateway | Vue d’ensemble**

    ![Naviguer dans la deuxième application](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources
Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources plus tard, supprimez le groupe de ressources du portail en exécutant la commande suivante dans Cloud Shell :
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
Dans les étapes précédentes, vous avez aussi défini le nom du groupe de ressources par défaut. Pour supprimer cette valeur par défaut, exécutez la commande suivante dans Cloud Shell :
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Journaux, métriques et suivi](spring-cloud-quickstart-logs-metrics-tracing.md)
