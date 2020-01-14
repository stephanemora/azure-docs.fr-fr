---
title: 'Démarrage rapide : Bibliothèque de client Recherche personnalisée Bing pour Java'
description: Commencez à utiliser la bibliothèque cliente Recherche personnalisée Bing pour Java en demandant les résultats de la recherche à votre instance Recherche personnalisée Bing dans ce guide de démarrage rapide.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 1060cceb9241d9c1c490f7e7f12490c734b0a78d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384551"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>Démarrage rapide : Bibliothèque de client Recherche personnalisée Bing pour Java

Commencez à utiliser la bibliothèque de client Recherche personnalisée Bing pour Java. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. L’API Recherche personnalisée Bing vous permet de créer des expériences de recherche exemptes de publicité, adaptées aux sujets qui vous intéressent.

Utilisez la bibliothèque de client Recherche personnalisée Bing pour Java pour :

* Rechercher des résultats de recherche sur le web à partir de votre instance Recherche personnalisée Bing. 

[Documentation de référence](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Artefact (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [Exemples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Conditions préalables requises

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/).
* La version actuelle du [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* L’[outil de génération Gradle](https://gradle.org/install/) ou un autre gestionnaire de dépendances.
* Une instance Recherche personnalisée Bing. Consultez [Démarrage rapide : Créer votre première instance Recherche personnalisée Bing](quick-start.md) pour plus amples informations.

## <a name="setting-up"></a>Configuration

### <a name="create-a-bing-custom-search-azure-resource"></a>Créer une instance Recherche personnalisée Bing

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Recherche personnalisée Bing en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre machine locale. Vous pouvez également :

* Obtenir une [clé d’évaluation](https://azure.microsoft.com/try/cognitive-services/#decision) valide pendant 7 jours gratuitement. Une fois l’inscription terminée, elle sera disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre abonnement ou ressource d’évaluation, [créez une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé, nommée `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

> [!TIP]
> Si vous n’utilisez pas Gradle, vous trouverez les informations sur la bibliothèque de client pour d’autres gestionnaires de dépendances sur le [référentiel central Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `gradle init` à partir de votre répertoire de travail. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts*, qui est utilisé au moment de l’exécution pour configurer votre application.

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

## <a name="install-the-client-library"></a>Installer la bibliothèque de client 

Recherchez *build.gradle.kts* et ouvrez-le avec votre IDE ou votre éditeur de texte habituel. Copiez-y ensuite cette configuration de build. Veillez à inclure la bibliothèque de client sous `dependencies` :

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Créez un dossier pour votre exemple d’application. Entrez la commande suivante à partir de votre répertoire de travail :

```console
mkdir src/main/java
```

Accédez au nouveau dossier et créez un fichier nommé *BingCustomSearchSample.java*. Ouvrez-le et ajoutez les instructions `import` suivantes :


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Créez une classe nommée `BingCustomSearchSample`.

```java
public class BingCustomSearchSample {
}
```

Dans la classe, créez une méthode `main` et une variable pour la clé de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, fermez et rouvrez l’éditeur, l’IDE ou le shell qui l’exécute pour y accéder. Vous définirez les méthodes plus tard.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Modèle objet

Le client Recherche personnalisée Bing est un objet [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) qui est créé à partir de la méthode [authenticate()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) de l’objet [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable). Vous pouvez envoyer une demande de recherche en utilisant la méthode [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) du client.

La réponse de l’API est un objet [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) contenant des informations sur la requête de recherche et les résultats de la recherche.

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Recherche personnalisée Bing pour Java :

* [Authentifier le client](#authenticate-the-client)
* [Obtenir des résultats de recherche à partir de votre instance de recherche personnalisée](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Authentifier le client

Votre méthode main doit inclure un objet [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) qui prend votre clé et appelle sa méthode `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Obtenir des résultats de recherche à partir de votre instance de recherche personnalisée

Utilisez la fonction [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) du client pour envoyer une requête de recherche à votre instance personnalisée. Définissez `withCustomConfig` sur votre ID de configuration personnalisée ou utilisez sa valeur par défaut `1`. Après avoir obtenu une réponse de l’API, vérifiez si des résultats de recherche ont été trouvés. Si c’est le cas, obtenez le premier résultat de la recherche en appelant la fonction `webPages().value().get()` de la réponse et affichez le nom et l’URL du résultat. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Exécution de l'application

Générez l’application avec la commande suivante à partir du répertoire principal de votre projet :

```console
gradle build
```

Exécutez l’application avec l’objectif `run` :

```console
gradle run
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web Recherche personnalisée](./tutorials/custom-search-web-page.md)
