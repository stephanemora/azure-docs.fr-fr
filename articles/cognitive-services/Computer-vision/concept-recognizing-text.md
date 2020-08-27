---
title: Reconnaissance optique de caractères (OCR) – Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la reconnaissance optique de caractères (OCR) d’images et de documents comportant du texte imprimé et manuscrit à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cb931d0b9c3dd4d3fa0fa69f69f5f90fc37ea8f6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929191"
---
# <a name="optical-character-recognition-ocr"></a>Reconnaissance optique des caractères (OCR)

L’API Vision par ordinateur d’Azure comprend des fonctionnalités de reconnaissance optique de caractères (OCR) qui extraient du texte imprimé ou manuscrit d’images. Vous pouvez extraire du texte d’images, comme des photos de contenants ou de conteneurs indiquant des numéros de série, ainsi que de documents (factures, rapports financiers, articles, etc.). 

## <a name="read-api"></a>API Lire 

[L’API Lire](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) Vision par ordinateur, toute dernière technologie OCR d’Azure, extrait du texte imprimé (dans plusieurs langues), du texte manuscrit (en anglais uniquement), des chiffres et des symboles monétaires à partir d’images et de documents PDF multipages. Elle est optimisée pour extraire le texte d’images à forte composante textuelle et de documents PDF multipages en langue mixte. Elle prend en charge la détection de texte imprimé et manuscrit dans la même image ou le même document.

