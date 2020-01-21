---
title: Déployer des applications sur Azure Spring Cloud avec Jenkins et Azure CLI
description: Découvrez comment utiliser Azure CLI dans un pipeline d’intégration et de déploiement continus pour déployer des microservices sur Azure Spring Cloud service.
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732852"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Tutoriel : Déployer des applications sur Azure Spring Cloud avec Jenkins et Azure CLI

[Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) est un développement de microservices complètement managé, avec détection de service et gestion de la configuration intégrées. Le service permet de déployer facilement des applications de microservices basées sur Spring boot sur Azure. Ce tutoriel montre comment utiliser Azure CLI dans Jenkins pour automatiser l’intégration et la livraison continues (CI/CD) pour Azure Spring Cloud.

Dans ce tutoriel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Provisionner une instance de service et lancer une application Java Spring
> * Préparer votre serveur Jenkins
> * Utiliser Azure CLI dans un pipeline Jenkins pour générer et déployer les applications de microservices 

Ce tutoriel suppose des connaissances de niveau intermédiaire dans les domaines suivants : services Azure de base, Azure Spring Cloud, [pipelines](https://jenkins.io/doc/book/pipeline/) et plug-ins Jenkins, et GitHub.

## <a name="prerequisites"></a>Conditions préalables requises

>[!Note]
> Azure Spring Cloud est actuellement disponible en préversion publique. Les offres en préversion publique permettent aux clients de tester les nouvelles fonctionnalités avant leur publication officielle.  Les fonctionnalités et services en préversion publique ne sont pas destinés à une utilisation en contexte de production.  Pour en savoir plus sur le support offert avec les préversions, consultez notre [FAQ](https://azure.microsoft.com/support/faq/) ou soumettez une [demande de support](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* Un compte GitHub. Si vous n’avez pas de compte GitHub, [créez-en un gratuitement](https://github.com/) avant de commencer.

* Un serveur maître Jenkins. Si vous ne disposez pas d’un serveur maître Jenkins, déployez [Jenkins](https://aka.ms/jenkins-on-azure) sur Azure en suivant les étapes décrites dans ce [guide de démarrage rapide](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). Les éléments suivants sont nécessaires sur le nœud/agent Jenkins (par exemple le serveur de build) :

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 ou ultérieur](https://maven.apache.org/download.cgi)
    * [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.67 ou ultérieure installée.

    >[!TIP]
    > Les outils tels que Git, JDK, Azure CLI et plug-ins Azure sont inclus par défaut dans le modèle de solution [Microsoft Jenkins](https://aka.ms/jenkins-on-azure) de la Place de marché Azure.
    
* [Souscrire à un abonnement Azure](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Provisionner une instance de service et lancer une application Java Spring

Nous utilisons [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) comme exemple d’application de service Microsoft et nous suivons les étapes indiquées dans [Démarrage rapide : Lancer une application Java Spring en utilisant Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) pour provisionner l’instance de service et configurer les applications. Si vous avez déjà effectué le même processus, vous pouvez passer à la section suivante. Sinon, les commandes Azure CLI sont listées plus loin. Pour obtenir des informations générales supplémentaires, consultez [Démarrage rapide : Lancer une application Java Spring en utilisant Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli).

Votre ordinateur local doit satisfaire les même prérequis que le serveur de build Jenkins. Vérifiez que les éléments suivants sont installés pour pouvoir générer et déployer les applications de microservices :
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 ou ultérieur](https://maven.apache.org/download.cgi)
    * [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.67 ou ultérieure installée.

1. Installez l’extension Azure Spring Cloud :

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Créez un groupe de ressources destiné à contenir votre service Azure Spring Cloud :

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Provisionnez une instance d’Azure Spring Cloud :

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Dupliquez (fork) le dépôt [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) sur votre propre compte GitHub. Sur votre ordinateur local, clonez votre dépôt dans un répertoire appelé `source-code` :

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Configurez votre serveur de configuration. Veillez à remplacer &lt;your GitHub id&gt; par la valeur correcte.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Générez le projet :

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Créez les trois microservices : **gateway**, **auth-service** et **account-service** :

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Déployez les applications : 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Affectez un point de terminaison public à la passerelle :

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Interrogez l’application de passerelle pour obtenir l’URL afin de pouvoir vérifier que l’application est en cours d’exécution.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Accédez à l’URL fournie par la commande précédente pour exécuter l’application PiggyMetrics. 

## <a name="prepare-jenkins-server"></a>Préparer le serveur Jenkins

Dans cette section, vous allez préparer le serveur Jenkins pour exécuter une build, ce qui est parfait pour effectuer des tests. Toutefois, en raison de l’implication en terme de sécurité, vous devez utiliser un [agent de machine virtuelle Azure](https://plugins.jenkins.io/azure-vm-agents) ou un [agent de conteneur Azure](https://plugins.jenkins.io/azure-container-agents) pour mettre en place un agent dans Azure afin d’exécuter vos builds. Pour plus d’informations, consultez l’article Jenkins sur les [implications en matière de sécurité de la génération sur le nœud master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Installer les plug-ins

1. Connectez-vous à votre serveur Jenkins. Choisissez**Manage Jenkins > Manage Plugins** (Gérer Jenkins > Gérer les plug-ins).
2. Sous l’onglet **Disponibles**, sélectionnez les plug-ins suivants :
    * [GitHub Integration](https://plugins.jenkins.io/github-pullrequest)
    * [Informations d’identification Azure](https://plugins.jenkins.io/azure-credentials)

    Si ces plug-ins ne sont pas listés, examinez l’onglet **Installés** pour voir s’ils sont déjà installés.

3. Pour installer les plug-ins, choisissez **Télécharger maintenant et installer après le redémarrage**.

4. Redémarrez votre serveur Jenkins pour terminer l’installation.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Ajouter vos informations d’identification de principal de service Azure dans le magasin d’informations d’identification Jenkins

1. Un principal de service Azure est nécessaire pour les déploiements sur Azure. Pour plus d’informations, consultez la section  [Créer le principal du service](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) du tutoriel Déployer dans Azure App Service. La sortie de `az ad sp create-for-rbac` ressemble à ceci :

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Dans le tableau de bord Jenkins, sélectionnez **Informations d’identification** > **Système**. Puis, sélectionnez **Informations d’identification globales (sans restriction)** .

3. Sélectionnez **Ajouter des informations d’identification**. 

4. Sélectionnez **Principal de service Microsoft Azure** comme type.

5. Fournissez des valeurs pour : * ID d’abonnement : utilisez votre ID d’abonnement Azure * ID client : utilisez `appId` * Clé secrète client : utilisez `password` * ID de locataire : utilisez `tenant` * Environnement Azure : sélectionnez une valeur prédéfinie. Par exemple, utilisez **Azure** pour Azure Global * ID : spécifiez **azure_service_principal**. Nous utiliserons cet ID lors d’une étape ultérieure de cet article * Description : champ facultatif. Nous vous recommandons de spécifier ici une valeur explicite.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Installer Maven et l’extension spring-cloud Azure CLI

L’exemple de pipeline utilise Maven pour générer et Azure CLI pour effectuer un déploiement sur l’instance de service. Quand Jenkins est installé, il crée un compte administrateur nommé *jenkins*. Vérifiez que l’utilisateur *jenkins* dispose des autorisations nécessaires pour exécuter l’extension spring-cloud.

1. Connectez-vous au maître Jenkins par le biais du protocole SSH. 

2. Installez Maven.

    ```bash
        sudo apt-get install maven 
    ```

3. Installez Azure CLI. Pour plus d’informations, consultez [Installation d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Azure CLI est installé par défaut si vous utilisez [Jenkins Master sur Azure](https://aka.ms/jenkins-on-azure).

4. Passez à l’utilisateur `jenkins` :

    ```bash
        sudo su jenkins
    ```

5. Ajoutez l’extension **spring-cloud** :

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Créer un Jenkinsfile
1. Dans votre propre dépôt (https://github.com/&lt ;your GitHub id&gt; /piggymetrics), créez un **Jenkinsfile** à la racine.

2. Mettez le fichier à jour comme suit. Veillez à remplacer les valeurs de **\<resource group name>** et **\<service name>** . Remplacez **azure_service_principal** par l’ID approprié si vous avez utilisé une valeur différente quand vous avez ajouté les informations d’identification dans Jenkins. 

```groovy
    node {
      stage('init') {
        checkout scm
      }
      stage('build') {
        sh 'mvn clean package'
      }
      stage('deploy') {
        withCredentials([azureServicePrincipal('azure_service_principal')]) {
          // login to Azure
          sh '''
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            az account set -s $AZURE_SUBSCRIPTION_ID
          '''  
          // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
          sh 'az configure --defaults group=<resource group name>'
          sh 'az configure --defaults spring-cloud=<service name>'
          // Deploy applications
          sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
          sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
          sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
          sh 'az logout'
        }
      }
    }
```

3. Enregistrez et validez la modification.

## <a name="create-the-job"></a>Créer le travail

1. Dans le tableau de bord Jenkins, cliquez sur **New Item** (Nouvel élément).

2. Spécifiez un nom, *Deploy-PiggyMetrics*, pour le travail et sélectionnez **Pipeline**. Cliquez sur OK.

3. Ensuite, cliquez sur l’onglet **Pipeline**.

4. Pour **Définition**, sélectionnez **Pipeline script from SCM** (Script de pipeline à partir de SCM).

5. Pour **SCM**, sélectionnez **Git**.

6. Entrez l’URL GitHub de votre dépôt dupliqué : **https://github.com/&lt ;votre_ID_GitHub&gt; /piggymetrics.git**.

7. Vérifiez que **Branch Specifier (black for 'any')** (Spécificateur de branche) est * **/Azure**.

8. Conservez **Jenkinsfile** pour **Script path** (Chemin du script).

7. Cliquez sur **Save** (Enregistrer).

## <a name="validate-and-run-the-job"></a>Valider et exécuter le travail

Avant d’exécuter le travail, nous allons mettre à jour le texte dans la zone d’entrée de connexion avec la valeur **entrez l’ID de connexion**.

1. Dans votre propre dépôt, ouvrez `index.html` dans **/gateway/src/main/resources/static/** .

2. Recherchez « enter your login » et remplacez-le par le texte vers « entrez l’ID de connexion ».

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. Valider les modifications

4. Exécutez le travail manuellement dans Jenkins. Dans le tableau de bord Jenkins, cliquez sur le travail *Deploy-PiggyMetrics* puis **Build Now** (Générer maintenant).

Une fois le travail terminé, accédez à l’adresse IP publique de l’application **passerelle** et vérifiez que votre application a été mise à jour. 

![Piggy Metrics mis à jour](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez les ressources créées dans cet article :

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser Azure CLI dans Jenkins pour automatiser l’intégration et la livraison continues (CI/CD) pour Azure Spring Cloud.

Pour en savoir plus sur le fournisseur Azure Jenkins, consultez le site Jenkins sur Azure.

> [!div class="nextstepaction"]
> [Jenkins sur Azure](/azure/jenkins/)
