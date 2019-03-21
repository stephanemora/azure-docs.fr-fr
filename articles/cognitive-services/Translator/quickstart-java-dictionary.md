---
title: 'Démarrage rapide : Rechercher des mots avec le dictionnaire bilingue, Java - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez découvrir comment obtenir des traductions alternatives possibles d’un terme ainsi que des exemples d’utilisation de ces traductions alternatives à l’aide de Java et de l’API de traduction de texte Translator Text.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 5c2f85e6417c3185df58e757f9831ef171e39add
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181892"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-java"></a>Démarrage rapide : Rechercher des mots avec le dictionnaire bilingue en utilisant Java

Dans ce guide de démarrage rapide, vous allez découvrir comment obtenir des traductions alternatives possibles d’un terme ainsi que des exemples d’utilisation de ces traductions alternatives à l’aide de Java et de l’API de traduction de texte Translator Text.

Pour suivre ce démarrage rapide, vous devrez disposer d’un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Traduction de texte Translator Text. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

* [JDK 7 ou ultérieur](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Une clé d’abonnement Azure pour Translator Text

## <a name="initialize-a-project-with-gradle"></a>Initialiser un projet avec Gradle

Commencez par créer un répertoire de travail pour ce projet. À partir de la ligne de commande (ou d’une session Terminal Server), exécutez cette commande :

```console
mkdir alt-translation-sample
cd alt-translation-sample
```

Ensuite, initialisez un projet Gradle. Cette commande crée les fichiers de build nécessaires pour Gradle, dont le plus important est le fichier `build.gradle.kts`, qui est utilisé au moment de l’exécution pour créer et configurer votre application. Exécutez cette commande à partir de votre répertoire de travail :

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

## <a name="configure-the-build-file"></a>Configurer le fichier de build

Recherchez le fichier `build.gradle.kts` et ouvrez-le dans votre IDE ou éditeur de texte habituel. Copiez-y ensuite cette configuration de build :

```
plugins {
    java
    application
}
application {
    mainClassName = "AltTranslation"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Notez que cet exemple présente des dépendances sur OkHttp pour les requêtes HTTP, et sur Gson pour gérer et analyser JSON. Pour en savoir plus sur les configurations de build, consultez [Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Créer un fichier Java

Créez maintenant un dossier pour votre exemple d’application. À partir de votre répertoire de travail, exécutez cette commande :

```console
mkdir -p src\main\java
```

Ensuite, dans ce dossier, créez un fichier nommé `AltTranslation.java`.

## <a name="import-required-libraries"></a>Importer les bibliothèques nécessaires

Ouvrez `AltTranslation.java` et ajoutez-y les instructions import suivantes :

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Définir des variables

Vous devez tout d’abord créer une classe publique pour votre projet :

```java
public class AltTranslation {
  // All project code goes here...
}
```

Ajoutez ces lignes à la classe `AltTranslation`. Vous remarquerez qu’en plus d’`api-version`, deux paramètres supplémentaires ont été ajoutés à `url`. Ces paramètres servent à définir l’entrée et la sortie de traduction. Dans cet exemple, il s’agit de l’anglais (`en`) et de l’espagnol (`es`).

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es";
```

## <a name="create-a-client-and-build-a-request"></a>Créer un client et générer une requête

Ajoutez cette ligne à la classe `AltTranslation` pour instancier `OkHttpClient` :

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Ensuite, générez la requête POST. Au besoin, changez le texte pour la traduction.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Pineapples\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Créer une fonction pour analyser la réponse

Cette fonction simple analyse et agrémente la réponse JSON retournée par le service de traduction de texte Translator Text.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Assemblage

La dernière étape consiste à effectuer une requête et à obtenir une réponse. Ajoutez ces lignes à votre projet :

```java
public static void main(String[] args) {
    try {
        AltTranslation altTranslationRequest = new AltTranslation();
        String response = altTranslationRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre exemple d’application. À partir de la ligne de commande (ou d’une session Terminal Server), accédez à la racine de votre répertoire de travail, puis exécutez cette commande :

```console
gradle build
```

Une fois la génération terminée, exécutez :

```console
gradle run
```

## <a name="sample-response"></a>Exemple de réponse

```json
[
  {
    "normalizedSource": "pineapples",
    "displaySource": "pineapples",
    "translations": [
      {
        "normalizedTarget": "piñas",
        "displayTarget": "piñas",
        "posTag": "NOUN",
        "confidence": 0.7016,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 5,
            "frequencyCount": 158
          },
          {
            "normalizedText": "cones",
            "displayText": "cones",
            "numExamples": 5,
            "frequencyCount": 13
          },
          {
            "normalizedText": "piña",
            "displayText": "piña",
            "numExamples": 3,
            "frequencyCount": 5
          },
          {
            "normalizedText": "ganks",
            "displayText": "ganks",
            "numExamples": 2,
            "frequencyCount": 3
          }
        ]
      },
      {
        "normalizedTarget": "ananás",
        "displayTarget": "ananás",
        "posTag": "NOUN",
        "confidence": 0.2984,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 2,
            "frequencyCount": 16
          }
        ]
      }
    ]
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Explorez l’exemple de code pour ce démarrage rapide et d’autres, y compris la traduction et la translittération, ainsi que d’autres exemples de projets de l’API de traduction de texte Translator Text sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples Java sur GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Voir aussi

* [Traduire le texte](quickstart-java-translate.md)
* [Translittérer du texte](quickstart-java-transliterate.md)
* [Identifier la langue par entrée](quickstart-java-detect.md)
* [Obtenir une liste des langues prises en charge](quickstart-java-languages.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-java-sentences.md)
