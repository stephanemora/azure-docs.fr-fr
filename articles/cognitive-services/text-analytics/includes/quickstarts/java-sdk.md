---
title: 'Démarrage rapide : Bibliothèque cliente Analyse de texte v3 pour Java | Microsoft Docs'
description: Découvrez comment utiliser la bibliothèque cliente Analyse de texte v3 pour Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 06/11/2021
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: bd2babf0ae88cce5d6162ddd797225a83ef9367b
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112083667"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Version préliminaire de la version 3.1](#tab/version-3-1)

[Documentation de référence](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.7/sdk/textanalytics/azure-ai-textanalytics) | [Package](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.7) | [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.7/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

[Documentation de référence](/java/api/overview/azure/ai-textanalytics-readme) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics) | [Package](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) | [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

---

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [Kit de développement Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) avec version 8 ou ultérieure
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="créez une ressource Analyse de texte"  target="_blank">Créer une ressource Analyse de texte </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison.  Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Analyse de texte. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* Pour utiliser la fonctionnalité d’analyse, vous aurez besoin d’une ressource Analyse de texte avec le niveau tarifaire Standard (S).

## <a name="setting-up"></a>Configuration

### <a name="add-the-client-library"></a>Ajouter la bibliothèque de client

# <a name="version-31-preview"></a>[Version préliminaire de la version 3.1](#tab/version-3-1)

Créez un projet Maven dans l’IDE ou l’environnement de développement de votre choix. Ensuite, ajoutez la dépendance suivante au fichier *pom.xml* de votre projet : Vous trouverez la syntaxe d’implémentation [pour d’autres outils de génération](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.7) en ligne.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.7</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Créez un projet Maven dans l’IDE ou l’environnement de développement de votre choix. Ensuite, ajoutez la dépendance suivante au fichier *pom.xml* de votre projet : Vous trouverez la syntaxe d’implémentation [pour d’autres outils de génération](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) en ligne.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez [sur GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), qui contient les exemples de code dans ce guide de démarrage rapide. 

---

Créez un fichier Java nommé `TextAnalyticsSamples.java`. Ouvrez le fichier et ajoutez les instructions `import` suivantes :

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

Dans le fichier Java, ajoutez une nouvelle classe ainsi que la clé et le point de terminaison de votre ressource Azure, comme indiqué ci-dessous.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Ajoutez la méthode main suivante à la classe. Vous définirez les méthodes appelées ici ultérieurement.

# <a name="version-31-preview"></a>[Version 3.1 (préversion)](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
        AnalyzeOperationExample(client)
}
```

---


## <a name="object-model"></a>Modèle objet

Le client Analyse de texte est un objet `TextAnalyticsClient` qui s’authentifie auprès d’Azure avec votre clé et fournit des fonctions permettant d’accepter du texte sous forme de chaînes individuelles ou de lots. Vous pouvez envoyer du texte à l’API de façon synchrone ou de façon asynchrone. L’objet Response contient les informations d’analyse de chaque document que vous envoyez. 

## <a name="code-examples"></a>Exemples de code

* [Authentifier le client](#authenticate-the-client)
* [Analyse des sentiments](#sentiment-analysis) 
* [Exploration des opinions](#opinion-mining)
* [Détection de la langue](#language-detection)
* [Reconnaissance d’entité nommée](#named-entity-recognition-ner)
* [Liaison d’entités](#entity-linking)
* [Extraction de phrases clés](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Authentifier le client

Créez une méthode pour instancier l’objet `TextAnalyticsClient` avec la clé et le point de terminaison de votre ressource Analyse de texte. Cet exemple est identique pour les versions 3.0 et 3.1 de l’API.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


Dans la méthode `main()` de votre programme, appelez la méthode d’authentification pour instancier le client.

## <a name="sentiment-analysis"></a>analyse de sentiments

# <a name="version-31-preview"></a>[Version préliminaire de la version 3.1](#tab/version-3-1)

> [!NOTE]
> Dans la version `3.1` :
> * Analyse des sentiments comprend une analyse Exploration des opinions, qui est un indicateur facultatif. 
> * Exploration des opinions contient un sentiment au niveau de l’aspect et de l’opinion. 

Créez une fonction appelée `sentimentAnalysisExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `analyzeSentiment()`. L’objet `AnalyzeSentimentResult` retourné contient `documentSentiment` et `sentenceSentiments` si l’opération réussit, ou un `errorMessage` si ce n’est pas le cas. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Output

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="opinion-mining"></a>Exploration des opinions

