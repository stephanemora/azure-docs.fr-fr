---
title: Appeler l’API Text Analytics
titlesuffix: Azure Cognitive Services
description: Découvrez comment appeler l’API REST Analyse de texte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e98979ac43945ebc9af82d5f89db01855429ca70
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304202"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Comment appeler l’API REST Analyse de texte

Les appels de l’**API Analyse de texte** sont des appels HTTP POST/GET, que vous pouvez formuler dans n’importe quel langage. Dans cet article, nous utilisons REST et [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) pour présenter des concepts clés.

Chaque demande doit inclure votre clé d’accès et un point de terminaison HTTP. Le point de terminaison spécifie la région que vous avez choisie lors de l’inscription, l’URL du service et une ressource utilisée lors de la demande : `sentiment`, `keyphrases`, `languages` et `entities`. 

Rappelez-vous qu’Analyse de texte étant sans état, il y a pas de ressources de données à gérer. Votre texte est chargé et analysé dès réception, puis les résultats sont retournés immédiatement à l’application appelante.

> [!Tip]
> Pour effectuer des appels ponctuels afin d’observer le fonctionnement de l’API, vous pouvez envoyer des demandes POST à partir de la **console de test d’API**, accessible via la [page de documentation de toute API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). Aucune installation n’est nécessaire, et les seules exigences sont de coller une clé d’accès et les documents JSON dans la demande. 

## <a name="prerequisites"></a>Prérequis

Vous devez avoir un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec l’API Analyse de texte, ainsi que la [clé du point de terminaison et la clé d’accès](text-analytics-how-to-access-key.md) générées lorsque vous vous inscrivez à Cognitive Services. 

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Définition de schéma JSON

L’entrée doit être un JSON en texte brut non structuré. XML n’est pas pris en charge. Le schéma, simple, se compose des éléments décrits dans la liste suivante. 

Vous pouvez actuellement envoyer les mêmes documents pour toutes les opérations d’Analyse de texte : sentiments, phrases clés, détection de la langue et identification d’entité (il se peut que le schéma varie pour chaque analyse à l’avenir).

| Élément | Valeurs valides | Requis ? | Usage |
|---------|--------------|-----------|-------|
|`id` |Les données dont de type chaîne mais, dans la pratique, les ID de document tendent à être des entiers. | Obligatoire | Le système utilise les ID que vous fournissez pour structurer la sortie. Des codes langue, phrases clés et scores de sentiment sont générés pour chaque ID dans la demande.|
|`text` | Texte brut non structuré, jusqu’à 5 120 caractères. | Obligatoire | Pour la détection de la langue, le texte peut être exprimé dans toute langue. Pour l’analyse des sentiments, l’extraction de phrases clés et l’identification d’entité, le texte doit être dans une [langue prise en charge](../text-analytics-supported-languages.md). |
|`language` | Code [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de 2 caractères d’une [langue prise en charge](../text-analytics-supported-languages.md) | Varie | Obligatoire pour l’analyse des sentiments, l’extraction de phrases clés et la liaison d’entité. Facultatif pour la détection de la langue. Son omission ne génère aucune erreur, mais affaiblit l’analyse. Le code de langue doit correspondre au `text` que vous fournissez. |

Pour plus d’informations sur les limites, voir [Vue d’ensemble de l’Analyse de texte > Limites des données](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Configurer une demande dans Postman

Le service accepte une taille de demande jusqu’à 1 Mo. Si vous utilisez Postman (ou un autre outil de test d’API web), configurez le point de terminaison pour inclure la ressource que vous voulez utiliser, et fournissez la clé d’accès dans un en-tête de demande. Chaque opération requiert que vous ajoutiez la ressource appropriée au point de terminaison. 

1. Dans Postman :

   + Choisissez **Post** comme type de demande.
   + Collez le point de terminaison que vous avez copié à partir de la page du portail.
   + Ajoutez une ressource.

   Les points de terminaison de ressource sont les suivants (votre région peut varier) :

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Définissez les trois en-têtes de demande :

   + `Ocp-Apim-Subscription-Key`: votre clé d’accès, obtenue du portail Azure.
   + `Content-Type` : application/json.
   + `Accept` : application/json.

   Votre demande doit ressembler à la capture d’écran suivante, en supposant que la ressource est **/keyPhrases**.

   ![Capture d’écran de demande avec point de terminaison et en-têtes](../media/postman-request-keyphrase-1.png)

4. Cliquez sur **Body** (Corps) et choisissez le format **raw** (brut).

   ![Capture d’écran de demande avec paramètres du corps](../media/postman-request-body-raw.png)

5. Collez des documents JSON d’un format valide pour l’analyse prévue. Pour plus d’informations sur une analyse particulière, voir les rubriques ci-dessous :

  + [Détection de la langue](text-analytics-how-to-language-detection.md)  
  + [Extraction de phrases clés](text-analytics-how-to-keyword-extraction.md)  
  + [Analyse des sentiments](text-analytics-how-to-sentiment-analysis.md)  
  + [Reconnaissance d’entités](text-analytics-how-to-entity-linking.md)  


6. Cliquez sur **Envoyer** pour soumettre la demande. Consultez la section [Limites de données](../overview.md#data-limits) dans la vue d’ensemble pour connaître le nombre de demandes que vous pouvez envoyer par minute et seconde.

   Dans Postman, la réponse s’affiche dans la fenêtre suivante en-dessous, en tant que document JSON, avec un élément pour chaque ID de document fourni dans la demande.

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Détecter la langue](text-analytics-how-to-language-detection.md)
