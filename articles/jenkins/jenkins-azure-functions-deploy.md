---
title: Effectuer un déploiement sur Azure Functions à l’aide du plug-in Azure Functions Jenkins
description: Découvrir comment effectuer un déploiement sur Azure Functions à l’aide du plug-in Azure Functions Jenkins
keywords: jenkins, azure, devops, java, azure functions
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: edf8c763a46a6f519175842a6525b2c1b314fb66
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470664"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Effectuer un déploiement sur Azure Functions à l’aide du plug-in Azure Functions Jenkins

[Azure Functions](/azure/azure-functions/) est un service de calcul sans serveur. À l’aide d’Azure Functions, vous pouvez exécuter du code à la demande sans provisionner ou gérer d’infrastructure. Ce tutoriel montre comment déployer une fonction Java sur Azure Functions à l’aide du plug-in Azure Functions.

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- **Serveur Jenkins** : Si aucun serveur Jenkins n’est installé, consultez l’article [Créer un serveur Jenkins sur Azure](./install-jenkins-solution-template.md).

  > [!TIP]
  > Le code source utilisé pour ce tutoriel se trouve dans le [dépôt GitHub de Visual Studio Chine](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Créer une fonction Java

Pour créer une fonction Java avec la pile d’exécution Java, utilisez le [portail Azure](https://portal.azure.com) ou l’[interface de ligne de commande Azure](/cli/azure/?view=azure-cli-latest).

Les étapes suivantes montrent comment créer une fonction Java à l’aide de l’interface de ligne de commande Azure :

1. Créez un groupe de ressources, en remplaçant l’espace réservé **&lt;resource_group>** par le nom de votre groupe de ressources.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Créez un compte de stockage Azure, en remplaçant les espaces réservés par les valeurs appropriées.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Créez l’application de fonction de test, en remplaçant les espaces réservés par les valeurs appropriées.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Préparer le serveur Jenkins

Les étapes suivantes expliquent comment préparer le serveur Jenkins :

1. Déployez un [serveur Jenkins](https://aka.ms/jenkins-on-azure) sur Azure. Si aucune instance du serveur Jenkins n’est encore installée, l’article [Créer un serveur Jenkins sur Azure](./install-jenkins-solution-template.md) vous guide tout au long du processus.

1. Connectez-vous à l’instance Jenkins avec SSH.

1. Sur l’instance Jenkins, installez maven à l’aide de la commande suivante :

    ```terminal
    sudo apt install -y maven
    ```

1. Sur l’instance Jenkins, installez [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) en exécutant les commandes suivantes à l’invite de commandes du terminal :

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Dans le tableau de bord Jenkins, installez les plug-ins suivants :

    - Plug-in Azure Functions
    - Plug-in EnvInject

1. Jenkins a besoin d’un principal de service Azure pour authentifier les ressources Azure et y accéder. Reportez-vous au tutoriel [Effectuer un déploiement sur Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md) pour obtenir des instructions pas à pas.

1. À l’aide du principal de service Azure, ajoutez le type d’informations d’identification « Microsoft Azure Service Principal » à Jenkins. Reportez-vous au tutoriel [Effectuer un déploiement sur Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins).

## <a name="fork-the-sample-github-repo"></a>Dupliquer (fork) le dépôt d’exemples GitHub

1. [Connectez-vous au dépôt GitHub pour consulter l’exemple d’application odd-or-even](https://github.com/VSChina/odd-or-even-function.git).

1. En haut à droite de GitHub, choisissez **Fork** (Dupliquer).

1. Suivez les invites pour sélectionner votre compte GitHub et terminer la duplication.

## <a name="create-a-jenkins-pipeline"></a>Créer un pipeline Jenkins

Dans cette section, vous allez créer le [pipeline Jenkins](https://jenkins.io/doc/book/pipeline/).

1. Dans le tableau de bord Jenkins, créez un pipeline.

1. Activez l’option permettant de **préparer un environnement pour l’exécution**.

1. Ajoutez les variables d’environnement suivantes dans **Properties Content** (Contenu des propriétés), en remplaçant les espaces réservés par les valeurs appropriées pour votre environnement :

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. Dans la section **Pipeline->Définition**, sélectionnez **Script de pipeline à partir de SCM**.

1. Entrez l’URL et le chemin du script de votre duplication (fork) GitHub (« doc/ressources/jenkins/JenkinsFile ») qui doivent être utilisés dans l’[exemple JenkinsFile](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>Générer et déployer

À présent, il est temps d’exécuter le travail Jenkins.

1. Commencez par obtenir la clé d’autorisation à l’aide des instructions fournies dans l’article [Déclencheurs et liaisons HTTP d’Azure Functions](/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys).

1. Dans votre navigateur, entrez l’URL de l’application. Remplacez les espaces réservés par les valeurs appropriées, puis spécifiez une valeur numérique pour **&lt;input_number>** comme entrée pour la fonction Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Des résultats semblables à l’exemple de sortie suivant (où un nombre impair, 365, a été utilisé comme test) s’affichent :

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez les ressources que vous avez créées en effectuant l’étape suivante :

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure Functions, consultez la ressource suivante :
> [!div class="nextstepaction"]
> [Documentation Azure Functions](/azure/azure-functions/)