![Comment la reconnaissance optique de caractères convertit les images et les documents en une sortie structurée avec du texte extrait](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Critères des entrées
L’opération de **lecture** de l’API Lire utilise des images et des documents comme entrée. Les conditions requises sont les suivantes :

* Formats de fichiers pris en charge : JPEG, PNG, BMP, PDF et TIFF.
* PDF et TIFF : jusqu’à 2 000 pages sont traitées. Abonnés du niveau Gratuit : seules les deux premières pages sont traitées.
* La taille de fichier doit être inférieure à 50 Mo, et les dimensions comprises entre 50 × 50 pixels et 10 000 × 10 000 pixels.
* Les dimensions des PDF ne doivent pas dépasser 17 × 17 pouces, ce qui correspond aux formats de papier Legal ou A3 (maximum).

> [!NOTE]
> **Entrée de langue** 
>
> L’[opération de lecture](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) a un paramètre de requête facultatif pour la langue. Il s’agit du code de langue BCP-47 du texte dans le document. L’opération de lecture prend en charge l’identification automatique des langues et les documents multilingues. Par conséquent, fournissez uniquement un code de langue si vous souhaitez forcer le traitement du document dans la langue en question.

## <a name="the-read-operation"></a>L’opération de lecture

L’[Opération de lecture](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) prend en entrée une image ou un document PDF et extrait du texte de façon asynchrone. L’appel retourne avec un champ d’en-tête de réponse appelé `Operation-Location`. La valeur `Operation-Location` est une URL qui contient l’ID d’opération à utiliser à l’étape suivante.

|En-tête de réponse| URL de résultat |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.0/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-read-results-operation"></a>L’opération Obtenir les résultats de lecture

La deuxième étape consiste à appeler l’opération [Obtenir les résultats de lecture](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750). Cette opération prend en entrée l’ID d’opération créé par l’opération de lecture. Elle retourne une réponse JSON qui contient un champ **État** avec les possibles valeurs suivantes. Vous appelez cette opération de façon itérative jusqu’à ce qu’elle retourne avec la valeur **succeeded**. Utilisez un intervalle de 1 à 2 secondes pour éviter de dépasser le taux de requêtes par seconde (RPS).

|Champ| Type | Valeurs possibles |
|:-----|:----:|:----|
|status | string | notStarted : L’opération n’a pas commencé. |
| |  | running : L’opération est en cours. |
| |  | failed : L’opération a échoué. |
| |  | succeeded : L'opération a réussi. |

> [!NOTE]
> Le niveau gratuit limite le taux de requêtes à 20 appels par minute. Le niveau payant autorise un taux de 10 requêtes par seconde (RPS) qui peut être augmenté sur demande. Utilisez le canal de support Azure ou votre équipe de compte pour demander un taux de requêtes par seconde (RPS) supérieur.

Lorsque le champ **État** a la valeur **succeeded**, la réponse JSON contient le contenu du texte extrait de votre image ou document. La réponse JSON conserve les regroupements de lignes d’origine des mots reconnus. Elle comprend les lignes de texte extraites et les coordonnées de leur cadre englobant. Chaque ligne de texte inclut tous les mots extraits avec leurs coordonnées et des scores de confiance.

### <a name="sample-json-output"></a>Exemple de sortir JSON

Voici un exemple de réponse JSON correcte :

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
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

Suivez le guide de démarrage rapide [Extraire du texte imprimé et manuscrit](./QuickStarts/CSharp-hand-text.md) pour mettre en œuvre la reconnaissance optique de caractères à l’aide de C# et de l’API REST.

## <a name="language-support"></a>Support multilingue

### <a name="printed-text"></a>Texte imprimé
L’[API Lire 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) prend en charge l’extraction de texte imprimé en anglais, en espagnol, en allemand, en français, en italien, en portugais et en néerlandais. 

La [préversion publique de l’API Lire 3.1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) ajoute la prise en charge du chinois simplifié. Si votre scénario nécessite la prise en charge d’autres langues, référez-vous à la rubrique de l’[API OCR](#ocr-api). 

Pour obtenir la liste complète des langues prises en charge par OCR, consultez l’article [Langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr).

### <a name="handwritten-text"></a>Texte manuscrit
L’opération de lecture prend actuellement en charge l’extraction de texte manuscrit en anglais exclusivement.

## <a name="integration-options"></a>Options d’intégration

### <a name="use-the-rest-api-or-client-sdk"></a>Utiliser l’API REST ou le kit de développement logiciel (SDK) client
La [l’API REST Lire 3.x](./QuickStarts/CSharp-hand-text.md) est l’option préférée pour la plupart des clients en raison de la facilité d’intégration et de la productivité rapide prête à l’emploi. Azure et le service de Vision par ordinateur gèrent l’évolutivité, les performances, la sécurité des données et les besoins en matière de conformité tout en répondant aux besoins de vos clients.

### <a name="use-containers-for-on-premise-deployment"></a>Utiliser des conteneurs destinés au déploiement local
Le [conteneur Docker Lire 2.0 (version préliminaire)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) vous permet de déployer les nouvelles fonctionnalités OCR dans votre propre environnement local. Les conteneurs conviennent particulièrement bien à certaines exigences de sécurité et de gouvernance des données.

## <a name="read-ocr-examples"></a>Exemples d’OCR Lire

### <a name="text-from-images"></a>Texte issu d’images

La sortie suivante de l’API Lire présente le texte extrait d’une image comportant du texte écrit à différents angles et dans différentes couleurs et polices.

![Image de plusieurs mots dans couleurs et des angles différents, avec le texte extrait listé](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Texte issu de documents

L’API Read peut également prendre des documents PDF comme entrée.

![Document de facture, avec le texte extrait listé](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Texte manuscrit

L’opération de lecture extrait le texte manuscrit des images (actuellement en anglais uniquement).

![Image d’une note manuscrite, avec le texte extrait listé](./Images/handwritten-example.png)

### <a name="printed-text"></a>Texte imprimé

L’opération de lecture peut extraire du texte imprimé dans différentes langues.

![Image d’un manuel en espagnol, avec le texte extrait listé](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Documents en langue mixte

L’API Lire prend en charge des images et des documents contenant plusieurs langues, communément appelés documents en langue mixte. Elle fonctionne en classant chaque ligne de texte du document dans la langue détectée avant d’extraire le contenu textuel.

![Image d’expressions dans plusieurs langues, avec le texte extrait listé](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>API OCR

[L’API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) applique un modèle de reconnaissance plus ancien, ne prend en charge que les images et s’exécute de façon synchrone, retournant immédiatement le texte détecté. Consultez les [langues prises en charge par OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) puis l’API Lire.

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme pour tous les services Cognitive Services, les développeurs qui utilisent les services Lire/OCR doivent connaître les stratégies de Microsoft concernant les données client. Pour plus d’informations, consultez la page Cognitive Services dans le [Centre de gestion de la confidentialité Microsoft](https://www.microsoft.com/trust-center/product-overview).

> [!NOTE]
> Les opérations RecognizeText de Computer Vison 2.0 seront prochainement remplacées par la nouvelle API Lire abordée dans cet article. Les clients existants devraient [effectuer la transition vers les opérations de lecture](upgrade-api-versions.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [l’API REST Lire 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Découvrez [l’API REST Lire 3.1-en version préliminaire publique](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) avec ajout de la prise en charge du chinois simplifié.
- Suivez le guide de démarrage rapide [Extraction de texte](./QuickStarts/CSharp-hand-text.md) pour mettre en œuvre la reconnaissance optique de caractères en C# , en Java, en JavaScript ou en Python avec l’API REST.
