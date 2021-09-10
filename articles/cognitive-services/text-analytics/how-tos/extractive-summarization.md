---
title: Résumer le texte à l’aide de l’API de résumé extractif Analyse de texte
titleSuffix: Azure Cognitive Services
description: Cet article vous montre comment résumer un texte avec l’API de résumé extractif Analyse de texte d’Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: aahi
ms.openlocfilehash: f8db386da9890be2a07f201243dbbb4beaf5c499
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122535225"
---
# <a name="how-to-summarize-text-with-text-analytics-preview"></a>Guide pratique pour synthétiser du texte à l’aide d’Analyse de texte (préversion)

> [!IMPORTANT] 
> Le résumé extractif d’Analyse de texte est une capacité en préversion fournie « EN L’ÉTAT » et « AVEC TOUS LES DÉFAUTS ». Par conséquent, le résumé extractif d’Analyse de texte (préversion) ne doit pas être implémenté ni déployé dans le cadre d’une utilisation en production. Le client est seul responsable de toute utilisation du résumé extractif d’Analyse de texte. 

En général, il existe deux approches pour le résumé automatique de texte : extractif et abstractif. L’API Analyse de texte fournit un résumé extractif à partir de la version `3.2-preview.1`.

Le résumé extractif est une fonctionnalité d’Analyse de texte Azure qui produit un résumé en extrayant les phrases qui représentent collectivement les informations les plus importantes ou les plus pertinentes dans le contenu d’origine.

Cette fonctionnalité est conçue pour raccourcir le contenu que les utilisateurs considèrent comme trop long à lire. Le résumé extractif condense des articles, des publications ou des documents en phrases clés.

Les modèles IA utilisés par l’API sont fournis par le service ; vous n’avez qu’à envoyer le contenu à analyser.

## <a name="extractive-summarization-and-features"></a>Résumé extractif et fonctionnalités

La fonctionnalité de résumé extractif dans Analyse de texte utilise des techniques de traitement du langage naturel pour localiser les phrases clés d’un document texte non structuré. Ces phrases transmettent collectivement l’idée principale du document.

Le résumé extractif renvoie un score de classement dans le cadre de la réponse du système, ainsi que les phrases extraites et leur position dans les documents d’origine. Un score de classement est un indicateur de la pertinence d’une phrase par rapport à l’idée principale d’un document. Le modèle attribue un score compris entre 0 et 1 (inclus) à chaque phrase et renvoie les phrases les mieux notées par requête. Par exemple, si vous demandez un résumé de trois phrases, le service renvoie les trois phrases les mieux notées.

Une autre fonctionnalité d’Analyse de texte, l’[extraction de phrases clés](./text-analytics-how-to-keyword-extraction.md), permet d’extraire des informations essentielles. Pour choisir entre l’extraction de phrases clés et le résumé extractif, tenez compte des points suivants :
* L’extraction de phrases clés renvoie des expressions alors que le résumé extractif renvoie des phrases.
* Le résumé extractif renvoie des phrases avec un score de classement. Les phrases les mieux classées seront renvoyées par requête.

## <a name="sending-a-rest-api-request"></a>Envoie d’une requête d’API REST 

> [!TIP]
> Vous pouvez également utiliser la préversion la plus récente de la bibliothèque de client pour utiliser le résumé extractif. Consultez les exemples suivants sur GitHub. 
> * [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample8_ExtractSummary.md)
> * [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeExtractiveSummarization.java)
> * [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_extract_summary.py)

### <a name="preparation"></a>Préparation

