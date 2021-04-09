---
title: Reconnaissance optique de caractères (OCR) – Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la reconnaissance optique de caractères (OCR) d’images et de documents comportant du texte imprimé et manuscrit à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: e0247560afa8229f4fa5c25ec7dfbbca4f7defb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102486096"
---
# <a name="optical-character-recognition-ocr"></a>Reconnaissance optique des caractères (OCR)

L’API Vision par ordinateur d’Azure comprend des fonctionnalités de reconnaissance optique de caractères (OCR) qui extraient du texte imprimé ou manuscrit d’images. Vous pouvez extraire du texte d’images, comme des photos de contenants ou de conteneurs indiquant des numéros de série, ainsi que de documents (factures, rapports financiers, articles, etc.).

## <a name="read-api"></a>API Lire 

[L’API Lire](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) Vision par ordinateur, toute dernière technologie OCR d’Azure ([découvrez les nouveautés](./whats-new.md)), extrait du texte imprimé (dans plusieurs langues), du texte manuscrit (en anglais uniquement), des chiffres et des symboles monétaires à partir d’images et de documents PDF multipages. Elle est optimisée pour extraire le texte d’images à forte composante textuelle et de documents PDF multipages en langue mixte. Elle prend en charge la détection de texte imprimé et manuscrit dans la même image ou le même document.

