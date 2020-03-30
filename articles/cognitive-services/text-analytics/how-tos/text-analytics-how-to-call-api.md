---
title: Appeler l’API Text Analytics
titleSuffix: Azure Cognitive Services
description: Cet article explique comment appeler l’API REST Analyse de texte Azure Cognitive Services et Postman.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: afb576c265ccdd4a014ed678331f030a0442a197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218551"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Comment appeler l’API REST Analyse de texte

Les appels de l’**API Analyse de texte** sont des appels HTTP POST/GET, que vous pouvez formuler dans n’importe quel langage. Dans cet article, nous utilisons REST et [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) pour présenter des concepts clés.

Chaque demande doit inclure votre clé d’accès et un point de terminaison HTTP. Le point de terminaison spécifie la région que vous avez choisie lors de l’inscription, l’URL du service et une ressource utilisée lors de la demande : `sentiment`, `keyphrases`, `languages` et `entities`. 

Rappelez-vous qu’Analyse de texte étant sans état, il y a pas de ressources de données à gérer. Votre texte est chargé et analysé dès réception, puis les résultats sont retournés immédiatement à l’application appelante.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Définition de schéma JSON

L’entrée doit être un JSON en texte brut non structuré. XML n’est pas pris en charge. Le schéma, simple, se compose des éléments décrits dans la liste suivante. 

Vous pouvez actuellement envoyer les mêmes documents pour toutes les opérations d’Analyse de texte : sentiments, phrases clés, détection de la langue et identification d’entité (il se peut que le schéma varie pour chaque analyse à l’avenir).

| Élément | Valeurs valides | Requis ? | Usage |
|---------|--------------|-----------|-------|
|`id` |Les données dont de type chaîne mais, dans la pratique, les ID de document tendent à être des entiers. | Obligatoire | Le système utilise les ID que vous fournissez pour structurer la sortie. Des codes langue, phrases clés et scores de sentiment sont générés pour chaque ID dans la demande.|
|`text` | Texte brut non structuré, jusqu’à 5 120 caractères. | Obligatoire | Pour la détection de la langue, le texte peut être exprimé dans toute langue. Pour l’analyse des sentiments, l’extraction de phrases clés et l’identification d’entité, le texte doit être dans une [langue prise en charge](../text-analytics-supported-languages.md). |
|`language` | Code [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de 2 caractères d’une [langue prise en charge](../text-analytics-supported-languages.md) | Variable | Obligatoire pour l’analyse des sentiments, l’extraction de phrases clés et la liaison d’entité. Facultatif pour la détection de la langue. Son omission ne génère aucune erreur, mais affaiblit l’analyse. Le code de langue doit correspondre au `text` que vous fournissez. |

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
