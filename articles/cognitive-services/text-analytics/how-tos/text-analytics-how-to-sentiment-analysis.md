---
title: Exécuter une analyse des sentiments avec l’API REST Analyse de texte
titleSuffix: Azure Cognitive Services
description: Cet article vous montre comment détecter les sentiments dans un texte avec l’API REST Analyse de texte d’Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: 33c9c708adcc196bc7d9b2e8a066d18e4dd20608
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326622"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Exemple : Détecter les sentiments avec Analyse de texte

L’[API Azure Analyse des sentiments](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) évalue le texte en entrée et retourne un score de sentiment pour chaque document. Les scores sont compris entre 0 (négatif) et 1 (positif).

Cette fonctionnalité est utile pour détecter des sentiments positifs ou négatifs dans les réseaux sociaux, les avis client et les forums de discussion. Le contenu est fourni par vous. Les modèles et les données d’entraînement sont fournis par le service.

Actuellement, l’API Analyse des sentiments prend en charge l’anglais, l’allemand, l’espagnol et le français. D’autres langues sont en préversion. Pour en savoir plus, consultez [Langages pris en charge](../text-analytics-supported-languages.md).

> [!TIP]
> L’API Azure Analyse de texte fournit également une image conteneur Docker basée sur Linux pour l’analyse des sentiments. Vous pouvez ainsi [installer et exécuter le conteneur Analyse de texte](text-analytics-how-to-install-containers.md) à proximité de vos données.

## <a name="concepts"></a>Concepts

