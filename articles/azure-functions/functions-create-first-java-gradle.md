---
title: Utiliser Java et Gradle pour publier une fonction sur Azure
description: Créez et publiez une fonction déclenchée par HTTP sur Azure avec Java et Gradle.
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 34aab24bf39e387715cfa5783b801d45ed488750
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732722"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Utiliser Java et Gradle pour créer et publier une fonction sur Azure

Cet article montre comment générer et publier un projet de fonction Java sur Azure Functions avec l’outil en ligne de commande Gradle. Quand vous avez terminé, votre code de fonction s’exécute dans Azure dans un [plan d’hébergement serverless](functions-scale.md#consumption-plan) et est déclenché par une requête HTTP. 

> [!NOTE]
> Si Gradle n’est pas votre outil de développement préféré, consultez nos tutoriels similaires, destinés aux développeurs Java utilisant [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) et [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).

## <a name="prerequisites"></a>Prérequis

Pour développer des fonctions avec Java, les éléments suivants doivent être installés :

- [Java Developer Kit (JDK)](https://aka.ms/azure-jdks), version 8
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 ou ultérieure
- [Gradle](https://gradle.org/), version 4.10 ou ultérieure

Vous avez également besoin d’un abonnement Azure actif. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Pour pouvoir effectuer ce démarrage rapide, vous devez définir la variable d’environnement JAVA_HOME sur l’emplacement d’installation du JDK.

## <a name="prepare-a-functions-project"></a>Préparer un projet Functions

Utilisez la commande suivante pour cloner l’exemple de projet :

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Ouvrez `build.gradle` et remplacez `appName` dans la section suivante par un nom unique pour éviter les conflits de noms de domaine lors du déploiement sur Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Ouvrez le nouveau fichier Function.java à partir du chemin *src/main/java* dans un éditeur de texte et passez en revue le code généré. Ce code est une fonction [déclenchée par HTTP](functions-bindings-http-webhook.md) qui renvoie le corps de la requête. 

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Exécutez la commande suivante pour générer et exécuter le projet de fonction :

```bash
gradle jar --info
gradle azureFunctionsRun
```
Une sortie semblable à celle-ci est retournée par Azure Functions Core Tools quand vous exécutez le projet localement :

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Déclenchez la fonction à partir de la ligne de commande en utilisant la commande cURL suivante dans une nouvelle fenêtre de terminal :

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

La sortie attendue est la suivante :

<pre>
Hello AzureFunctions!
</pre>

La [clé de fonction](functions-bindings-http-webhook-trigger.md#authorization-keys) n’est pas nécessaire lors de l’exécution locale.  
Utilisez `Ctrl+C` dans le terminal pour arrêter le code de la fonction.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Déployer la fonction sur Azure

Une application de fonction et les ressources associées sont créées dans Azure lors du déploiement initial de votre application de fonction. Avant de procéder au déploiement, utilisez la commande Azure CLI [az login](/cli/azure/authenticate-azure-cli) pour vous connecter à votre abonnement Azure. 

```azurecli
az login
```

> [!TIP]
> Si votre compte peut accéder à plusieurs abonnements, utilisez [az account set](/cli/azure/account#az-account-set) pour définir l’abonnement par défaut pour cette session. 

Utilisez la commande suivante pour déployer votre projet vers une nouvelle application de fonction. 

```bash
gradle azureFunctionsDeploy
```

Cela crée les ressources suivantes dans Azure, en fonction des valeurs du fichier build.gradle :

+ Groupe de ressources. Nommé avec le _resourceGroup_ que vous avez fourni.
+ Compte de stockage. Requis par les fonctions. Le nom est généré de façon aléatoire en fonction des exigences du nom de compte de stockage.
+ Plan App Service. Hébergement de Plan de consommation serverless pour votre application de fonction dans l’_appRegion_ spécifiée. Le nom est généré de façon aléatoire.
+ Application de fonction. Une application de fonction est l’unité de déploiement et d’exécution de vos fonctions. Le nom est l’_appName_, auquel est ajouté un numéro généré de façon aléatoire. 

Le déploiement empaquette également les fichiers projet et les déploie dans la nouvelle application de fonction à l’aide de [zip deployment](functions-deployment-technologies.md#zip-deploy), en activant le mode d’exécution à partir du package.

Étant donné que le déclencheur HTTP que nous avons publié utilise `authLevel = AuthorizationLevel.FUNCTION`, vous devez obtenir la clé de fonction pour appeler le point de terminaison de fonction sur HTTP. Le moyen le plus simple d’obtenir la clé de fonction consiste à passer par le [Azure portal].

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Obtient l’URL du déclencheur HTTP

Vous pouvez obtenir l’URL requise pour déclencher votre fonction, avec la clé de fonction, à partir du portail Azure. 

1. Accédez au [Azure portal], connectez-vous, tapez l’_appName_ de votre application de fonction dans **Rechercher** en haut de la page, puis appuyez sur Entrée.
 
1. Dans votre application de fonction, développez **Fonctions (Lecture seule)** , choisissez votre fonction, puis sélectionnez **</> Obtenir l’URL de fonction** en haut à droite. 

    ![Copier l’URL de fonction à partir du portail Azure](./media/functions-create-java-maven/get-function-url-portal.png)

1. Choisissez **par défaut (Clé de fonction)** et sélectionnez **Copier**. 

Vous pouvez maintenant utiliser l’URL copiée pour accéder à votre fonction.

## <a name="verify-the-function-in-azure"></a>Vérifier la fonction dans Azure

Pour vérifier que l’application de fonction s’exécute sur Azure à l’aide de `cURL`, remplacez l’URL de l’exemple ci-dessous par celle que vous avez copiée à partir du portail.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Cela envoie une requête POST au point de terminaison de fonction avec `AzureFunctions` dans le corps de la requête. Vous obtenez la réponse suivante.

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un projet de fonctions Java avec une fonction déclenchée par HTTP, vous l’avez exécuté sur votre ordinateur local et vous l’avez déployée sur Azure. À présent, étendez votre fonction en effectuant un...

> [!div class="nextstepaction"]
> [Ajout de liaison de sortie de file d’attente Stockage Azure](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure portal]: https://portal.azure.com
