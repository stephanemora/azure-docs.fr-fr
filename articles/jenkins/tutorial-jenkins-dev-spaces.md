---
title: Utilisation du plug-in Azure Dev Spaces pour Jenkins avec Azure Kubernetes Service
description: Découvrez comment utiliser le plug-in Azure Dev Spaces dans un pipeline d’intégration continue.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 281565cec5ee947781ab8ee9f62a00e01f9ababb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037035"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Tutoriel : Utilisation du plug-in Azure Dev Spaces pour Jenkins avec Azure Kubernetes Service 

Azure Dev Spaces vous permet de tester et de développer de manière itérative votre application de microservices exécutée dans AKS (Azure Kubernetes Service) sans avoir à répliquer ni à simuler de dépendances. Le plug-in Azure Dev Spaces pour Jenkins vous permet d’utiliser Dev Spaces dans votre pipeline CI/CD (intégration et livraison continues).

Ce tutoriel utilise également ACR (Azure Container Registry). ACR stocke les images, et une tâche ACR Tasks génère les artefacts Docker et Helm. Le fait d’utiliser ACR et ACR Tasks pour la génération d’artefacts vous évite d’installer des logiciels supplémentaires, tels que Docker, sur votre serveur Jenkins. 

Dans ce tutoriel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer un cluster AKS prenant en charge Azure Dev Spaces
> * Déployer une application multiservice sur AKS
> * Préparer votre serveur Jenkins
> * Utiliser le plug-in Azure Dev Spaces dans un pipeline Jenkins pour afficher un aperçu des changements de code avant de les fusionner dans le projet

