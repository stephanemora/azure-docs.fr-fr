---
title: Extraction de phrases clés à l’aide de l’API REST Analyse de texte | Microsoft Docs
description: Comment extraire des phrases clés à l’aide de l’API REST Analyse de texte d’Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: 9cb6a40e6ce53a6d82d4129db876a1b24269f166
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997714"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exemple : Comment extraire des phrases clés avec l’Analyse de texte

L’[API Extraction de phrases clés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) évalue un texte non structuré puis, pour chaque document JSON, retourne une liste d’expressions clés. 

Cette fonctionnalité est utile si vous avez besoin d’identifier rapidement les principaux points d’une collection de documents. Par exemple, pour le texte d’entrée « The food was delicious and there were wonderful staff » (La nourriture était délicieuse et le personnel adorable), le service retourne les principaux points de discussion : « food » (nourriture) et « wonderful staff » (personnel adorable).

Actuellement, l’API Extraction de phrases clés prend en charge l’anglais, l’allemand, l’espagnol et le japonais. D’autres langues sont en préversion. Pour en savoir plus, consultez [Langages pris en charge](../text-analytics-supported-languages.md).

> [!TIP]
> Analyse de texte fournit également une image conteneur Docker basée sur Linux pour l’extraction de phrases clés. Vous pouvez ainsi [installer et exécuter le conteneur Analyse de texte](text-analytics-how-to-install-containers.md) à proximité de vos données.

## <a name="preparation"></a>Préparation

L’extraction de phrases clés fonctionne mieux avec une plus grande quantité de texte. Contrairement à l’analyse des sentiments, qui fonctionne mieux sur des petites quantités de texte. Pour obtenir des résultats optimaux pour ces deux opérations, envisagez de restructurer les entrées en conséquence.

Vous devez disposer de documents JSON dans le format : id, texte, langue

La taille des documents doit être inférieure à 5 120 caractères par document et vous pouvez avoir jusqu’à 1 000 éléments (ID) par collection. La collection est soumise dans le corps de la demande. L’exemple suivant illustre le contenu qui peut être soumis pour extraction d’expressions clés.

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

Vous trouverez plus d’informations sur la définition d’une demande dans [Guide pratique pour appeler l’API Analyse de texte](text-analytics-how-to-call-api.md). Les points suivants sont réaffirmés pour des raisons pratiques :

+ Créez une demande **POST**. Passez en revue la documentation de l’API pour cette requête : [API d’expressions clés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)

+ Définissez le point de terminaison HTTP pour l’extraction de phrases clés à l’aide d’une ressource Analyse de texte sur Azure ou d’un [conteneur Analyse de texte](text-analytics-how-to-install-containers.md) instancié. Il doit inclure la ressource `/keyPhrases` : `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`

+ Définissez un en-tête de demande pour inclure la clé d’accès pour les opérations d’Analyse de texte. Pour plus d’informations, consultez [Guide pratique pour rechercher des points de terminaison et des clés d’accès](text-analytics-how-to-access-key.md).

+ Dans le corps de la demande, fournissez la collection de documents JSON que vous avez préparée pour cette analyse.

> [!Tip]
> Utilisez [Postman](text-analytics-how-to-call-api.md) ou ouvrez la **console de test d’API** dans la [documentation](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) pour structurer une demande et la publier dans le service.

## <a name="step-2-post-the-request"></a>Étape 2 : Publier la requête

L’analyse est effectuée à la réception de la demande. Le service accepte jusqu'à 100 demandes par minute. Chaque demande peut être au maximum de 1 Mo.

Rappelez-vous que le service est sans état. Aucune donnée n’est stockée dans votre compte. Les résultats sont retournés immédiatement dans la réponse.

## <a name="step-3-view-results"></a>Étape 3 : Afficher les résultats

Toutes les demandes POST retournent une réponse au format JSON avec les ID et les propriétés détectées.

La sortie est retournée immédiatement. Vous pouvez diffuser en continu les résultats dans une application qui accepte le code JSON ou enregistrer la sortie dans un fichier sur le système local, puis l’importer dans une application qui vous permet de trier, rechercher et manipuler les données.

Voici un exemple de sortie pour l’extraction de phrases clés :

```json
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
```

Comme indiqué, l’analyseur recherche et ignore les mots non essentiels, et conserve les termes ou expressions uniques qui semblent constituer le sujet ou l’objet d’une phrase. 

## <a name="summary"></a>Résumé

Dans cet article, vous avez vu les concepts et le flux de travail d’extraction d’expressions clés à l’aide de l’API Analyse de texte dans Cognitive Services. En résumé :

+ L’[API Extraction de phrases clés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) est disponible pour les langues sélectionnées.
+ Les documents JSON figurant dans le corps de la demande incluent un id, un texte et un code de langue.
+ La demande POST s’effectue sur un point de terminaison `/keyphrases`, à l’aide [d’une clé d’accès et d’un point de terminaison](text-analytics-how-to-access-key.md) personnalisés valides pour votre abonnement.
+ La sortie de réponse, qui se compose de mots et d’expressions clés pour chaque ID de document, peut être diffusée vers n’importe quelle application qui accepte JSON, y compris Excel et Power BI, pour n’en citer que quelques-unes.

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Page produit d’Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API Analyse de texte](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