L’API Analyse de texte utilise un algorithme de classification d’apprentissage automatique pour générer un score de sentiment compris entre 0 et 1. Les scores proches de 1 indiquent un sentiment positif, tandis que les scores proches de 0 dénotent un sentiment négatif. Le modèle est pré-entraîné avec un corps de texte complet contenant des associations de sentiments. Actuellement, vous ne pouvez pas fournir vos propres données d’entraînement. Le modèle utilise une combinaison de différentes techniques lors de l’analyse de texte, telles que le traitement du texte, l’analyse de la parole, l’emplacement des mots et les associations de mots. Pour plus d’informations sur l’algorithme, consultez [Présentation d’Analyse de texte](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

L’analyse des sentiments est effectuée sur l’ensemble du document, par opposition à l’extraction de sentiments pour une entité particulière dans le texte. Dans la pratique, la précision des scores a tendance à augmenter quand les documents contiennent une ou deux phrases plutôt qu’un grand bloc de texte. Lors d’une phase d’évaluation d’objectivité, le modèle détermine si un document dans son ensemble est objectif ou s’il contient des sentiments. Un document principalement objectif n’évolue pas vers la phase de détection de sentiments, ce qui génère un score de 0,50 sans traitement supplémentaire. Pour les documents qui se poursuivent dans le pipeline, la phase suivante génère un score supérieur ou inférieur à 0,50. Le score dépend du degré de sentiment détecté dans le document.

## <a name="sentiment-analysis-v3-public-preview"></a>Analyse des sentiments v3 - Préversion publique

La prochaine version d’Analyse des sentiments est désormais disponible en préversion publique. Elle apporte des améliorations significatives en matière de précision et de détail du score et de la catégorisation du texte de l’API. Essayez-la en utilisant la [console de test d’API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment).

> [!NOTE]
> * Analyse des sentiments v3 nécessite le même format de requête et les mêmes [limites de données](../overview.md#data-limits) que la version précédente.
> * À l’heure actuelle, Analyse des sentiments v3 :
>    * Prend actuellement en charge l’anglais (`en`), le japonais (`ja`), le chinois simplifié (`zh-Hans`), le chinois traditionnel (`zh-Hant`), le français (`fr`), l’italien (`it`), l’espagnol (`es`), le néerlandais (`nl`), le portugais (`pt`) et l’allemand (`de`).
>    * Est disponible dans les régions suivantes : `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` et `West US 2`.

|Fonctionnalité |Description  |
|---------|---------|
|Précision accrue     | Amélioration significative de la détection des sentiments positifs, neutres, négatifs et mixtes dans des documents texte par rapport aux versions précédentes.           |
|Score de sentiment au niveau du document et des phrases     | Détection du sentiment exprimé dans un document et dans ses différentes phrases. Si le document comprend plusieurs phrases, un score de sentiment est également attribué à chacune d’elles.         |
|Étiquetage et scoring des sentiments     | En plus d’un score de sentiment, l’API retourne désormais des catégories de sentiments pour le texte. Les catégories sont `positive`, `negative`, `neutral` et `mixed`.       |
| Sortie améliorée | Analyse des sentiments retourne désormais des informations à la fois pour un document texte entier et pour ses différentes phrases. |
| paramètre de version du modèle | Paramètre facultatif permettant de choisir quelle version du modèle Analyse de texte est utilisée sur vos données. |

### <a name="sentiment-labeling"></a>Étiquetage des sentiments

Analyse des sentiments v3 peut retourner des scores et des étiquettes au niveau du document et des phrases. Les scores et les étiquettes sont `positive`, `negative` et `neutral`. Au niveau du document, l’étiquette (pas le score) de sentiment `mixed` peut également être retournée. Le sentiment du document est déterminé en agrégeant les scores des phrases.

| Sentiment des phrases                                                        | Étiquette de document retournée |
|---------------------------------------------------------------------------|----------------|
| Au moins une phrase positive et le reste des phrases sont neutres. | `positive`     |
| Au moins une phrase négative et le reste des phrases sont neutres.  | `negative`     |
| Au moins une phrase négative et au moins une phrase positive.         | `mixed`        |
| Toutes les phrases sont neutres.                                                 | `neutral`      |

### <a name="model-versioning"></a>Gestion des versions des modèles

> [!NOTE]
> La gestion des versions des modèles pour l’analyse des sentiments est disponible à partir de la version `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="sentiment-analysis-v3-example-request"></a>Exemple de requête d’Analyse des sentiments v3

Le JSON suivant est un exemple de requête adressée à la nouvelle version d’Analyse des sentiments. Notez que la mise en forme de la requête est identique à celle de la version précédente :

```json
    {
        "documents": [
        {
            "language": "en",
            "id": "1",
            "text": "Hello world. This is some input text that I love."
        },
        {
            "language": "en",
            "id": "2",
            "text": "It's incredibly sunny outside! I'm so happy."
        }
        ],
    }
```

### <a name="sentiment-analysis-v3-example-response"></a>Exemple de réponse d’Analyse des sentiments v3

Alors que le format de la demande est identique à celui de la version précédente, le format de la réponse a changé. Le JSON suivant est un exemple de réponse de la nouvelle version de l’API :

```json
    {
        "documents": [
            {
                "id": "1",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.98570585250854492,
                    "neutral": 0.0001625834556762,
                    "negative": 0.0141316400840878
                },
                "sentences": [
                    {
                        "sentiment": "neutral",
                        "sentenceScores": {
                            "positive": 0.0785155147314072,
                            "neutral": 0.89702343940734863,
                            "negative": 0.0244610067456961
                        },
                        "offset": 0,
                        "length": 12
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.98570585250854492,
                            "neutral": 0.0001625834556762,
                            "negative": 0.0141316400840878
                        },
                        "offset": 13,
                        "length": 36
                    }
                ]
            },
            {
                "id": "2",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.89198976755142212,
                    "neutral": 0.103382371366024,
                    "negative": 0.0046278294175863
                },
                "sentences": [
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.78401315212249756,
                            "neutral": 0.2067587077617645,
                            "negative": 0.0092281140387058
                        },
                        "offset": 0,
                        "length": 30
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.99996638298034668,
                            "neutral": 0.0000060341349126,
                            "negative": 0.0000275444017461
                        },
                        "offset": 31,
                        "length": 13
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="example-c-code"></a>Exemple de code C#

Vous trouverez un exemple d’application C# qui appelle cette version d’Analyse des sentiments sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="preparation"></a>Préparation

L’analyse des sentiments produit un résultat de meilleure qualité quand vous lui donnez de petits segments de texte à analyser. Au contraire, l’extraction d’expressions clés fonctionne mieux sur de plus grands blocs de texte. Pour obtenir des résultats optimaux pour ces deux opérations, envisagez de restructurer les entrées en conséquence.

Vous devez disposer des documents JSON dans ce format : ID, texte et langue.

La taille du document doit être inférieure à 5 120 caractères par document. Vous pouvez avoir jusqu’à 1000 éléments (ID) par collection. La collection est soumise dans le corps de la demande. Voici un exemple de contenu que vous pouvez soumettre pour analyse des sentiments :

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Étape 1 : Structurer la requête

Pour plus d’informations sur la définition de la requête, consultez [Appeler l’API Analyse de texte](text-analytics-how-to-call-api.md). Les points suivants sont réaffirmés pour des raisons pratiques :

+ Créez une requête POST. Pour passer en revue la documentation de l’API pour cette requête, consultez [API Analyse des sentiments](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Définissez le point de terminaison HTTP pour l’analyse des sentiments à l’aide d’une ressource Analyse de texte sur Azure ou d’un [conteneur Analyse de texte](text-analytics-how-to-install-containers.md) instancié. Vous devez inclure `/text/analytics/v2.1/sentiment` dans l’URL. Par exemple : `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`.

+ Définissez un en-tête de requête pour inclure la [clé d’accès](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) des opérations Analyse de texte.

+ Dans le corps de la demande, fournissez la collection de documents JSON que vous avez préparée pour cette analyse.

> [!Tip]
> Utilisez [Postman](text-analytics-how-to-call-api.md) ou ouvrez la **console de test d’API** dans la [documentation](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) pour structurer la requête et la publier sur le service.

## <a name="step-2-post-the-request"></a>Étape 2 : Publier la requête

L’analyse est effectuée à la réception de la demande. Pour connaître la taille et le nombre de requêtes que vous pouvez envoyer par minute et seconde, consultez la section [Limites de données](../overview.md#data-limits) dans la vue d’ensemble.

Rappelez-vous que le service est sans état. Aucune donnée n’est stockée dans votre compte. Les résultats sont retournés immédiatement dans la réponse.


## <a name="step-3-view-the-results"></a>Étape 3 : View the results

L’analyseur de sentiments classifie le texte comme principalement positif ou négatif. Il attribue un score compris entre 0 et 1. Les valeurs proches de 0,5 sont neutres ou indéterminées. Un score de 0,5 indique la neutralité. Quand une chaîne ne peut pas être analysée en ce qui concerne les sentiments ou ne présente pas de sentiments, le score est toujours exactement 0,5. Par exemple, si vous traitez une chaîne espagnole avec un code de langue anglaise, le score obtenu est 0,5.

La sortie est retournée immédiatement. Vous pouvez diffuser les résultats vers une application qui accepte JSON ou enregistrer la sortie dans un fichier sur le système local. Ensuite, importez la sortie dans une application que vous pouvez utiliser pour trier, rechercher et manipuler les données.

L’exemple suivant montre la réponse pour la collection de documents dans cet article :

```json
    {
        "documents": [
            {
                "score": 0.9999237060546875,
                "id": "1"
            },
            {
                "score": 0.0000540316104888916,
                "id": "2"
            },
            {
                "score": 0.99990355968475342,
                "id": "3"
            },
            {
                "score": 0.980544924736023,
                "id": "4"
            },
            {
                "score": 0.99996328353881836,
                "id": "5"
            }
        ],
        "errors": []
    }
```

## <a name="summary"></a>Résumé

Dans cet article, vous avez vu les concepts et le flux de travail d’analyse des sentiments à l’aide de l’API Analyse de texte dans Azure Cognitive Services. En résumé :

+ L’[API Analyse des sentiments](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) est disponible pour certaines langues.
+ Les documents JSON figurant dans le corps de la demande incluent un ID, un texte et un code de langue.
+ La requête POST s’effectue sur un point de terminaison `/sentiment`, à l’aide [d’une clé d’accès et d’un point de terminaison](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personnalisés valides pour votre abonnement.
+ La sortie de réponse, qui se compose d’un score de sentiment pour chaque ID de document, peut être diffusée vers n’importe quelle application qui accepte JSON, par exemple Excel et Power BI, pour n’en citer que quelques-unes.

## <a name="see-also"></a>Voir aussi

 [Vue d’ensemble Analyse de texte](../overview.md) [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Page produit d’Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Extraire des expressions clés](text-analytics-how-to-keyword-extraction.md)
