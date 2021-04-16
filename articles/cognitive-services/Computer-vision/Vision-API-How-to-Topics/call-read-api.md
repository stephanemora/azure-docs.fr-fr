---
title: Comment appeler l’API Read
titleSuffix: Azure Cognitive Services
description: Découvrez comment appeler l’API Read et configurer son comportement en détail.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: 54ec669984488d5fe429050faefa0ff2d2e8eb9b
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287483"
---
# <a name="call-the-read-api"></a>Appeler l’API Lire

Dans ce guide, vous allez apprendre à appeler l’API Read pour extraire du texte à partir d’images. Vous découvrirez les différentes façons dont vous pouvez configurer le comportement de cette API pour répondre à vos besoins.

## <a name="submit-data-to-the-service"></a>Envoyer des données au service

L’[appel de lecture](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) de l’API de lecture prend en entrée une image ou un document PDF et extrait du texte de façon asynchrone.

`https://{endpoint}/vision/v3.2-preview.3/read/analyze[?language][&pages][&readingOrder]`

L’appel retourne avec un champ d’en-tête de réponse appelé `Operation-Location`. La valeur `Operation-Location` est une URL qui contient l’ID d’opération à utiliser à l’étape suivante.

|En-tête de réponse| Valeur d'exemple |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> La page de [tarification de Vision par ordinateur](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) comprend le niveau tarifaire pour Lire. Chaque image ou page analysée est une transaction. Si vous appelez l’opération avec un document PDF ou TIFF contenant 100 pages, l’opération Lire compte cela comme 100 transactions et vous serez facturé pour 100 transactions. Si vous avez effectué 50 appels à l’opération et que chaque appel a envoyé un document avec 100 pages, vous êtes facturé pour 50 X 100 = 5000 transactions.

## <a name="determine-how-to-process-the-data"></a>Déterminer le mode de traitement des données

### <a name="language-specification"></a>Spécification du langage

L’[appel de lecture](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) a un paramètre de requête facultatif pour la langue. L’opération de lecture prend en charge l’identification automatique des langues et les documents multilingues. Par conséquent, fournissez uniquement un code de langue si vous souhaitez forcer le traitement du document dans la langue en question.

### <a name="natural-reading-order-output-latin-languages-only"></a>Sortie de l’ordre de lecture naturel (langues latines uniquement)
À l’aide de l’[API Read 3.2 preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), vous pouves spécifier l’ordre dans lequel les lignes de texte sont générées avec le paramètre de requête `readingOrder`. Utilisez `natural` pour une sortie d’ordre de lecture plus conviviale, comme illustré dans l’exemple suivant. Cette fonctionnalité est prise en charge uniquement pour les langues latines.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="Exemple d’ordre de lecture OCR":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Sélectionner des pages ou des plages de pages pour l’extraction de texte
Avec l’[API Read 3.2 preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), pour les grands documents multipages, utilisez le paramètre de requête `pages` pour spécifier des numéros de pages ou des plages de pages spécifiques pour extraire le texte de ces pages uniquement. L’exemple suivant montre un document de 10 pages, avec le texte extrait pour les deux cas : toutes les pages (1 à 10) et certaines pages (3 à 6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Sortie des pages sélectionnées":::

## <a name="get-results-from-the-service"></a>Obtenir les résultats du service

La deuxième étape consiste à appeler l’opération [Obtenir les résultats de lecture](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750). Cette opération prend en entrée l’ID d’opération créé par l’opération de lecture. 

`https://{endpoint}/vision/v3.2-preview.3/read/analyzeResults/{operationId}`

Elle retourne une réponse JSON qui contient un champ **État** avec les possibles valeurs suivantes. 

|Valeur | Signification |
|:-----|:----|
| `notStarted`| L’opération n’a pas commencé. |
| `running`| L’opération est en cours. |
| `failed`| L’opération a échoué. |
| `succeeded`| L'opération a réussi. |

Vous appelez cette opération de façon itérative jusqu’à ce qu’elle retourne avec la valeur **succeeded**. Utilisez un intervalle de 1 à 2 secondes pour éviter de dépasser le taux de requêtes par seconde (RPS).

> [!NOTE]
> Le niveau gratuit limite le taux de requêtes à 20 appels par minute. Le niveau payant autorise un taux de 10 requêtes par seconde (RPS) qui peut être augmenté sur demande. Utilisez le canal de support Azure ou votre équipe de compte pour demander un taux de requêtes par seconde (RPS) supérieur.

Lorsque le champ **État** a la valeur `succeeded`, la réponse JSON contient le contenu du texte extrait de votre image ou document. La réponse JSON conserve les regroupements de lignes d’origine des mots reconnus. Elle comprend les lignes de texte extraites et les coordonnées de leur cadre englobant. Chaque ligne de texte inclut tous les mots extraits avec leurs coordonnées et des scores de confiance.

> [!NOTE]
> Les données soumises à l’opération `Read` sont temporairement chiffrées et stockées au repos pendant une brève durée, puis supprimées. Cela permet à vos applications de récupérer le texte extrait dans le cadre de la réponse du service.

### <a name="sample-json-output"></a>Exemple de sortir JSON

Voici un exemple de réponse JSON correcte :

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Classification manuscrite pour les lignes de texte (langues latines uniquement)
La réponse de l’[API Read 3.2 preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) inclut le classement de chaque ligne de texte selon qu’elle est de style manuscrit ou non, avec un score de confiance. Cette fonctionnalité est prise en charge uniquement pour les langues latines. L’exemple suivant illustre la classification manuscrite pour le texte de l’image.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Exemple de classification d’écriture manuscrite OCR":::

## <a name="next-steps"></a>Étapes suivantes

Pour utiliser l’API REST, consultez la [Référence de l’API Read](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005).