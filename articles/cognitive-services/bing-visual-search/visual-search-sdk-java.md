---
title: 'Démarrage rapide : Bibliothèque de client Recherche visuelle Bing pour Java | Microsoft Docs'
description: Chargez une image à l’aide du SDK Recherche visuelle Bing pour récupérer des insights sur celle-ci.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/30/2019
ms.author: aahi
ms.openlocfilehash: 7fb00fd3ce588aeeba4f315f191f6b82d6b75715
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695723"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Démarrage rapide : Bibliothèque de client Recherche visuelle Bing pour Java

Commencez à utiliser la bibliothèque de client Recherche visuelle Bing pour Java. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. 

Utilisez la bibliothèque de client Recherche visuelle Bing pour Java pour :

* Télécharger une image pour envoyer une demande de recherche visuelle.
* Obtenir le jeton insights de l’image et les balises de recherche visuelle.

[Documentation de référence](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [Artefact (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [Exemples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* La version actuelle du [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* L’[outil de génération Gradle](https://gradle.org/install/) ou un autre gestionnaire de dépendances

## <a name="setting-up"></a>Configuration

### <a name="create-a-bing-visual-search-azure-resource"></a>Créer une ressource Azure Recherche visuelle Bing

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Recherche visuelle Bing en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre machine locale. Vous pouvez également :

* Obtenir une [clé](https://azure.microsoft.com/try/cognitive-services/#decision) pour un essai gratuit valide pendant 7 jours. Une fois l’inscription terminée, elle sera disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre abonnement ou ressource d’évaluation, [créez une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé, nommée `BING_SEARCH_V7_SUBSCRIPTION_KEY`.

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `gradle init` à partir de votre répertoire de travail. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts*, qui est utilisé au moment de l’exécution pour créer et configurer votre application.

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

Recherchez *build.gradle.kts* et ouvrez-le avec votre IDE ou votre éditeur de texte habituel. Copiez-y ensuite cette configuration de build :

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Créez un dossier pour votre exemple d’application. Entrez la commande suivante à partir de votre répertoire de travail :

```console
mkdir -p src/main/java
```

Créez un dossier pour l’image à charger sur l’API. Placez l’image dans le dossier **resources**.

```console
mkdir -p src/main/resources
``` 

Accédez au nouveau dossier et créez un fichier nommé *BingVisualSearchSample.java*. Ouvrez-le dans votre éditeur ou IDE habituel et ajoutez les instructions `import` suivantes :

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Ensuite, créez une classe.

```java
public class BingVisualSearchSample {
}
```

Dans la méthode `main` de l’application, créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable. Créez ensuite un `byte[]` pour l’image que vous allez charger. Créez un bloc `try` pour les méthodes que vous allez définir ultérieurement, puis chargez l’image et convertissez-la en octets à l’aide de `toByteArray()`.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Ce guide de démarrage rapide utilise le gestionnaire de dépendances Gradle. Vous trouverez la bibliothèque de client et des informations concernant d’autres gestionnaires de dépendances sur le [référentiel central Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Dans le fichier *build.gradle.kts* de votre projet, veillez à inclure la bibliothèque de client en tant qu’instruction `implementation`. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Recherche visuelle Bing et Java :

* [Authentifier le client](#authenticate-the-client)
* [Envoyer une demande de recherche visuelle](#send-a-visual-search-request)
* [Imprimer le jeton insights de l’image et les balises de recherche visuelle](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE]
> Ce guide de démarrage rapide part du principe que vous avez [créé une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour votre clé Recherche visuelle Bing, nommée `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


Dans votre méthode principale, veillez à utiliser votre clé d’abonnement pour instancier un objet [BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable).

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Envoyer une demande de recherche visuelle

Dans une nouvelle méthode, envoyez le tableau d’octets d’image (créé dans la méthode `main()`) à l’aide de la méthode [bingImages().visualSearch()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) du client. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Imprimer le jeton insights de l’image et les étiquettes de recherche visuelle

Vérifiez si l’objet [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) a la valeur null. Si ce n’est pas le cas, imprimez le jeton insights de l’image, le nombre d’étiquettes, le nombre d’actions et le premier type d’action.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Exécution de l'application

Vous pouvez générer l’application avec :

```console
gradle build
```

Exécutez l’application avec l’objectif `run` :

```console
gradle run
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interface de ligne de commande Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](tutorial-bing-visual-search-single-page-app.md)
