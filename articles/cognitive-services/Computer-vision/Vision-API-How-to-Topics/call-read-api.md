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
ms.openlocfilehash: 8e0ef789653181d744100ef6e179bcf328f6d704
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308623"
---
# <a name="call-the-read-api"></a>Appeler l’API Lire

Dans ce guide, vous allez apprendre à appeler l’API Read pour extraire du texte à partir d’images. Vous découvrirez les différentes façons dont vous pouvez configurer le comportement de cette API pour répondre à vos besoins.

Ce guide part du principe que vous avez déjà <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créé une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur</a>, et obtenu une clé d'abonnement et une URL de point de terminaison. Si vous ne l'avez pas encore fait, suivez un guide de [démarrage rapide](../quickstarts-sdk/client-library.md) pour commencer.

## <a name="submit-data-to-the-service"></a>Envoyer des données au service

L’[appel de lecture](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) de l’API de lecture prend en entrée une image ou un document PDF et extrait du texte de façon asynchrone.

`https://{endpoint}/vision/v3.2/read/analyze[?language][&pages][&readingOrder]`

L’appel retourne avec un champ d’en-tête de réponse appelé `Operation-Location`. La valeur `Operation-Location` est une URL qui contient l’ID d’opération à utiliser à l’étape suivante.

|En-tête de réponse| Valeur d'exemple |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> La page de [tarification de Vision par ordinateur](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) comprend le niveau tarifaire pour Lire. Chaque image ou page analysée est une transaction. Si vous appelez l’opération avec un document PDF ou TIFF contenant 100 pages, l’opération Lire compte cela comme 100 transactions et vous serez facturé pour 100 transactions. Si vous avez effectué 50 appels à l’opération et que chaque appel a envoyé un document avec 100 pages, vous êtes facturé pour 50 X 100 = 5000 transactions.

## <a name="determine-how-to-process-the-data"></a>Déterminer le mode de traitement des données

### <a name="language-specification"></a>Spécification du langage

L’[appel de lecture](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) a un paramètre de requête facultatif pour la langue. L’opération de lecture prend en charge l’identification automatique des langues et les documents multilingues. Par conséquent, fournissez uniquement un code de langue si vous souhaitez forcer le traitement du document dans la langue en question.

### <a name="natural-reading-order-output-latin-languages-only"></a>Sortie de l’ordre de lecture naturel (langues latines uniquement)
Spécifiez l'ordre dans lequel les lignes de texte sont générées avec le paramètre de requête `readingOrder`. Utilisez `natural` pour une sortie d’ordre de lecture plus conviviale, comme illustré dans l’exemple suivant. Cette fonctionnalité est prise en charge uniquement pour les langues latines.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="Exemple d’ordre de lecture OCR":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Sélectionner des pages ou des plages de pages pour l’extraction de texte
Pour les documents multipages volumineux, utilisez le paramètre de requête `pages` pour spécifier des numéros de page ou des plages de pages afin d'extraire le texte de ces pages uniquement. L’exemple suivant montre un document de 10 pages, avec le texte extrait pour les deux cas : toutes les pages (1 à 10) et certaines pages (3 à 6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Sortie des pages sélectionnées":::

## <a name="get-results-from-the-service"></a>Obtenir les résultats du service

La deuxième étape consiste à appeler l’opération [Obtenir les résultats de lecture](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d9869604be85dee480c8750). Cette opération prend en entrée l’ID d’opération créé par l’opération de lecture. 

`https://{endpoint}/vision/v3.2/read/analyzeResults/{operationId}`

Elle retourne une réponse JSON qui contient un champ **État** avec les possibles valeurs suivantes. 

|Valeur | Signification |
|:-----|:----|
| `notStarted`| L’opération n’a pas commencé. |
| `running`| L’opération est en cours. |
| `failed`| L’opération a échoué. |
| `succeeded`| L'opération a réussi. |

Vous appelez cette opération de façon itérative jusqu’à ce qu’elle retourne avec la valeur **succeeded**. Utilisez un intervalle de 1 à 2 secondes pour éviter de dépasser le taux de requêtes par seconde (RPS).

> [!NOTE]
> Le niveau gratuit limite le taux de requêtes à 20 appels par minute. Le niveau payant autorise un taux de 10 requêtes par seconde (RPS) qui peut être augmenté sur demande. Notez votre identificateur de ressource Azure et votre région, puis ouvrez un ticket de support Azure ou contactez l'équipe en charge de vos comptes pour solliciter une augmentation du taux de requêtes par seconde (RPS).

Lorsque le champ **État** a la valeur `succeeded`, la réponse JSON contient le contenu du texte extrait de votre image ou document. La réponse JSON conserve les regroupements de lignes d’origine des mots reconnus. Elle comprend les lignes de texte extraites et les coordonnées de leur cadre englobant. Chaque ligne de texte inclut tous les mots extraits avec leurs coordonnées et des scores de confiance.

> [!NOTE]
> Les données soumises à l’opération `Read` sont temporairement chiffrées et stockées au repos pendant une brève durée, puis supprimées. Cela permet à vos applications de récupérer le texte extrait dans le cadre de la réponse du service.

### <a name="sample-json-output"></a>Exemple de sortir JSON

Voici un exemple de réponse JSON correcte :

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
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
La réponse inclut le classement de chaque ligne de texte selon qu’elle est de style manuscrit ou non, avec un score de confiance. Cette fonctionnalité est prise en charge uniquement pour les langues latines. L’exemple suivant illustre la classification manuscrite pour le texte de l’image.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Exemple de classification d’écriture manuscrite OCR":::

## <a name="next-steps"></a>Étapes suivantes

Pour essayer l'API REST, consultez [Informations de référence sur l'API Read](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