Ce tutoriel suppose des connaissances intermédiaires dans les domaines suivants : services Azure de base, AKS, ACR, Azure Dev Spaces, [pipelines](https://jenkins.io/doc/book/pipeline/)/plug-ins Jenkins et GitHub. Il est également utile d’être familier avec des outils connexes tels que kubectl et Helm.

## <a name="prerequisites"></a>Prérequis

* Un compte Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Un compte GitHub. Si vous n’avez pas de compte GitHub, [créez-en un gratuitement](https://github.com/) avant de commencer.

* [Visual Studio Code](https://code.visualstudio.com/download) avec l’extension [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) installée.

* Interface [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.43 ou ultérieure installée.

* Un serveur maître Jenkins. Si vous ne disposez pas d’un serveur maître Jenkins, déployez [Jenkins](https://aka.ms/jenkins-on-azure) sur Azure en suivant les étapes décrites dans ce [guide de démarrage rapide](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Helm et kubectl doivent être installés sur le serveur Jenkins et accessibles au compte Jenkins, comme nous le verrons par la suite dans ce tutoriel.

* VS Code, VS Code Terminal ou WSL et Bash. 


## <a name="create-azure-resources"></a>Créer des ressources Azure

Dans cette section, vous allez créer des ressources Azure :

* Un groupe de ressources pour contenir toutes les ressources Azure de ce tutoriel.
* Un cluster [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) (AKS).
* Un [registre de conteneurs Azure](https://docs.microsoft.com/azure/container-registry/) (ACR) pour générer (avec ACR Tasks) et stocker des images Docker.

1. Créez un groupe de ressources.

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. Créez un cluster AKS. Créez le cluster AKS dans une [région qui prend en charge Dev Spaces](../dev-spaces/about.md#supported-regions-and-configurations).

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Configurez ACS en vue d’utiliser Dev Spaces.

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Cette étape installe l’extension CLI `azds`.

4. Créez un registre de conteneurs.

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Déployer des exemples d’applications sur le cluster AKS

Dans cette section, vous allez configurer un espace de développement et déployer un exemple d’application sur le cluster AKS créé dans la section précédente. L’application comprend deux parties : *webfrontend* et *mywebapi*. Les deux composants sont déployés dans un espace de développement. Plus loin dans ce tutoriel, vous soumettrez une demande de tirage (pull request) sur mywebapi pour déclencher le pipeline CI dans Jenkins.

Pour plus d’informations sur l’utilisation d’Azure Dev Spaces et du développement multiservice avec Azure Dev Spaces, consultez [Bien démarrer avec l’utilisation d’Azure Dev Spaces conjointement à Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java) et [Développement multiservice avec Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Ces tutoriels fournissent des informations générales supplémentaires qui ne sont pas incluses ici.

1. Téléchargez le dépôt https://github.com/Azure/dev-spaces à partir de GitHub.

2. Ouvrez le dossier `samples/java/getting-started/webfrontend` dans VS Code. (Vous pouvez ignorer les invites par défaut qui vous demandent d’ajouter des ressources de débogage ou de restaurer le projet.)

3. Mettez à jour `/src/main/java/com/ms/sample/webfrontend/Application.java` pour qu’il se présente comme suit :

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Cliquez sur **Affichage**, puis sur **Terminal** pour ouvrir le terminal intégré dans VS Code.

5. Exécutez la commande `azds prep` pour préparer votre application afin qu’elle s’exécute dans un espace de développement. Exécutez cette commande à partir de `dev-spaces/samples/java/getting-started/webfrontend` pour préparer votre application correctement :

    ```bash
    azds prep --public
    ```

    La commande `azds prep` de l’interface CLI de Dev Spaces génère des ressources Docker et Kubernetes avec les paramètres par défaut. Ces fichiers, qui persistent pendant la durée de vie du projet, peuvent être personnalisés :

    * `./Dockerfile` et `./Dockerfile.develop` décrivent l’image conteneur de l’application ainsi que la façon dont le code source est généré et s’exécute dans le conteneur.
    * Un [graphique Helm](https://helm.sh/docs/topics/charts/) sous `./charts/webfrontend` décrit la façon dont le conteneur est déployé dans Kubernetes.
    * `./azds.yaml` est le fichier de configuration Azure Dev Spaces.

    Pour plus d’informations, consultez [Fonctionnement et configuration d’Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Générez et exécutez l’application dans AKS à l’aide de la commande `azds up` :

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Analysez la sortie de la console pour y rechercher des informations concernant l’URL créée par la commande `up`. Ces informations se présentent sous la forme suivante :

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Ouvrez cette URL dans une fenêtre de navigateur. Vous devriez alors voir l’application web. Lorsque le conteneur s’exécute, les sorties `stdout` et `stderr` sont transmises à la fenêtre de terminal.

8. Ensuite, configurez et déployez *mywebapi* :

    1. Remplacez le répertoire par `dev-spaces/samples/java/getting-started/mywebapi`.

    2. Exécuter

        ```bash
        azds prep
        ```

    3. Exécuter

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Préparer le serveur Jenkins

Dans cette section, vous allez préparer le serveur Jenkins afin qu’il exécute l’exemple de pipeline CI.

* Installer les plug-ins
* Installer l’interface CLI de Helm et Kubernetes
* Ajouter les informations d’identification

### <a name="install-plug-ins"></a>Installer les plug-ins

1. Connectez-vous à votre serveur Jenkins. Choisissez**Manage Jenkins > Manage Plugins** (Gérer Jenkins > Gérer les plug-ins).
2. Sous l’onglet **Disponibles**, sélectionnez les plug-ins suivants :
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry Tasks](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Environment Injector](https://plugins.jenkins.io/envinject)
    * [GitHub Integration](https://plugins.jenkins.io/github-pullrequest)

    Si ces plug-ins ne sont pas listés, examinez l’onglet **Installés** pour voir s’ils sont déjà installés.

3. Pour installer les plug-ins, choisissez **Télécharger maintenant et installer après le redémarrage**.

4. Redémarrez votre serveur Jenkins pour terminer l’installation.

### <a name="install-helm-and-kubectl"></a>Installer Helm et kubectl

L’exemple de pipeline utilise Helm et kubectl pour le déploiement sur l’espace de développement. Quand Jenkins est installé, il crée un compte administrateur nommé *jenkins*. L’utilisateur jenkins doit pouvoir accéder à Helm et à kubectl.

1. Établissez une connexion SSH au serveur maître Jenkins. 

2. Passez à l’utilisateur `jenkins` :
    ```bash
    sudo su jenkins
    ```

3. Installez l’interface CLI de Helm. Pour en savoir plus, consultez [Installation de Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Installez kubectl. Pour plus d’informations, consultez [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Ajout d’informations d’identification à Jenkins

1. Jenkins a besoin d’un principal de service Azure pour authentifier les ressources Azure et y accéder. Pour créer le principal de service, reportez-vous à la section  [Créer le principal du service](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal)  du tutoriel Déployer dans Azure App Service. Veillez à enregistrer une copie de la sortie de `create-for-rbac`, car vous en aurez besoin à l’étape suivante. Vous obtenez un résultat semblable à ce qui suit :

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Ajoutez un type d’informations d’identification *Microsoft Azure Service Principal* (Principal de service Microsoft Azure) dans Jenkins en utilisant les informations de principal de service de l’étape précédente. Les noms dans la capture d’écran ci-dessous correspondent à la sortie de `create-for-rbac`.

    Le champ **ID** est le nom des informations de connexion Jenkins pour votre principal de service. L’exemple utilise la valeur de `displayName` (dans ce cas, `xxxxxxxjenkinssp`), mais vous pouvez utiliser n’importe quel texte. Le nom des informations de connexion est la valeur de la variable d’environnement AZURE_CRED_ID dans la section suivante.

    ![Ajouter les informations d’identification du principal de service à Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    La **description** est facultative. Pour obtenir des instructions plus détaillées, consultez la section [Ajouter un principal de service dans Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins)  du tutoriel Déployer dans Azure App Service. 



3. Pour afficher vos informations d’identification ACR, exécutez cette commande :

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    Effectuez une copie de la sortie JSON, qui doit ressembler à ceci :

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Ajoutez un type d’informations d’identification *Username with password* (Nom d’utilisateur avec mot de passe) dans Jenkins. **username** est le nom d’utilisateur provenant de la dernière étape (dans cet exemple, `acr01`). **password** est la valeur du premier mot de passe (dans cet exemple, `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`). L’**ID** de ces informations d’identification est la valeur de ACR_CRED_ID.

5. Configurez des informations d’identification AKS. Ajoutez un type d’informations d’identification *Kubernetes configuration (kubeconfig)* dans Jenkins. Utilisez pour cela l’option « Enter directly » (Entrer directement). Pour obtenir les informations d’identification d’accès pour votre cluster AKS, exécutez la commande suivante :

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   L’**ID** est la valeur de KUBE_CONFIG_ID dans la section suivante.

## <a name="create-a-pipeline"></a>Créer un pipeline

Le scénario sélectionné pour l’exemple de pipeline est basé sur un modèle réel : Une demande de tirage (pull request) déclenche un pipeline CI qui génère les changements proposés, puis les déploie sur un espace de développement Azure à des fins de test et de révision. Selon le résultat de la révision, les changements sont soit fusionnés et déployés sur AKS, soit ignorés. Enfin, l’espace de développement est supprimé.

La configuration du pipeline Jenkins et Jenkinsfile définissent les étapes dans le pipeline CI. Cet organigramme présente les étapes du pipeline et les points de décision définis par Jenkinsfile :

![Flux du pipeline Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Téléchargez une version modifiée du projet *mywebapi* à l’adresse [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi). Ce projet contient plusieurs fichiers nécessaires pour créer un pipeline, notamment *Jenkinsfile*, *Dockerfiles* et le graphique Helm.

2. Connectez-vous à Jenkins. Dans le menu de gauche, sélectionnez **Add Item** (Ajouter un élément).

3. Sélectionnez **Pipeline**, puis entrez un nom dans la zone**Enter an item name** (Entrer un nom d’élément). Sélectionnez **OK**. L’écran de configuration du pipeline s’ouvre automatiquement.

4. Sous l’onglet **General**, cochez **Prepare an environment for the run** (Préparer un environnement pour l’exécution). 

5. Cochez **Keep Jenkins Environment Variables** (Conserver les variables d’environnement Jenkins) et **Keep Jenkins Build Variables** (Conserver les variables de build Jenkins).

6. Dans la zone **Properties Content** (Contenu des propriétés), entrez les variables d’environnement suivantes :

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Compte tenu des exemples de valeurs données dans les sections précédentes, la liste des variables d’environnement doit ressembler à ceci :

    ![Variables d’environnement du pipeline Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Choisissez **Pipeline script from SCM** (Script de pipeline à partir de SCM) dans **Pipeline > Definition**.
8. Dans **SCM**, choisissez **Git**, puis entrez l’URL du dépôt.
9. Dans **Branch Specifier** (Spécificateur de branche), entrez `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Indiquez l’URL du dépôt SCM et le chemin de script « Jenkinsfile ».
11. Cochez **Lightweight checkout** (Validation légère).

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Créer une demande de tirage (pull request) pour déclencher le pipeline

Pour effectuer l’étape 3 de cette section, vous devez commenter une partie de Jenkinsfile. Sinon, vous obtenez une erreur 404 quand vous essayez d’afficher côte à côte la nouvelle version et l’ancienne. Par défaut, quand vous choisissez de fusionner la demande de tirage, la version partagée précédente de mywebapi est supprimée et remplacée par la nouvelle version. Apportez le changement suivant dans Jenkinsfile avant de terminer l’étape 1 :

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Changez `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`, puis créez une demande de tirage. Par exemple :

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Connectez-vous à Jenkins et sélectionnez le nom du pipeline, puis choisissez **Build Now** (Générer maintenant). 

    Vous pouvez également configurer un *webhook* pour déclencher automatiquement le pipeline Jenkins. Quand une demande de tirage est entrée, GitHub émet un POST à Jenkins, ce qui déclenche le pipeline. Pour plus d’informations sur la configuration d’un webhook, consultez [Connecter Jenkins à GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Comparez les changements apportés à la version partagée actuelle :

    1. Ouvrez votre navigateur et accédez à la version partagée `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT contient l’URL.

    2. Ouvrez un autre onglet, puis entrez l’URL de l’espace de développement de la demande de tirage. Il doit ressembler à `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Vous trouverez le lien dans **Historique de build > <n° de build> > Sortie de la console** pour le travail Jenkins. Recherchez `aksapp` dans la page ou, pour afficher uniquement le préfixe, recherchez `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Construction de l’URL dans l’espace de développement enfant

Quand vous déposez une demande de tirage, Jenkins crée un espace de développement enfant en fonction de l’espace de développement partagé de l’équipe et exécute le code à partir de votre demande de tirage dans cet espace de développement enfant. L’URL de l’espace de développement enfant est au format suivant : `http://$env.azdsprefix.<test_endpoint>`. 

**$env.azdsprefix** est défini pendant l’exécution du pipeline par le plug-in Azure Dev Spaces par **devSpacesCreate** :

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

`test_endpoint` est l’URL de l’application webfrontend que vous avez précédemment déployée à l’aide de `azds up` dans [Déployer des exemples d’applications sur le cluster AKS, étape 7](#test_endpoint). La valeur de `$env.TEST_ENDPOINT` est définie dans la configuration du pipeline. 

L’extrait de code suivant montre l’utilisation de l’URL de l’espace de développement enfant à l’étape `smoketest`. Le code vérifie si l’espace de développement enfant TEST_ENDPOINT est disponible et, le cas échéant, télécharge le texte de bienvenue dans stdout :

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous avez terminé d’utiliser l’exemple d’application, nettoyez les ressources Azure en supprimant le groupe de ressources :

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [CI/CD avec Jenkins sur Azure](jenkins-continuous-deployment.md)
