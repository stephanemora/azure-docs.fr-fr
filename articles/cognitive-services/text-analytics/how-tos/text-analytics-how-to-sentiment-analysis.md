---
title: Guide pratique pour analyser les sentiments dans l’API REST Analyse de texte (Microsoft Cognitive Services sur Azure) | Microsoft Docs
description: Explique comment détecter des sentiments à l’aide de l’API REST Analyse de texte dans Microsoft Cognitive Services sur Azure dans ce tutoriel pas à pas.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: e5ceb6208229baf3e07e4030992d5de85309789c
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212492"
---
# <a name="example-how-to-detect-sentiment-in-text-analytics"></a>Exemple : Guide pratique pour détecter des sentiments dans Analyse de texte

L’[API Analyse des sentiments](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) évalue le texte en entrée et retourne un score de sentiment pour chaque document, entre 0 (négatif) et 1 (positif).

Cette fonctionnalité est utile pour détecter des sentiments positifs ou négatifs dans les réseaux sociaux, les avis client et les forums de discussion. Le contenu est fourni par vous. Les modèles et les données d’entraînement sont fournis par le service.

Actuellement, l’API Analyse des sentiments prend en charge l’anglais, l’allemand, l’espagnol et le français. D’autres langues sont en préversion. Pour en savoir plus, consultez [Langages pris en charge](../text-analytics-supported-languages.md).

> [!TIP]
> Analyse de texte fournit également une image conteneur Docker basée sur Linux pour l’analyse des sentiments. Vous pouvez ainsi [installer et exécuter le conteneur Analyse de texte](text-analytics-how-to-install-containers.md) à proximité de vos données.

## <a name="concepts"></a>Concepts

L’API Analyse de texte utilise un algorithme de classification d’apprentissage automatique pour générer un score de sentiment compris entre 0 et 1. Les scores proches de 1 indiquent un sentiment positif, tandis que les scores proches de 0 dénotent un sentiment négatif. Le modèle est pré-entraîné avec un corps de texte complet contenant des associations de sentiments. Actuellement, vous ne pouvez pas fournir vos propres données d’entraînement. Le modèle utilise une combinaison de techniques lors de l’analyse de texte, y compris le traitement de texte, l’analyse morphosyntaxique, le positionnement des mots et les associations de mots. Pour plus d’informations sur l’algorithme, consultez [Présentation d’Analyse de texte](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

L’analyse des sentiments est effectuée sur l’ensemble du document, par opposition à l’extraction de sentiments pour une entité particulière dans le texte. Dans la pratique, la précision des scores a tendance à augmenter lorsque les documents contiennent une ou deux phrases au lieu d’un grand bloc de texte. Lors d’une phase d’évaluation d’objectivité, le modèle détermine si un document dans son ensemble est objectif ou s’il contient des sentiments. Un document principalement objectif n’évolue pas vers une phase de détection de sentiments et obtient au final un score de 0,50, sans traitement supplémentaire. Pour les documents qui continuent dans le pipeline, la phase suivante génère un score supérieur ou inférieur à 0,50, selon le degré de sentiments détecté dans le document.

## <a name="preparation"></a>Préparation

L’analyse des sentiments produit un résultat de meilleure qualité lorsque vous lui donnez de petits segments de texte à analyser. Au contraire, l’extraction d’expressions clés fonctionne mieux sur de plus grands blocs de texte. Pour obtenir des résultats optimaux pour ces deux opérations, envisagez de restructurer les entrées en conséquence.

Vous devez disposer des documents JSON dans le format : id, texte, langue

La taille des documents doit être inférieure à 5 000 caractères par document et vous pouvez avoir jusqu'à 1 000 éléments (ID) par collection. La collection est soumise dans le corps de la demande. Voici un exemple de contenu que vous pouvez soumettre pour analyse des sentiments.

```
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

Vous trouverez plus d’informations sur la définition d’une demande dans [Guide pratique pour appeler l’API Analyse de texte](text-analytics-how-to-call-api.md). Les points suivants sont réaffirmés pour des raisons pratiques :

+ Créez une demande **POST**. Passez en revue la documentation de l’API pour cette requête : [API Analyse des sentiments](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)

+ Définissez le point de terminaison HTTP pour l’analyse des sentiments à l’aide d’une ressource Analyse de texte sur Azure ou d’un [conteneur Analyse de texte](text-analytics-how-to-install-containers.md) instancié. Il doit inclure la ressource `/sentiment` : `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`

+ Définissez un en-tête de demande pour inclure la clé d’accès pour les opérations d’Analyse de texte. Pour plus d’informations, consultez [Guide pratique pour rechercher des points de terminaison et des clés d’accès](text-analytics-how-to-access-key.md).

+ Dans le corps de la demande, fournissez la collection de documents JSON que vous avez préparée pour cette analyse.

> [!Tip]
> Utilisez [Postman](text-analytics-how-to-call-api.md) ou ouvrez la **console de test d’API** dans la [documentation](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) pour structurer la demande et la publier (POST) dans le service.

## <a name="step-2-post-the-request"></a>Étape 2 : Publier la requête

L’analyse est effectuée à la réception de la demande. Le service accepte jusqu'à 100 demandes par minute. Chaque demande peut être au maximum de 1 Mo.

Rappelez-vous que le service est sans état. Aucune donnée n’est stockée dans votre compte. Les résultats sont retournés immédiatement dans la réponse.


## <a name="step-3-view-results"></a>Étape 3 : Afficher les résultats

L’analyseur de sentiments classifie le texte comme principalement positif ou négatif, en affectant un score compris entre 0 et 1. Les valeurs proches de 0,5 sont neutres ou indéterminées. Un score de 0,5 indique la neutralité. Quand une chaîne ne peut pas être analysée en ce qui concerne les sentiments ou ne présente pas de sentiments, le score est toujours exactement 0,5. Par exemple, si vous traitez une chaîne espagnole avec un code de langue anglaise, le score obtenu est 0,5.

La sortie est retournée immédiatement. Vous pouvez diffuser en continu les résultats dans une application qui accepte le code JSON ou enregistrer la sortie dans un fichier sur le système local, puis l’importer dans une application qui vous permet de trier, rechercher et manipuler les données.

L’exemple suivant montre la réponse pour la collection de documents dans cet article.

```
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

Dans cet article, vous avez vu les concepts et le flux de travail d’analyse des sentiments à l’aide de l’API Analyse de texte dans Cognitive Services. En résumé :

+ L’[API Analyse des sentiments](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) est disponible pour les langues sélectionnées.
+ Les documents JSON figurant dans le corps de la demande incluent un id, un texte et un code de langue.
+ La demande POST s’effectue sur un point de terminaison `/sentiment`, à l’aide [d’une clé d’accès et d’un point de terminaison](text-analytics-how-to-access-key.md) personnalisés valides pour votre abonnement.
+ La sortie de réponse, qui se compose d’un score de sentiment pour chaque ID de document, peut être diffusée vers n’importe quelle application qui accepte JSON, y compris Excel et Power BI, pour n’en citer que quelques-unes.

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Page produit d’Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Extraire des expressions clés](text-analytics-how-to-keyword-extraction.md)