![Comment la reconnaissance optique de caractères convertit les images et les documents en une sortie structurée avec du texte extrait](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Critères des entrées
L’appel **Lire** utilise des images et des documents comme entrée. Les conditions requises sont les suivantes :

* Formats de fichiers pris en charge : JPEG, PNG, BMP, PDF et TIFF.
* Pour les fichiers PDF et TIFF, jusqu’à 2000 pages (seules les deux premières pages pour le niveau gratuit) sont traitées.
* La taille de fichier doit être inférieure à 50 Mo (4 Mo pour le niveau gratuit), et les dimensions comprises entre 50 × 50 pixels et 10000 × 10000 pixels. 
* Les dimensions des PDF ne doivent pas dépasser 17 × 17 pouces, ce qui correspond aux formats de papier Legal ou A3 (maximum).

> [!NOTE]
> **Entrée de langue** 
>
> L’[appel de lecture](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) a un paramètre de requête facultatif pour la langue. L’opération de lecture prend en charge l’identification automatique des langues et les documents multilingues. Par conséquent, fournissez uniquement un code de langue si vous souhaitez forcer le traitement du document dans la langue en question.

## <a name="ocr-demo-examples"></a>Version de démonstration OCR (exemples)

![Versions de démonstration OCR](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>Étape 1 : L’opération de lecture

L’[appel de lecture](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) de l’API de lecture prend en entrée une image ou un document PDF et extrait du texte de façon asynchrone. L’appel retourne avec un champ d’en-tête de réponse appelé `Operation-Location`. La valeur `Operation-Location` est une URL qui contient l’ID d’opération à utiliser à l’étape suivante.

|En-tête de réponse| URL de résultat |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> La page de [tarification de Vision par ordinateur](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) comprend le niveau tarifaire pour Lire. Chaque image ou page analysée est une transaction. Si vous appelez l’opération avec un document PDF ou TIFF contenant 100 pages, l’opération Lire compte cela comme 100 transactions et vous serez facturé pour 100 transactions. Si vous avez effectué 50 appels à l’opération et que chaque appel a envoyé un document avec 100 pages, vous êtes facturé pour 50 X 100 = 5000 transactions.

## <a name="step-2-the-get-read-results-operation"></a>Étape 2 : L’opération Obtenir les résultats de lecture

La deuxième étape consiste à appeler l’opération [Obtenir les résultats de lecture](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750). Cette opération prend en entrée l’ID d’opération créé par l’opération de lecture. Elle retourne une réponse JSON qui contient un champ **État** avec les possibles valeurs suivantes. Vous appelez cette opération de façon itérative jusqu’à ce qu’elle retourne avec la valeur **succeeded**. Utilisez un intervalle de 1 à 2 secondes pour éviter de dépasser le taux de requêtes par seconde (RPS).

|Champ| Type | Valeurs possibles |
|:-----|:----:|:----|
|status | string | notStarted : L’opération n’a pas commencé. |
| |  | running : L’opération est en cours. |
| |  | failed : L’opération a échoué. |
| |  | succeeded : L'opération a réussi. |

> [!NOTE]
> Le niveau gratuit limite le taux de requêtes à 20 appels par minute. Le niveau payant autorise un taux de 10 requêtes par seconde (RPS) qui peut être augmenté sur demande. Utilisez le canal de support Azure ou votre équipe de compte pour demander un taux de requêtes par seconde (RPS) supérieur.

Lorsque le champ **État** a la valeur **succeeded**, la réponse JSON contient le contenu du texte extrait de votre image ou document. La réponse JSON conserve les regroupements de lignes d’origine des mots reconnus. Elle comprend les lignes de texte extraites et les coordonnées de leur cadre englobant. Chaque ligne de texte inclut tous les mots extraits avec leurs coordonnées et des scores de confiance.

> [!NOTE]
> Les données soumises à l’opération `Read` sont temporairement chiffrées et stockées au repos pendant une brève durée, puis supprimées. Cela permet à vos applications de récupérer le texte extrait dans le cadre de la réponse du service.

## <a name="sample-json-output"></a>Exemple de sortir JSON

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

## <a name="natural-reading-order-output-latin-only"></a>Sortie de l’ordre de lecture naturel (langues latines uniquement)
À l’aide de l’[API Read 3.2 preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), spécifiez l’ordre dans lequel les lignes de texte sont générées avec le paramètre de requête `readingOrder`. Utilisez `natural` pour une sortie d’ordre de lecture plus conviviale, comme illustré dans l’exemple suivant. Cette fonctionnalité est prise en charge uniquement pour les langues latines.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="Exemple d’ordre de lecture OCR":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>Classification manuscrite pour les lignes de texte (Latin uniquement)
La réponse de l’[API Read 3.2 preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) inclut le classement de chaque ligne de texte selon qu’elle est de style manuscrit ou non, avec un score de confiance. Cette fonctionnalité est prise en charge uniquement pour les langues latines. L’exemple suivant illustre la classification manuscrite pour le texte de l’image.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="Exemple de classification d’écriture manuscrite OCR":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>Sélectionner des pages ou des plages de pages pour l’extraction de texte
Avec l’[API Read 3.2 preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), pour les grands documents multipages, utilisez le paramètre de requête `pages` pour spécifier des numéros de pages ou des plages de pages spécifiques pour extraire le texte de ces pages uniquement. L’exemple suivant montre un document de 10 pages, avec le texte extrait pour les deux cas : toutes les pages (1 à 10) et certaines pages (3 à 6).

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="Sortie des pages sélectionnées":::

## <a name="supported-languages"></a>Langues prises en charge
Les API Read prennent en charge un total de 73 langues pour le texte de style d’impression. Consultez la liste complète des [langues prises en charge par OCR](./language-support.md#optical-character-recognition-ocr). L’OCR de style manuscrit est pris en charge exclusivement pour l’anglais.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Utiliser l’API cloud ou déployer localement
Les API cloud Read 3.x sont l’option préférée pour la plupart des clients en raison de la facilité d’intégration et de la productivité rapide prête à l’emploi. Azure et le service de Vision par ordinateur gèrent l’évolutivité, les performances, la sécurité des données et les besoins en matière de conformité tout en répondant aux besoins de vos clients.

Pour un déploiement local, le [conteneur Docker Read (préversion)](./computer-vision-how-to-install-containers.md) vous permet de déployer les nouvelles capacités OCR dans votre propre environnement local. Les conteneurs conviennent particulièrement bien à certaines exigences de sécurité et de gouvernance des données.

## <a name="ocr-api"></a>API OCR

[L’API OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) applique un modèle de reconnaissance plus ancien, ne prend en charge que les images et s’exécute de façon synchrone, retournant immédiatement le texte détecté. Consultez les [langues prises en charge par OCR](./language-support.md#optical-character-recognition-ocr) puis l’API Lire.

> [!NOTE]
> Les opérations RecognizeText de Computer Vison 2.0 seront prochainement remplacées par la nouvelle API Lire abordée dans cet article. Les clients existants devraient [effectuer la transition vers les opérations de lecture](upgrade-api-versions.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment bien démarrer avec les [démarrages rapides sur la bibliothèque de client ou l’API REST Vision par ordinateur](./quickstarts-sdk/client-library.md).
- Découvrez l’[API REST Lire 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Découvrez [l’API REST Read 3.2 en préversion publique](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) avec prise en charge d’un total de 73 langues.