Pour effectuer une analyse des sentiments avec exploration des opinions, créez une fonction appelée `sentimentAnalysisWithOpinionMiningExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `analyzeSentiment()` en définissant l’objet facultatif `AnalyzeSentimentOptions`. L’objet `AnalyzeSentimentResult` retourné contient `documentSentiment` et `sentenceSentiments` si l’opération réussit, ou un `errorMessage` si ce n’est pas le cas. 


```java
 static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
 {
     // The document that needs be analyzed.
     String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

     System.out.printf("Document = %s%n", document);

     AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
     final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
     SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
     System.out.printf(
             "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
             documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());


     documentSentiment.getSentences().forEach(sentenceSentiment -> {
         SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
         System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                 sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
         sentenceSentiment.getOpinions().forEach(opinion -> {
             TargetSentiment targetSentiment = opinion.getTarget();
             System.out.printf("\t\tTarget sentiment: %s, target text: %s%n", targetSentiment.getSentiment(),
                     targetSentiment.getText());
             for (AssessmentSentiment assessmentSentiment : opinion.getAssessments()) {
                 System.out.printf("\t\t\t'%s' assessment sentiment because of \"%s\". Is the assessment negated: %s.%n",
                         assessmentSentiment.getSentiment(), assessmentSentiment.getText(), assessmentSentiment.isNegated());
             }
         });
     });
 }
```

### <a name="output"></a>Output

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: atmosphere
            'negative' assessment sentiment because of "bad". Is the assessment negated: false.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: Staff
            'negative' assessment sentiment because of "friendly". Is the assessment negated: true.
            'negative' assessment sentiment because of "helpful". Is the assessment negated: true.
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Créez une fonction appelée `sentimentAnalysisExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `analyzeSentiment()`. L’objet `AnalyzeSentimentResult` retourné contient `documentSentiment` et `sentenceSentiments` si l’opération réussit, ou un `errorMessage` si ce n’est pas le cas. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Output

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

---

## <a name="language-detection"></a>Détection de la langue

Créez une fonction appelée `detectLanguageExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `detectLanguage()`. L’objet `DetectLanguageResult` retourné contient une langue principale détectée, une liste d’autres langues détectées si l’opération réussit ou un `errorMessage` si ce n’est pas le cas. Cet exemple est identique pour les versions 3.0 et 3.1 de l’API.

> [!Tip]
> Dans certains cas, il peut être difficile de lever toute ambiguïté sur les langues en fonction de l’entrée. Vous pouvez utiliser le paramètre `countryHint` pour spécifier un code de pays à 2 lettres. L’API utilise « US » comme countryHint par défaut. Pour modifier ce comportement, vous pouvez réinitialiser ce paramètre en définissant cette valeur sur une chaîne vide `countryHint = ""`. Pour définir une autre valeur par défaut, définissez la propriété `TextAnalyticsClientOptions.DefaultCountryHint` et transmettez-la pendant l’initialisation du client.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Output

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>Reconnaissance d’entité nommée (NER)

# <a name="version-31-preview"></a>[Version préliminaire de la version 3.1](#tab/version-3-1)

> [!NOTE]
> Dans la version `3.1` :
> * NER comprend des méthodes distinctes pour la détection d’informations personnelles. 
> * La liaison d’entités est une demande autre que NER.

Créez une fonction appelée `recognizeEntitiesExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `recognizeEntities()`. L’objet `CategorizedEntityCollection` retourné contient une liste de `CategorizedEntity` si l’opération réussit, ou un `errorMessage` si ce n’est pas le cas.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Output

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```


### <a name="personally-identifiable-information-recognition"></a>Reconnaissance des informations d’identification personnelle

Créez une fonction appelée `recognizePiiEntitiesExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `recognizePiiEntities()`. L’objet `PiiEntityCollection` retourné contient une liste de `PiiEntity` si l’opération réussit, ou un `errorMessage` si ce n’est pas le cas. Il contient également le texte rédigé, qui se compose du texte d’entrée, où toutes les entités identifiables sont remplacées par `*****`.

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Output

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

