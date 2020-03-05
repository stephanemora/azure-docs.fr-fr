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
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 60f6443e041c2f3a76a9027bebd33dc29a07f445
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197468"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Procédure : Détecter les sentiments à l’aide de l’API Analyse de texte

La fonctionnalité Analyse des sentiments de l’API Analyse de texte évalue le texte et retourne des scores et des étiquettes de sentiment pour chaque phrase. Elle s’avère utile pour détecter les sentiments positifs ou négatifs dans les réseaux sociaux, les avis client, les forums de discussion, etc. Les modèles IA utilisés par l’API sont fournis par le service ; vous n’avez qu’à envoyer le contenu à analyser.

> [!TIP]
> Analyse de texte fournit également une image conteneur Docker basée sur Linux pour la détection de la langue. Vous pouvez ainsi [installer et exécuter le conteneur Analyse de texte](text-analytics-how-to-install-containers.md) à proximité de vos données.

Analyse des sentiments prend en charge une large palette de langues, et plus de langues encore en préversion. Pour en savoir plus, consultez [Langages pris en charge](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Concepts

L’API Analyse de texte utilise un algorithme de classification de machine learning pour générer un score de sentiment compris entre 0 et 1. Les scores proches de 1 indiquent un sentiment positif, tandis que les scores proches de 0 dénotent un sentiment négatif. L’analyse des sentiments porte sur l’ensemble du document, et non sur des parties individuelles du texte. Cela signifie que les scores de sentiment retournés se situent au niveau du document ou de la phrase. 

Le modèle utilisé est préentraîné avec un corpus étendu d’associations de texte et de sentiments. Il utilise une combinaison de techniques pour l’analyse, notamment le traitement de texte, l’analyse morphosyntaxique, le positionnement des mots et les associations de mots. Pour plus d’informations sur l’algorithme, consultez [Présentation d’Analyse de texte](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). Actuellement, vous ne pouvez pas fournir vos propres données d’entraînement. 

La précision des scores a tendance à s’améliorer quand les documents contiennent peu de phrases plutôt qu’un grand bloc de texte. Lors d’une phase d’évaluation d’objectivité, le modèle détermine si un document dans son ensemble est objectif ou s’il contient des sentiments. Un document principalement objectif n’évolue pas vers la phase de détection de sentiments, ce qui génère un score de 0,50 sans traitement supplémentaire. Pour les documents qui se poursuivent dans le pipeline, la phase suivante génère un score supérieur ou inférieur à 0,50. Le score dépend du degré de sentiment détecté dans le document.

## <a name="sentiment-analysis-versions-and-features"></a>Versions et fonctionnalités d’Analyse des sentiments

L’API Analyse de texte offre deux versions de l’Analyse des sentiments – v2 et v3. Analyse des sentiments v3 (préversion publique) apporte des améliorations significatives en terme de précision et de détail du score et de la catégorisation de texte de l’API.

> [!NOTE]
> * Analyse des sentiments v3 nécessite le même format de requête et les mêmes [limites de données](../overview.md#data-limits) que la version précédente.
> * Analyse des sentiments v3 est disponible dans les régions suivantes : `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` et `West US 2`.

| Fonctionnalité                                   | Analyse des sentiments v2 | Analyse des sentiments v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Méthodes pour les requêtes uniques et de lots    | X                     | X                     |
| Scores de sentiment pour le document entier  | X                     | X                     |
| Scores de sentiment pour les phrases individuelles |                       | X                     |
| Étiquetage des sentiments                        |                       | X                     |
| Gestion des versions des modèles                   |                       | X                     |

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

### <a name="sentiment-scoring"></a>Score de sentiment

Analyse des sentiments v3 classifie le texte avec des étiquettes de sentiment (décrit ci-dessous). Les scores retournés représentent le niveau de confiance du modèle pour ce qui est du caractère positif, négatif ou neutre du texte. Plus les valeurs sont élevées, plus le niveau de confiance est haut. 

### <a name="sentiment-labeling"></a>Étiquetage des sentiments

Analyse des sentiments v3 peut retourner des scores et des étiquettes au niveau du document et des phrases. Les scores et les étiquettes sont `positive`, `negative` et `neutral`. Au niveau du document, l’étiquette de sentiment `mixed` peut aussi être retournée sans score. Le sentiment du document est déterminé comme suit :

| Sentiment des phrases                                                                            | Étiquette de document retournée |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Le document contient au moins une phrase `positive`. Le reste des phrases est de type `neutral`. | `positive`              |
| Le document contient au moins une phrase `negative`. Le reste des phrases est de type `neutral`. | `negative`              |
| Le document contient au moins une phrase `negative` et au moins une phrase `positive`.    | `mixed`                 |
| Toutes les phrases du document sont de type `neutral`.                                                  | `neutral`               |

### <a name="model-versioning"></a>Gestion des versions des modèles

> [!NOTE]
> La gestion des versions des modèles pour l’analyse des sentiments est disponible à partir de la version `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Exemple de code C#

Vous trouverez un exemple d’application C# qui appelle cette version d’Analyse des sentiments sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs).


#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

### <a name="sentiment-scoring"></a>Score de sentiment

L’analyseur de sentiments classifie le texte comme principalement positif ou négatif. Il attribue un score compris entre 0 et 1. Les valeurs proches de 0,5 sont neutres ou indéterminées. Un score de 0,5 indique la neutralité. Quand une chaîne ne peut pas être analysée en ce qui concerne les sentiments ou ne présente pas de sentiments, le score est toujours exactement 0,5. Par exemple, si vous traitez une chaîne espagnole avec un code de langue anglaise, le score obtenu est 0,5.

---

## <a name="sending-a-rest-api-request"></a>Envoie d’une requête d’API REST 

### <a name="preparation"></a>Préparation

La qualité des résultats de l’analyse des sentiments est d’autant meilleure que vous lui donnez de petites quantités de texte à analyser. Au contraire, l’extraction d’expressions clés fonctionne mieux sur de plus grands blocs de texte. Pour obtenir des résultats optimaux pour ces deux opérations, envisagez de restructurer les entrées en conséquence.

Vous devez disposer des documents JSON dans ce format : ID, texte et langue.

La taille du document doit être inférieure à 5 120 caractères par document. Vous pouvez avoir jusqu’à 1000 éléments (ID) par collection. La collection est soumise dans le corps de la demande.

## <a name="structure-the-request"></a>Structurer la requête

Créez une requête POST. Vous pouvez [utiliser Postman](text-analytics-how-to-call-api.md) ou la **console de test d’API** via les liens de référence suivants pour en structurer une rapidement et l’envoyer. 

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

[Informations de référence sur Analyse des sentiments v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[Informations de référence sur Analyse des sentiments v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Définissez le point de terminaison HTTPS pour l’analyse des sentiments à l’aide d’une ressource Analyse de texte sur Azure ou d’un [conteneur Analyse de texte](text-analytics-how-to-install-containers.md) instancié. Vous devez inclure l’URL correspondant à la version que vous souhaitez utiliser. Par exemple :

> [!NOTE]
> Vous pouvez trouver votre clé et votre point de terminaison pour votre ressource Analyse de texte dans le portail Azure. Ces informations se trouvent dans la page **Démarrage rapide** de la ressource, sous **gestion des ressources**. 

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Définissez un en-tête de requête pour inclure votre clé d’API Analyse de texte. Dans le corps de la demande, fournissez la collection de documents JSON que vous avez préparée pour cette analyse.

### <a name="example-sentiment-analysis-request"></a>Exemple de requête Analyse des sentiments 

Voici un exemple de contenu que vous pouvez soumettre pour analyse des sentiments. Le format de la requête est le même pour les deux versions de l’API.
    
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

### <a name="post-the-request"></a>Publier la requête

L’analyse est effectuée à la réception de la demande. Pour connaître la taille et le nombre de requêtes que vous pouvez envoyer par minute et seconde, consultez la section [Limites de données](../overview.md#data-limits) dans la vue d’ensemble.

L’API Analyse de texte est sans état. Aucune donnée n’est stockée dans votre compte, et les résultats sont retournés immédiatement dans la réponse.


### <a name="view-the-results"></a>View the results

L’analyseur de sentiments classifie le texte comme principalement positif ou négatif. Il attribue un score compris entre 0 et 1. Les valeurs proches de 0,5 sont neutres ou indéterminées. Un score de 0,5 indique la neutralité. Quand une chaîne ne peut pas être analysée en ce qui concerne les sentiments ou ne présente pas de sentiments, le score est toujours exactement 0,5. Par exemple, si vous traitez une chaîne espagnole avec un code de langue anglaise, le score obtenu est 0,5.

La sortie est retournée immédiatement. Vous pouvez diffuser les résultats vers une application qui accepte JSON ou enregistrer la sortie dans un fichier sur le système local. Ensuite, importez la sortie dans une application que vous pouvez utiliser pour trier, rechercher et manipuler les données.

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Exemple de réponse d’Analyse des sentiments v3

Les réponses d’Analyse des sentiments v3 contiennent des étiquettes de sentiment pour chaque phrase et document analysés. `documentScores` n’est pas retourné si l’étiquette de sentiment du document est `mixed`.

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

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Exemple de réponse d’Analyse des sentiments v2

Les réponses d’Analyse des sentiments v2 contiennent des scores de sentiment pour chaque document envoyé.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert les concepts et le workflow de l’analyse des sentiments avec l’API Analyse de texte. En résumé :

+ Analyse des sentiments est disponible pour certaines langues dans deux versions.
+ Les documents JSON figurant dans le corps de la demande incluent un ID, un texte et un code de langue.
+ La requête POST s’effectue sur un point de terminaison `/sentiment`, à l’aide [d’une clé d’accès et d’un point de terminaison](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personnalisés valides pour votre abonnement.
+ La sortie de réponse, qui se compose d’un score de sentiment pour chaque ID de document, peut être diffusée vers n’importe quelle application qui accepte JSON, Par exemple, Excel et Power BI.

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Utilisation de la bibliothèque cliente Analyse de texte](../quickstarts/text-analytics-sdk.md)
* [Nouveautés](../whats-new.md)
