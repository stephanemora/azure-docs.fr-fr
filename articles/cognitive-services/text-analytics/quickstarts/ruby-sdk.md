---
title: 'Démarrage rapide : Utilisation du SDK Ruby pour appeler le service cognitif Analyse de texte'
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations et des exemples de code pour vous aider à commencer à utiliser rapidement l’API Analyse de texte dans Azure Cognitive Services.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 7def77c0b1cf99fcc2cee77a28782dddaf2ac45d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992925"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>Démarrage rapide : Utilisation du SDK Ruby pour appeler le service Analyse de texte

<a name="HOLTop"></a>


Utilisez ce démarrage rapide pour commencer l’analyse du langage avec le SDK Analyse de texte pour Ruby. Si l’API REST [Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759711) est compatible avec la plupart des langages de programmation, le kit SDK offre quant à lui un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb).

Consultez les [définitions des API](//go.microsoft.com/fwlink/?LinkID=759346) pour accéder à la documentation technique des API.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Il vous faut également [le point de terminaison et la clé d’accès](../How-tos/text-analytics-how-to-access-key.md) générée pendant le processus d’inscription. 

[Ici](https://rubyinstaller.org/downloads/), téléchargez et installez la version 2.5.5 de Ruby selon l’architecture de votre système.

> [!Tip]
>  Alors que vous pouvez appeler [les points de terminaison API REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) directement avec Ruby, le SDK `Microsoft.Azure.CognitiveServices.TextAnalytics` permet d’appeler facilement le service sans sérialisation ni désérialisation JSON.
>
> Quelques liens utiles :
> - [Page Ruby gem](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
> - [Code SDK sur GitHub](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics/lib/v2.1/generated/azure_cognitiveservices_textanalytics)

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Créer un projet Ruby et installer le SDK

1. Créez un projet Ruby et ajoutez un nouveau fichier nommé `Gemfile`.
2. Ajoutez le SDK Analyse de texte au projet en ajoutant le code ci-dessous à `Gemfile`.

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>Créer un client Analyse de texte

1. Dans votre éditeur ou environnement de développement intégré favori, créez un fichier nommé `TextAnalyticsExamples.rb`. Importez le SDK Analyse de texte.

2. Un objet informations d’identification est utilisé par le client Analyse de texte. Créez-le avec `CognitiveServicesCredentials.new()` et en passant votre clé d’abonnement.

3. Créez le client avec votre point de terminaison Analyse de texte approprié.

    ```ruby
    require 'azure_cognitiveservices_textanalytics'
    
    include Azure::CognitiveServices::TextAnalytics::V2_1::Models
    
    credentials =
        MsRestAzure::CognitiveServicesCredentials.new("enter key here")
    # Replace 'westus' with the correct region for your Text Analytics subscription
    endpoint = String.new("https://westus.api.cognitive.microsoft.com/")
    
    textAnalyticsClient =
        Azure::TextAnalytics::Profiles::Latest::Client.new({
            credentials: credentials
        })
    textAnalyticsClient.endpoint = endpoint
    ```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>analyse de sentiments

À l’aide de l’API ou du SDK Analyse de texte, vous pouvez effectuer des analyses de sentiments sur un ensemble d’enregistrements texte. L’exemple suivant affiche les scores de sentiment pour plusieurs documents.

1. Créez une fonction appelée `SentimentAnalysisExample()` qui accepte le client Analyse de texte créé ci-dessus en tant que paramètre.

2. Définissez un ensemble d’objets `MultiLanguageInput` à analyser. Ajoutez une langue et du texte pour chaque objet. Vous pouvez utiliser n’importe quel ID.

    ```ruby
    def SentimentAnalysisExample(client)
      # The documents to be analyzed. Add the language of the document. The ID can be any value.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'en'
      input_1.text = 'I had the best day of my life.'
    
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'en'
      input_2.text = 'This was a waste of my time. The speaker put me to sleep.'
    
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'es'
      input_3.text = 'No tengo dinero ni nada que dar...'
    
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'it'
      input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    ```

3. Dans la même fonction, regroupez les documents dans une liste. Ajoutez-la au champ `documents` d’un objet `MultiLanguageBatchInput`. 

4. Appelez la fonction `sentiment()` du client avec l’objet `MultiLanguageBatchInput` en tant que paramètre pour envoyer les documents. Si des résultats sont retournés, imprimez-les.
    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.sentiment(
          multi_language_batch_input: input_documents
      )
      
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
          puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
      end
    end
    ```

5. Appelez la fonction `SentimentAnalysisExample()`.

    ```ruby
    SentimentAnalysisExample(textAnalyticsClient)
    ```

### <a name="output"></a>Sortie

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Détection de la langue

Le service Analyse de texte peut détecter la langue d’un document texte parmi un large éventail de langues et dialectes. L’exemple suivant affiche la langue dans laquelle ont été écrits plusieurs documents.

1. Créez une fonction appelée `DetectLanguageExample()` qui accepte le client Analyse de texte créé ci-dessus en tant que paramètre. 

2. Définissez un ensemble d’objets `LanguageInput` à analyser. Ajoutez une langue et du texte pour chaque objet. Vous pouvez utiliser n’importe quel ID.

    ```ruby
    def DetectLanguageExample(client)
       # The documents to be analyzed.
       language_input_1 = LanguageInput.new
       language_input_1.id = '1'
       language_input_1.text = 'This is a document written in English.'
    
       language_input_2 = LanguageInput.new
       language_input_2.id = '2'
       language_input_2.text = 'Este es un document escrito en Español..'
    
       language_input_3 = LanguageInput.new
       language_input_3.id = '3'
       language_input_3.text = '这是一个用中文写的文件'
    ```

3. Dans la même fonction, regroupez les documents dans une liste. Ajoutez-la au champ `documents` d’un objet `LanguageBatchInput`. 

4. Appelez la fonction `detect_language()` du client avec l’objet `LanguageBatchInput` en tant que paramètre pour envoyer les documents. Si des résultats sont retournés, imprimez-les.
    ```ruby
       input_documents = LanguageBatchInput.new
       input_documents.documents = [language_input_1, language_input_2, language_input_3]
    
    
       result = client.detect_language(
           language_batch_input: input_documents
       )
    
       if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
         puts '===== LANGUAGE DETECTION ====='
         result.documents.each do |document|
           puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
         end
       else
         puts 'No results data..'
       end
     end
    ```

5. Appeler la fonction `DetectLanguageExample`

    ```ruby
    DetectLanguageExample(textAnalyticsClient)
    ```

### <a name="output"></a>Sortie

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Reconnaissance d’entité

Le service Analyse de texte peut différencier et extraire différentes entités (personnes, lieux et objets) dans des documents texte. L’exemple suivant affiche les entités trouvées dans plusieurs exemples de documents.

1. Créez une fonction appelée `Recognize_Entities()` qui accepte le client Analyse de texte créé ci-dessus en tant que paramètre.

2. Définissez un ensemble d’objets `MultiLanguageInput` à analyser. Ajoutez une langue et du texte pour chaque objet. Vous pouvez utiliser n’importe quel ID.

    ```ruby
      def RecognizeEntitiesExample(client)
        # The documents to be analyzed.
        input_1 = MultiLanguageInput.new
        input_1.id = '1'
        input_1.language = 'en'
        input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'
    
        input_2 = MultiLanguageInput.new
        input_2.id = '2'
        input_2.language = 'es'
        input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. Dans la même fonction, regroupez les documents dans une liste. Ajoutez-la au champ `documents` d’un objet `MultiLanguageBatchInput`. 

4. Appelez la fonction `entities()` du client avec l’objet `MultiLanguageBatchInput` en tant que paramètre pour envoyer les documents. Si des résultats sont retournés, imprimez-les.

    ```ruby
        input_documents =  MultiLanguageBatchInput.new
        input_documents.documents = [input_1, input_2]
    
        result = client.entities(
            multi_language_batch_input: input_documents
        )
    
        if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
          puts '===== ENTITY RECOGNITION ====='
          result.documents.each do |document|
            puts "Document ID: #{document.id}"
              document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                  puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
              end
          end
        else
          puts 'No results data..'
        end
      end
    ```

5. Appeler la fonction `RecognizeEntitiesExample`
    ```ruby
    RecognizeEntitiesExample(textAnalyticsClient)
    ```

### <a name="output"></a>Sortie

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Le service Analyse de texte peut extraire des expressions clés dans des phrases. L’exemple suivant affiche les entités trouvées dans plusieurs exemples de documents dans plusieurs langues.

1. Créez une fonction appelée `KeyPhraseExtractionExample()` qui accepte le client Analyse de texte créé ci-dessus en tant que paramètre.

2. Définissez un ensemble d’objets `MultiLanguageInput` à analyser. Ajoutez une langue et du texte pour chaque objet. Vous pouvez utiliser n’importe quel ID.

    ```ruby
    def KeyPhraseExtractionExample(client)
      # The documents to be analyzed.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'ja'
      input_1.text = '猫は幸せ'
  
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'de'
      input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'
  
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'en'
      input_3.text = 'My cat is stiff as a rock.'
  
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'es'
      input_4.text = 'A mi me encanta el fútbol!'
      ```

3. Dans la même fonction, regroupez les documents dans une liste. Ajoutez-la au champ `documents` d’un objet `MultiLanguageBatchInput`. 

4. Appelez la fonction `key_phrases()` du client avec l’objet `MultiLanguageBatchInput` en tant que paramètre pour envoyer les documents. Si des résultats sont retournés, imprimez-les.

    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.key_phrases(
          multi_language_batch_input: input_documents
      )
    
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
          puts "Document Id: #{document.id}"
          puts '  Key Phrases'
          document.key_phrases.each do |key_phrase|
            puts "    #{key_phrase}"
          end
        end
      else
        puts 'No results data..'
      end
    end
    ```

5. Appeler la fonction `KeyPhraseExtractionExample`

    ```ruby
    KeyPhraseExtractionExample(textAnalyticsClient)
    ```

### <a name="output"></a>Sortie

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Voir aussi

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)
