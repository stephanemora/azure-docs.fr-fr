---
title: 'Démarrage rapide : Bibliothèque cliente Content Moderator pour Java'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, apprenez à utiliser la bibliothèque cliente Azure Cognitive Services Content Moderator pour Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5dd1c1bb2b321e617efc430ce48745c06a827305
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772464"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Démarrage rapide : Bibliothèque cliente Content Moderator pour Java

Commencez à utiliser la bibliothèque cliente Content Moderator pour Java. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Content Moderator est un service cognitif qui vérifie le texte, les images et le contenu vidéo à la recherche d’éléments potentiellement dangereux, offensants ou indésirables. Lorsque des éléments de ce type sont détectés, le service applique les étiquettes appropriées (indicateurs) au contenu. L’application peut ensuite gérer le contenu marqué afin de se conformer aux réglementations ou pour maintenir l’environnement souhaité pour les utilisateurs.

Utilisez la bibliothèque cliente Content Moderator pour Java aux fins suivantes :

* Modérez des images pour y rechercher du contenu pour adultes ou osé, du texte ou des visages.

[Documentation de référence](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [Artifact (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Exemples](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* La version actuelle du [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* L’[outil de génération Gradle](https://gradle.org/install/) ou un autre gestionnaire de dépendances.

## <a name="setting-up"></a>Configuration

### <a name="create-a-content-moderator-azure-resource"></a>Créer une ressource Azure Content Moderator

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Content Moderator en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local. Vous pouvez également :

* Obtenir une [clé](https://azure.microsoft.com/try/cognitive-services/#decision) pour un essai gratuit valide pendant 7 jours. Une fois l’inscription terminée, elle est disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre abonnement ou ressource d’essai, [créez une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé, nommée `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```
Exécutez `gradle init`. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts*, qui est utilisé au moment de l’exécution pour créer et configurer votre application. Exécutez cette commande à partir de votre répertoire de travail :

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un DSL de script de génération, sélectionnez **Kotlin**.

Recherchez *build.gradle.kts* et ouvrez-le avec votre IDE ou éditeur de texte préféré. Copiez-y ensuite la configuration de build suivante. Cette configuration définit le projet en tant qu’application Java dont le point d’entrée est la classe **ContentModeratorQuickstart**. Elle importe le SDK Content Moderator, ainsi que le SDK Gson pour la sérialisation JSON.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

Dans votre répertoire de travail, exécutez la commande suivante pour créer un dossier de projet source.

```console
mkdir -p src/main/java
```

Créez ensuite un fichier nommé *ContentModeratorQuickstart.java* dans le nouveau dossier. Ouvrez le fichier dans votre éditeur ou IDE préféré et importez les bibliothèques suivantes en haut :

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent certaines des principales fonctionnalités du SDK Java Content Moderator.

|Nom|Description|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Cette classe est nécessaire pour toutes les fonctionnalités Content Moderator. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Cette classe fournit les fonctionnalités permettant d’analyser des images pour y rechercher du contenu pour adultes, des informations personnelles ou des visages.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Cette classe fournit les fonctionnalités d’analyse de texte pour le langage, les blasphèmes, les erreurs et les informations personnelles.|
|[Révisions](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Cette classe fournit les fonctionnalités des API de révision, notamment les méthodes de création de travaux, les workflows personnalisés et les révisions humaines.|


## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque cliente Content Moderator pour Java :

* [Authentifier le client](#authenticate-the-client)
* [Modérer les images](#moderate-images)

## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE]
> Cette étape part du principe que vous avez [créé une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour votre clé Content Moderator, nommée `AZURE_CONTENTMODERATOR_KEY`.

Dans la méthode `main` de l’application, créez un objet [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) à l’aide de la valeur de votre point de terminaison d’abonnement et de la variable d’environnement de clé d’abonnement. 

> [!NOTE]
> Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Modérer des images

### <a name="get-images"></a>Obtenir des images

Dans le dossier **src/main/** de votre projet, créez un dossier de **ressources** et accédez-y. Créez ensuite un nouveau fichier texte nommé *ImageFiles.txt*. Dans ce fichier, ajoutez les URL des images pour analyser&mdash;une URL sur chaque ligne. Vous pouvez utiliser les exemples d’image suivants :

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Définir une classe d’assistance

Ensuite, dans votre fichier *ContentModeratorQuickstart.java*, ajoutez la définition de classe suivante à l’intérieur de la classe **ContentModeratorQuickstart**. Cette classe interne sera utilisée ultérieurement dans le processus de modération d’image.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Parcourir les images

Ensuite, ajoutez le code suivant en bas de la méthode `main`. Ou vous pouvez l’ajouter à une méthode distincte appelée à partir de `main`. Ce code parcourt chaque ligne du fichier _ImageFiles.txt_.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Rechercher du contenu pour adultes et osé
Cette ligne de code vérifie l’image à l’URL donnée pour y rechercher du contenu pour adultes ou osé. Pour plus d’informations sur ces termes, consultez le guide conceptuel sur la modération d’images.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Rechercher du texte
Cette ligne de code recherche le texte visible dans l’image.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Rechercher des visages
Cette ligne de code recherche les visages humains dans l’image.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Enfin, stockez les informations renvoyées dans la liste `EvaluationData`.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Imprimer les résultats

Après la boucle `while`, ajoutez le code suivant, qui imprime les résultats dans la console et dans un fichier de sortie nommé *SRC/main/Resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Fermez l’instruction `try` et ajoutez une instruction `catch` pour terminer la méthode.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Exécution de l'application

Vous pouvez générer l’application avec :

```console
gradle build
```

Exécutez l’application avec la commande `gradle run` :

```console
gradle run
```

Accédez ensuite au fichier *src/main/resources/ModerationOutput.json* et affichez les résultats de la modération du contenu.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à utiliser la bibliothèque Java Content Moderator pour effectuer des tâches de modération. Pour plus d’informations sur la modération des images ou d’autres éléments multimédias, consultez le guide conceptuel.

> [!div class="nextstepaction"]
>[Concepts liés à la modération d’image](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Qu’est-ce qu’Azure Content Moderator ?](./overview.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java).