Contrairement à d’autres fonctionnalités d’Analyse de texte, la fonctionnalité de résumé extractif est une opération asynchrone uniquement, à laquelle vous pouvez accéder via le point de terminaison /analyze. Les données de la requête JSON doivent respecter le format décrit dans [Demandes asynchrones adressées au point de terminaison /analyze](./text-analytics-how-to-call-api.md?tabs=asynchronous#api-request-formats).

Le résumé extractif prend en charge un large éventail de langues pour l’entrée de documents. Pour en savoir plus, consultez [Langages pris en charge](../language-support.md).

La taille du document doit être inférieure à 125 000 caractères par document. Pour connaître le nombre maximal de documents autorisés dans une collection, consultez l’article [Limites de données](../concepts/data-limits.md?tabs=version-3). La collection est soumise dans le corps de la demande.

### <a name="structure-the-request"></a>Structurer la requête

Créez une requête POST. Vous pouvez [utiliser Postman](text-analytics-how-to-call-api.md) ou la **console de test d’API** via le lien de référence suivant pour en structurer une rapidement et l’envoyer. 

[Référence pour le résumé de texte](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-2-preview-1/operations/Analyze)

### <a name="request-endpoints"></a>Points de terminaison de requête

Définissez le point de terminaison HTTPS pour le résumé extractif en utilisant une ressource Analyse de texte sur Azure. Par exemple :

> [!NOTE]
> Vous pouvez trouver la clé et le point de terminaison pour votre ressource Analyse de texte dans le portail Azure. Ces informations se trouvent dans la page **Clé et point de terminaison** de la ressource. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze`

Définissez un en-tête de requête pour inclure votre clé d’API Analyse de texte. Dans le corps de la demande, fournissez la collection de documents JSON que vous avez préparée pour cette analyse.

### <a name="example-request"></a>Exemple de requête 

Voici un exemple de contenu que vous pourriez soumettre pour résumé, qui est extrait en utilisant une [représentation holistique en vue d’une intelligence artificielle intégrative](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/) à des fins de démonstration. L’API de résumé extractif peut accepter un texte d’entrée beaucoup plus long. Pour plus d’informations sur les limites de données de l’API Analyse de texte, consultez [Limites de données](../Concepts/data-limits.md).

Une requête peut inclure plusieurs documents. 

Chaque document possède les paramètres suivants :
* `id` pour identifier un document ; 
* `language` pour indiquer la langue source du document, `en` étant la valeur par défaut ;
* `text` pour joindre le texte du document.

Tous les documents d’une même requête partagent les paramètres suivants. Ces paramètres peuvent être spécifiés dans la définition `tasks` de la requête.
* `model-version` pour indiquer la version du modèle à utiliser, `latest` étant la valeur par défaut. Pour plus d’informations, consultez [Version de modèle](../concepts/model-versioning.md). 
* `sentenceCount` pour spécifier le nombre de phrases à renvoyer, `3` étant la valeur par défaut. La plage est comprise entre 1 et 20.
* `sortBy` pour spécifier l’ordre dans lequel les phrases extraites seront renvoyées. Les valeurs acceptées pour `sortBy` sont `Offset` et `Rank`, `Offset` étant la valeur par défaut. La valeur `Offset` correspond à la position de départ d’une phrase dans le document d’origine.    

```json
{
  "analysisInput": {
    "documents": [
      {
        "language": "en",
        "id": "1",
        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding. As Chief Technology Officer of Azure AI Cognitive Services, I have been working with a team of amazing scientists and engineers to turn this quest into a reality. In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z). At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better. We believe XYZ-code will enable us to fulfill our long-term vision: cross-domain transfer learning, spanning modalities and languages. The goal is to have pretrained models that can jointly learn representations to support a broad range of downstream AI tasks, much in the way humans do today. Over the past five years, we have achieved human performance on benchmarks in conversational speech recognition, machine translation, conversational question answering, machine reading comprehension, and image captioning. These five breakthroughs provided us with strong signals toward our more ambitious aspiration to produce a leap in AI capabilities, achieving multisensory and multilingual learning that is closer in line with how humans learn and understand. I believe the joint XYZ-code is a foundational component of this aspiration, if grounded with external knowledge sources in the downstream AI tasks."
      }
    ]
  },
  "tasks": {
    "extractiveSummarizationTasks": [
      {
        "parameters": {
          "model-version": "latest",
          "sentenceCount": 3,
          "sortBy": "Offset"
        }
      }
    ]
  }
}
```

### <a name="post-the-request"></a>Publier la requête

L’API de résumé extractif est exécutée dès réception de la requête. Pour connaître la taille et le nombre de requêtes que vous pouvez envoyer par minute et seconde, consultez la section [Limites de données](../overview.md#data-limits) dans la vue d’ensemble.

L’API de résumé extractif d’Analyse de texte est une API asynchrone, il n’y a donc pas de texte dans l’objet de réponse. Toutefois, vous avez besoin de la clé `operation-location` dans les en-têtes de réponse pour effectuer une requête GET afin de vérifier l’état du travail et la sortie.  Voici un exemple de valeur operation-location KEY dans l’en-tête de réponse de la requête POST :

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze/jobs/<jobID>`

Pour vérifier l’état du travail, faites une requête GET à l’URL dans la valeur de l’en-tête operation-location KEY de la réponse POST.  Les états suivants sont utilisés pour refléter l’état d’un travail : `NotStarted`, `running`, `succeeded`, `failed` ou `rejected`.  

Si la tâche a réussi, la sortie de l’API est renvoyée dans le corps de la requête GET. 


### <a name="view-the-results"></a>View the results

Voici un exemple de réponse d’une requête GET.  La sortie est disponible et récupérable jusqu’à ce que le délai `expirationDateTime` (24 heures à partir de l’heure de création du travail) soit écoulé, après quoi la sortie est purgée. En raison de la prise en charge multilingue et des émojis, la réponse peut contenir des décalages de texte. Pour plus d’informations, consultez le [guide pratique pour traiter les décalages](../concepts/text-offsets.md).

### <a name="example-response"></a>Exemple de réponse
  
La fonctionnalité de résumé extractif renvoie : 

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-06-11T05:43:37Z",
    "createdDateTime": "2021-06-11T05:42:32Z",
    "expirationDateTime": "2021-06-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "sentences": [
                    {
                        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding.",
                        "rankScore": 0.7673416137695312,
                        "Offset": 0,
                        "length": 160
                    },
                    {
                        "text": "In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z).",
                        "rankScore": 0.7644073963165283,
                        "Offset": 324,
                        "length": 192
                    },
                    {
                        "text": "At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better.",
                        "rankScore": 0.7623870968818665,
                        "Offset": 517,
                        "length": 203
                    }    
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-08-01"
    }
}
```

## <a name="summary"></a>Résumé

Dans cet article, vous avez appris les concepts et le flux de travail du résumé extractif à l’aide de l’API de résumé extractif Analyse de texte. Vous pouvez utiliser le résumé extractif pour :

* Faciliter le traitement des documents pour améliorer l’efficacité.
* Extraire des informations essentielles à partir de documents, de rapports et d’autres formes de texte volumineux.
* Mettre en évidence les phrases clés des documents.
* Parcourir rapidement des documents dans une bibliothèque.
* Générer le contenu d’un flux d’informations.

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Nouveautés](../whats-new.md)
* [Versions du modèle](../concepts/model-versioning.md)
