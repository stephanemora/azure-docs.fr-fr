---
title: Compétence cognitive Détection de la langue
titleSuffix: Azure Cognitive Search
description: Évalue le texte non structuré et, pour chaque enregistrement, retourne un identificateur de langue avec un score indiquant la puissance de l’analyse dans un pipeline d’enrichissement par IA dans Recherche cognitive Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: fbe2c9a66015886c96a7c4414eb3b425b8180a18
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111539776"
---
#   <a name="language-detection-cognitive-skill"></a>Compétence cognitive Détection de la langue

La compétence **Détection de langue** détecte la langue du texte d’entrée et renvoie un code de langue unique pour chaque document soumis dans la requête. Le code de langue est associé à un score indiquant la puissance de l’analyse. Cette compétence utilise les modèles Machine Learning fournis par [Analyse de texte](../cognitive-services/text-analytics/overview.md) dans Cognitive Services.

Cette fonctionnalité est particulièrement utile lorsqu’il est nécessaire d’indiquer la langue du texte en entrée dans d’autres compétences (par exemple, la [compétence Analyse des sentiments](cognitive-search-skill-sentiment-v3.md) ou la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md)).

La détection de la langue s’appuie sur les bibliothèques de traitement en langage naturel de Bing, qui dépassent le nombre de [langues et régions prises en charge](../cognitive-services/text-analytics/language-support.md) répertoriées pour le service Analyse de texte. La liste exacte des langues n’est pas publiée, mais inclut toutes les langues courantes, ainsi que les variantes, dialectes et certaines langues régionales et culturelles. Si vous avez du contenu exprimé dans une langue moins fréquemment utilisée, vous pouvez [essayer l’API Détection de langue](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) pour voir si elle retourne un code. La réponse pour les langues qui ne peuvent pas être détectées est `(Unknown)`.

> [!NOTE]
> Si vous élargissez le champ en augmentant la fréquence des traitements, en ajoutant des documents supplémentaires ou en ajoutant plusieurs algorithmes d’IA, vous devez [attacher une ressource Cognitive Services facturable](cognitive-search-attach-cognitive-services.md). Des frais s’appliquent durant l’appel des API dans Cognitive Services ainsi que pour l’extraction d’images dans le cadre de la phase de craquage de document de la Recherche cognitive Azure. L’extraction de texte à partir des documents est gratuite.
>
> L'exécution des compétences intégrées est facturée au prix actuel du [paiement à l'utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les prix appliqués pour l’extraction d’images sont présentés sur la [page de tarification du service Recherche cognitive Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limites de données
La taille maximale d’un enregistrement doit être de 50 000 caractères telle que mesurée par [`String.Length`](/dotnet/api/system.string.length). Si vous avez besoin de découper vos données avant de les envoyer à la compétence de détection de langage, vous pouvez utiliser la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.

| Entrées | Description |
|---------------------|-------------|
| `defaultCountryHint` | (Facultatif) Un code de pays à deux lettres ISO 3166-1 alpha-2 peut être fourni comme indicateur pour le modèle de détection de langue s’il ne peut pas lever l’ambiguïté de la langue. Pour plus d’informations, consultez la [documentation d’Analyse de texte](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) dans cette rubrique. Plus précisément, le paramètre `defaultCountryHint` est utilisé avec les documents qui ne spécifient pas l’entrée `countryHint` explicitement.  |
| `modelVersion`   | (Facultatif) Version du modèle à utiliser lors de l’appel du service Analyse de texte. Si rien n’est spécifié, la dernière version disponible est utilisée par défaut. Nous vous recommandons de ne pas spécifier cette valeur, sauf nécessité absolue. Pour plus d’informations, consultez [Contrôle de version de modèle dans l’API Analyse de texte](../cognitive-services/text-analytics/concepts/model-versioning.md). |

## <a name="skill-inputs"></a>Entrées de la compétence

Les paramètres respectent la casse.

| Entrées     | Description |
|--------------------|-------------|
| `text` | Texte à analyser.|
| `countryHint` | Code de pays à deux lettres ISO 3166-1 alpha-2 à utiliser comme indicateur pour le modèle de détection de langue s’il ne peut pas lever l’ambiguïté de la langue. Pour plus d’informations, consultez la [documentation d’Analyse de texte](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) dans cette rubrique. |

## <a name="skill-outputs"></a>Sorties de la compétence

| Nom de sortie    | Description |
|--------------------|-------------|
| `languageCode` | Code ISO 6391 de la langue identifiée. Exemple : « en ». |
| `languageName` | Nom de la langue. Exemple : « anglais ». |
| `score` | Valeur comprise entre 0 et 1 correspondant à la probabilité que la langue soit correctement identifiée. Le score peut être inférieur à 1 si la phrase comporte plusieurs langues.  |

##  <a name="sample-definition"></a>Exemple de définition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Exemple d’entrée

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>Exemple de sortie

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)