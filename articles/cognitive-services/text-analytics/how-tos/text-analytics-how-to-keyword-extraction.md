---
title: Extraction de phrases clés à l’aide de l’API REST Analyse de texte
titleSuffix: Azure Cognitive Services
description: Comment extraire des phrases clés à l’aide de l’API REST Analyse de texte d’Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: ec5ff756d7e732430675676868bc754627a2a4a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72429031"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exemple : Comment extraire des phrases clés avec l’Analyse de texte

L’[API Extraction de phrases clés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) évalue un texte non structuré puis, pour chaque document JSON, retourne une liste d’expressions clés.

Cette fonctionnalité est utile si vous avez besoin d’identifier rapidement les principaux points d’une collection de documents. Par exemple, pour le texte d’entrée « The food was delicious and there were wonderful staff » (La nourriture était délicieuse et le personnel adorable), le service retourne les principaux points de discussion : « food » (nourriture) et « wonderful staff » (personnel adorable).

Pour en savoir plus, consultez [Langages pris en charge](../text-analytics-supported-languages.md).

> [!TIP]
> Analyse de texte fournit également une image conteneur Docker basée sur Linux pour l’extraction de phrases clés. Vous pouvez ainsi [installer et exécuter le conteneur Analyse de texte](text-analytics-how-to-install-containers.md) à proximité de vos données.

## <a name="preparation"></a>Préparation

L’extraction de phrases clés fonctionne mieux avec une plus grande quantité de texte. Contrairement à l’analyse des sentiments, qui fonctionne mieux sur des petites quantités de texte. Pour obtenir des résultats optimaux pour ces deux opérations, envisagez de restructurer les entrées en conséquence.

Vous devez disposer des documents JSON dans ce format : ID, texte, langue

La taille des documents doit être égale ou inférieure à 5 120 caractères par document, et vous pouvez avoir jusqu’à 1 000 éléments (ID) par collection. La collection est soumise dans le corps de la demande. L’exemple suivant illustre le contenu qui peut être soumis pour extraction d’expressions clés.

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

## <a name="step-1-structure-the-request"></a>Étape 1 : Structurer la requête

Pour en savoir plus sur la définition de la requête, voir [Comment appeler l’API REST Analyse de texte](text-analytics-how-to-call-api.md). Les points suivants sont réaffirmés pour des raisons pratiques :

+ Créez une demande **POST**. Passez en revue la documentation de l’API pour cette requête : [API d’expressions clés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Définissez le point de terminaison HTTP pour l’extraction de phrases clés à l’aide d’une ressource Analyse de texte sur Azure, ou avec un [conteneur Analyse de texte](text-analytics-how-to-install-containers.md) instancié. Vous devez inclure `/text/analytics/v2.1/keyPhrases` dans l’URL. Par exemple : `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`.

+ Définissez un en-tête de requête pour inclure la [clé d’accès](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) des opérations Analyse de texte.

+ Dans le corps de la demande, fournissez la collection de documents JSON que vous avez préparée pour cette analyse.

> [!Tip]
> Utilisez [Postman](text-analytics-how-to-call-api.md) ou ouvrez la **console de test d’API** dans la [documentation](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) pour structurer une demande et la publier dans le service.

## <a name="step-2-post-the-request"></a>Étape 2 : Publier la requête

L’analyse est effectuée à la réception de la demande. Pour connaître la taille et le nombre de requêtes que vous pouvez envoyer par minute et par seconde, consultez la section [Limites de données](../overview.md#data-limits) dans la vue d’ensemble.

Rappelez-vous que le service est sans état. Aucune donnée n’est stockée dans votre compte. Les résultats sont retournés immédiatement dans la réponse.

## <a name="step-3-view-results"></a>Étape 3 : Afficher les résultats

Toutes les demandes POST retournent une réponse au format JSON avec les ID et les propriétés détectées. L’ordre des expressions clés retournées est déterminé en interne par le modèle.

La sortie est retournée immédiatement. Vous pouvez diffuser en continu les résultats dans une application qui accepte le code JSON ou enregistrer la sortie dans un fichier sur le système local, puis l’importer dans une application qui vous permet de trier, rechercher et manipuler les données.

Voici un exemple de sortie pour l’extraction de phrases clés :

```json
    {
        "documents": [
            {
                "keyPhrases": [
                    "year",
                    "trail",
                    "trip",
                    "views"
                ],
                "id": "1"
            },
            {
                "keyPhrases": [
                    "marked trails",
                    "Worst hike",
                    "goners"
                ],
                "id": "2"
            },
            {
                "keyPhrases": [
                    "trail",
                    "small children",
                    "family"
                ],
                "id": "3"
            },
            {
                "keyPhrases": [
                    "spectacular views",
                    "trail",
                    "area"
                ],
                "id": "4"
            },
            {
                "keyPhrases": [
                    "places",
                    "beautiful views",
                    "favorite trail"
                ],
                "id": "5"
            }
        ],
        "errors": []
    }
```

Comme indiqué, l’analyseur recherche et ignore les mots non essentiels, et conserve les termes ou expressions uniques qui semblent constituer le sujet ou l’objet d’une phrase.

## <a name="summary"></a>Résumé

Dans cet article, vous avez vu les concepts et le flux de travail lié à l’extraction de phrases clés à l’aide de l’API Analyse de texte dans Cognitive Services. En résumé :

+ L’[API Extraction de phrases clés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) est disponible pour les langues sélectionnées.
+ Les documents JSON figurant dans le corps de la demande incluent un ID, un texte et un code de langue.
+ La demande POST s’effectue sur un point de terminaison `/keyphrases`, à l’aide [d’une clé d’accès et d’un point de terminaison](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personnalisés valides pour votre abonnement.
+ La sortie de réponse, qui se compose de mots et de phrases clés pour chaque ID de document, peut être diffusée vers n’importe quelle application qui accepte JSON, y compris Microsoft Office Excel et Power BI, pour n’en citer que quelques-unes.

## <a name="see-also"></a>Voir aussi

 [Vue d’ensemble Analyse de texte](../overview.md) [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Page produit d’Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API Analyse de texte](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
