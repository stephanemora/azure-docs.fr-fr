---
title: 'Démarrage rapide : Utiliser Java pour appeler l’API REST Analyse de texte'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment obtenir des informations et des exemples de code pour vous aider à commencer rapidement à utiliser l’API Analyse de texte dans Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: aahi
ms.custom: seo-java-july2019, seo-java-august2019
ms.openlocfilehash: d6278452eda0a6a043654d90519374a741caf76a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027983"
---
# <a name="quickstart-use-java-to-call-the-azure-text-analytics-cognitive-service"></a>Démarrage rapide : Utiliser Java pour appeler le service cognitif Analyse de texte d’Azure
<a name="HOLTop"></a>

Cet article montre comment [détecter la langue](#Detect), [analyser les sentiments](#SentimentAnalysis), [extraire les expressions clés](#KeyPhraseExtraction) et [identifier les entités liées](#Entities) à l’aide des [API Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759711) avec Java.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Vous devez également avoir le [point de terminaison et la clé d’accès](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) générée pendant le processus d’inscription.

<a name="Detect"></a>

## <a name="detect-language"></a>Détecter la langue

L’API Détection de langue détecte la langue d’un document texte à l’aide de la  [méthode Détecter la langue](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Créez un projet Java dans votre IDE favori (ou dans un nouveau dossier sur votre poste de travail). Créez une classe nommée `DetectLanguage.java`.
1. Ajoutez le code fourni ci-dessous à votre classe.
1. Copiez votre clé et votre point de terminaison Analyse de texte dans le code. 
1. Vérifiez que la bibliothèque [Gson](https://github.com/google/gson) est installée.
1. Exécutez le programme dans votre IDE ou utilisez la ligne de commande pour l’exécuter (il y a des instructions dans les commentaires du code).

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (DetectLanguage.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac DetectLanguage.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar DetectLanguage
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, text;

    public Document(String id, String text){
        this.id = id;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String text) {
        this.documents.add (new Document (id, text));
    }
}

public class DetectLanguage {
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key = "<paste-your-text-analytics-key-here>";
        endpoint = "<paste-your-text-analytics-endpoint-here>";
    }

    static String path = "/text/analytics/v3.0/languages";
    
    public static String GetLanguage (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Initialize();

            Documents documents = new Documents ();
            documents.add ("1", "This is a document written in English.");
            documents.add ("2", "Este es un document escrito en Español.");
            documents.add ("3", "这是一个用中文写的文件");

            String response = GetLanguage (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="language-detection-response"></a>Réponse de détection de langue

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analyser les sentiments

L’API Analyse des sentiments détecte les sentiments d’un ensemble d’enregistrements de texte à l’aide de la [méthode Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). L’exemple suivant évalue deux documents, l’un en anglais, l’autre en espagnol.

1. Créez un projet Java dans votre IDE favori (ou dans un nouveau dossier sur votre poste de travail). Créez-y une classe nommée `GetSentiment.java`.
1. Ajoutez le code fourni ci-dessous à votre classe.
1. Copiez votre clé et votre point de terminaison Analyse de texte dans le code.
1. Vérifiez que la bibliothèque [Gson](https://github.com/google/gson) est installée.
1. Exécutez le programme dans votre IDE ou utilisez la ligne de commande pour l’exécuter (il y a des instructions dans les commentaires du code).

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (GetSentiment.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac GetSentiment.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GetSentiment
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, language, text;

    public Document(String id, String language, String text){
        this.id = id;
        this.language = language;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String language, String text) {
        this.documents.add (new Document (id, language, text));
    }
}

public class GetSentiment {
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key = "<paste-your-text-analytics-key-here>";
        endpoint = "<paste-your-text-analytics-endpoint-here>";
    }

    static String path = "/text/analytics/v3.0/sentiment";
    
    public static String getTheSentiment (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Initialize();

            Documents documents = new Documents ();
            documents.add ("1", "en", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.");
            documents.add ("2", "es", "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.");

            String response = getTheSentiment (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="sentiment-analysis-response"></a>Réponse d’analyse des sentiments

Le résultat est mesuré comme positif si son score est plus proche de 1,0 et négatif s’il est plus proche de 0,0.
Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant :

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extraire des expressions clés

L’API Extraction de phrases clés extrait des phrases clés d’un document texte à l’aide de la [méthode Phrases clés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). L’exemple suivant extrait les phrases clés des documents en anglais et en espagnol.

1. Créez un projet Java dans votre IDE favori (ou dans un nouveau dossier sur votre poste de travail). Créez-y une classe nommée `GetKeyPhrases.java`.
1. Ajoutez le code fourni ci-dessous à votre classe.
1. Copiez votre clé et votre point de terminaison Analyse de texte dans le code. 
1. Vérifiez que la bibliothèque [Gson](https://github.com/google/gson) est installée.
1. Exécutez le programme dans votre IDE ou utilisez la ligne de commande pour l’exécuter (il y a des instructions dans les commentaires du code).

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (GetKeyPhrases.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac GetKeyPhrases.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GetKeyPhrases
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, language, text;

    public Document(String id, String language, String text){
        this.id = id;
        this.language = language;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String language, String text) {
        this.documents.add (new Document (id, language, text));
    }
}

public class GetKeyPhrases {
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key = "<paste-your-text-analytics-key-here>";
        endpoint = "<paste-your-text-analytics-endpoint-here>";
    }

    static String path = "/text/analytics/v3.0/keyPhrases";
    
    public static String GetKeyPhrases (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Initialize();

            Documents documents = new Documents ();
            documents.add ("1", "en", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.");
            documents.add ("2", "es", "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.");
            documents.add ("3", "en", "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen.");

            String response = GetKeyPhrases (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="key-phrase-extraction-response"></a>Réponse d’extraction d’expressions clés

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant :

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```
<a name="Entities"></a>

## <a name="identify-entities"></a>Identification d’entités

L’API Entités identifie les entités bien connues dans un document texte à l’aide de la [méthode Entités](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634). Les [entités](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) extraient les mots du texte, comme « United States », puis vous donnent le type et/ou un lien Wikipédia pour ce ou ces mots. Le type pour « United States » est `location`, alors que le lien Wikipédia est `https://en.wikipedia.org/wiki/United_States`.  L’exemple suivant identifie les entités dans les documents en anglais.

1. Créez un projet Java dans votre IDE favori (ou dans un nouveau dossier sur votre poste de travail). Créez-y une classe nommée `GetEntities.java`.
1. Ajoutez le code fourni ci-dessous à votre classe.
1. Copiez votre clé et votre point de terminaison Analyse de texte dans le code. 
1. Vérifiez que la bibliothèque [Gson](https://github.com/google/gson) est installée.
1. Exécutez le programme dans votre IDE ou utilisez la ligne de commande pour l’exécuter (il y a des instructions dans les commentaires du code).

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (GetEntities.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac GetEntities.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GetEntities
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, language, text;

    public Document(String id, String language, String text){
        this.id = id;
        this.language = language;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String language, String text) {
        this.documents.add (new Document (id, language, text));
    }
}

public class GetEntities {
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key = "<paste-your-text-analytics-key-here>";
        endpoint = "<paste-your-text-analytics-endpoint-here>";
    }

    static String path = "/text/analytics/v3.0/entities/recognition/general";
    
    public static String GetEntities (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Initialize();

            Documents documents = new Documents ();
            documents.add ("1", "en", "Microsoft is an It company.");

            String response = GetEntities (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="entity-extraction-response"></a>Réponse d’extraction d’entité

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant :

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Voir aussi

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)