> [!NOTE]
> Dans la version `3.0` :
> * NER comprend des méthodes distinctes pour la détection d’informations personnelles. 
> * La liaison d’entités est une demande autre que NER.

Créez une fonction appelée `recognizeEntitiesExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `recognizeEntities()`. L’objet `CategorizedEntityCollection` retourné contient une liste de `CategorizedEntity` si l’opération réussit, ou un `errorMessage` si ce n’est pas le cas.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Output

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

---

## <a name="entity-linking"></a>Liaison d’entités

# <a name="version-31-preview"></a>[Version 3.1 preview](#tab/version-3-1)

Créez une fonction appelée `recognizeLinkedEntitiesExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `recognizeLinkedEntities()`. L’objet `LinkedEntityCollection` retourné contient une liste de `LinkedEntity` si l’opération réussit, ou un `errorMessage` si ce n’est pas le cas. Les entités liées étant identifiées de manière unique, les occurrences d’une même entité sont regroupées sous un objet `LinkedEntity` sous la forme d’une liste d’objets `LinkedEntityMatch`.


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```

# <a name="version-30-preview"></a>[Version 3.0 préversion](#tab/version-3)

Créez une fonction appelée `recognizeLinkedEntitiesExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `recognizeLinkedEntities()`. L’objet `LinkedEntityCollection` retourné contient une liste de `LinkedEntity` si l’opération réussit, ou un `errorMessage` si ce n’est pas le cas. Les entités liées étant identifiées de manière unique, les occurrences d’une même entité sont regroupées sous un objet `LinkedEntity` sous la forme d’une liste d’objets `LinkedEntityMatch`.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Créez une fonction appelée `extractKeyPhrasesExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `extractKeyPhrases()`. L’objet `ExtractKeyPhraseResult` retourné contient une liste d’expressions clés si l’opération réussit, ou un `errorMessage` si ce n’est pas le cas. Cet exemple est identique pour les versions 3.0 et 3.1 de l’API.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Output

```console
Recognized phrases: 
cat
veterinarian
```
---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Utiliser l’API de manière asynchrone avec l’opération d’analyse

# <a name="version-31-preview"></a>[Version préliminaire de la version 3.1](#tab/version-3-1)

Vous pouvez utiliser l’opération d’analyse pour effectuer des requêtes de lots asynchrones pour : la reconnaissance d’entité nommée (NER), l’extraction d’expressions clés, l’analyse des sentiments et la détection des informations d’identification personnelle (PII). Vous trouverez ci-dessous un exemple de base sur une opération. Vous trouverez un exemple plus élaboré [sur GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeActions.md)

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

Créez une fonction nommée `analyzeBatchActionsExample()` qui doit appeler la fonction `beginAnalyzeBatchActions()`. Il en résultera une opération durable qui sera interrogée pour obtenir des résultats.

```java
static void analyzeBatchActionsExample(TextAnalyticsClient client)
{
        List<TextDocumentInput> documents = Arrays.asList(
                        new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen.")
                        );

        
        SyncPoller<AnalyzeActionsOperationDetail, PagedIterable<AnalyzeActionsResult>> syncPoller =
                client.beginAnalyzeActions(documents,
                        new TextAnalyticsActions().setDisplayName("Analyze Batch Actions Quickstart")
                                .setRecognizeEntitiesOptions(new RecognizeEntitiesOptions()),
                        new AnalyzeActionsOptions().setIncludeStatistics(false),
                        Context.NONE);

        // Task operation statistics
        while (syncPoller.poll().getStatus() == LongRunningOperationStatus.IN_PROGRESS) {
            final AnalyzeActionsOperationDetail operationResult = syncPoller.poll().getValue();
            System.out.printf("Action display name: %s, Successfully completed actions: %d, in-process actions: %d, failed actions: %d, total actions: %d%n",
                    operationResult.getDisplayName(), operationResult.getActionsSucceeded(),
                    operationResult.getActionsInProgress(), operationResult.getActionsFailed(),
                    operationResult.getActionsInTotal());
        }

        syncPoller.waitForCompletion();

        Iterable<PagedResponse<AnalyzeActionsResult>> pagedResults = syncPoller.getFinalResult().iterableByPage();
        for (PagedResponse<AnalyzeActionsResult> page : pagedResults) {
            System.out.printf("Response code: %d, Continuation Token: %s.%n", page.getStatusCode(), page.getContinuationToken());
            page.getElements().forEach(analyzeActionsResult -> {
                System.out.println("Entities recognition action results:");
                IterableStream<RecognizeEntitiesActionResult> recognizeEntitiesActionResults =
                        analyzeActionsResult.getRecognizeEntitiesActionResults();
                if (recognizeEntitiesActionResults != null) {
                    recognizeEntitiesActionResults.forEach(actionResult -> {
                        if (!actionResult.isError()) {
                            // Recognized entities for each of documents from a batch of documents
                            AtomicInteger counter = new AtomicInteger();
                            for (RecognizeEntitiesResult documentResult : actionResult.getResult()) {
                                System.out.printf("%n%s%n", documents.get(counter.getAndIncrement()));
                                if (documentResult.isError()) {
                                    // Erroneous document
                                    System.out.printf("Cannot recognize entities. Error: %s%n",
                                            documentResult.getError().getMessage());
                                } else {
                                    // Valid document
                                    documentResult.getEntities().forEach(entity -> System.out.printf(
                                            "Recognized entity: %s, entity category: %s, entity subcategory: %s, confidence score: %f.%n",
                                            entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
                                }
                            }
                        } else {
                            TextAnalyticsError actionError = actionResult.getError();
                            // Erroneous action
                            System.out.printf("Cannot execute Entities Recognition action. Error: %s%n", actionError.getMessage());
                        }
                    });
                }
            });
        }
    }
```

Après avoir ajouté cet exemple à votre application, appelez-le dans votre méthode `main()`.

```java
analyzeBatchActionsExample(client);
```

### <a name="output"></a>Sortie

```console
Action display name: Analyze Batch Actions Quickstart, Successfully completed actions: 0, in-process actions: 1, failed actions: 0, total actions: 1
Response code: 200, Continuation Token: null.
Entities recognition action results:

Text = Microsoft was founded by Bill Gates and Paul Allen., Id = 0, Language = null
Recognized entity: Microsoft, entity category: Organization, entity subcategory: null, confidence score: 0.970000.
Recognized entity: Bill Gates, entity category: Person, entity subcategory: null, confidence score: 1.000000.
Recognized entity: Paul Allen, entity category: Person, entity subcategory: null, confidence score: 0.990000.
```

Vous pouvez également utiliser l’opération d’analyse pour la reconnaissance d’entité nommée (NER), l’extraction d’expressions clés, l’analyse des sentiments et la détection des informations d’identification personnelle (PII). Consultez l’[exemple d’analyse](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeActionsAsync.java) sur GitHub.

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

Cette fonctionnalité n’est pas disponible dans la version 3.0.

---
