---
title: Dispositions - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Découvrez les concepts liés à l’analyse des dispositions avec l’API Form Recognizer - utilisation et limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 73bef21f430bde1c6c2c95d7c3f685cccbbd9179
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467015"
---
# <a name="form-recognizer-layout-service"></a>Service Layout de Form Recognizer

Azure Form Recognizer peut extraire du texte, des tables, des marques de sélection et des informations de structure dans des documents à l’aide de son service Layout. L’API de disposition permet aux clients de prendre des documents dans une multitude de formats et de renvoyer des représentations de données structurées des documents. Elle combine nos puissantes capacités de [reconnaissance optique de caractères (OCR)](../computer-vision/concept-recognizing-text.md) avec des modèles Deep Learning pour extraire le texte, les tableaux, les marques de sélection et la structure du document. 

## <a name="what-does-the-layout-service-do"></a>Comment fonctionne le service Layout ?

L’API de disposition extrait le texte, les tableaux, les marques de sélection et les informations structurelles des documents avec une précision exceptionnelle et renvoie une réponse JSON organisée et structurée. Les documents peuvent être dans divers formats et de diverses qualités. Il peut s’agir notamment d’images capturées par téléphone, de documents numérisés et de fichiers PDF numériques. L’API de disposition extrait avec précision la sortie structurée de tous ces documents.

![Exemple de disposition](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Faire un essai

Pour tester le service Layout de Form Recognizer, accédez à l’outil d’exemple d’interface utilisateur en ligne :

> [!div class="nextstepaction"]
> [Form OCR Test Tool (FOTT)](https://fott-preview.azurewebsites.net)

Vous aurez besoin d’un abonnement Azure ([créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services)), ainsi que d’un point de terminaison et d’une clé de [ressource Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) pour tester l’API Layout de Form Recognizer. 

![Capture d’écran de l’exemple d’interface utilisateur. Le texte, les tables et les marques de sélection d’un document sont analysés.](./media/analyze-layout.png)

### <a name="input-requirements"></a>Critères des entrées 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Opération d’analyse d’une disposition

Tout d’abord, appelez l’opération [Analyze Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) (Analyser la disposition). Analyze Layout prend un document (fichier image, TIFF ou PDF) en entrée et en extrait le texte, les tableaux, les marques de sélection et la structure. L’appel retourne un champ d’en-tête de réponse appelé `Operation-Location`. La valeur `Operation-Location` est une URL qui contient l’ID de résultat à utiliser à l’étape suivante.

|En-tête de réponse| URL de résultat |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Sortie de l’ordre de lecture naturel (langues latines uniquement)

Vous pouvez spécifier l’ordre dans lequel les lignes de texte sont générées avec le paramètre de requête `readingOrder`. Utilisez `natural` pour une sortie d’ordre de lecture plus conviviale, comme illustré dans l’exemple suivant. Cette fonctionnalité est prise en charge uniquement pour les langues latines.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Disposition – Exemple d’ordre de lecture" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Sélectionner des numéros de page ou des plages de pages pour l’extraction de texte

Pour les documents volumineux comportant plusieurs pages, utilisez le paramètre de requête `pages` pour indiquer des numéros de page ou des plages de pages spécifiques pour l’extraction de texte. L’exemple suivant montre un document de 10 pages, avec le texte extrait pour les deux cas : toutes les pages (1 à 10) et certaines pages (3 à 6).

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Disposition – Sortie des pages sélectionnées":::

## <a name="the-get-analyze-layout-result-operation"></a>Opération d’obtention du résultat de l’analyse de la disposition

La seconde étape consiste à appeler l’opération d’[obtention du résultat de l’analyse de la disposition](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult). Cette opération prend en entrée l’ID de résultat créé par l’opération d’analyse de la disposition. Elle retourne une réponse JSON qui contient un champ **État** avec les possibles valeurs suivantes. 

|Champ| Type | Valeurs possibles |
|:-----|:----:|:----|
|status | string | `notStarted` : L’opération d’analyse n’a pas commencé.<br /><br />`running` : L’opération d’analyse est en cours.<br /><br />`failed` : L’opération d’analyse a échoué.<br /><br />`succeeded` : L’opération d’analyse a réussi.|

Appelez cette opération de façon itérative jusqu’à ce qu’elle renvoie la valeur `succeeded`. Utilisez un intervalle de 3 à 5 secondes pour éviter de dépasser le taux de demandes par seconde (RPS).

Quand le champ **status** a la valeur `succeeded`, la réponse JSON inclut la mise en page, le texte, les tableaux et les marques de sélection extraits. Les données extraites comprennent les lignes de texte et les mots extraits, les cadres englobants, l’aspect du texte avec indication manuscrite, les tableaux et les marques de sélection avec indication de sélection/non sélection. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Classification manuscrite pour les lignes de texte (Latin uniquement)

La réponse inclut le classement de chaque ligne de texte selon qu’elle est de style manuscrit ou non, avec un score de confiance. Cette fonctionnalité est prise en charge uniquement pour les langues latines. L’exemple suivant illustre la classification manuscrite pour le texte de l’image.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="Exemple de classification d’écriture manuscrite":::

### <a name="sample-json-output"></a>Exemple de sortir JSON

La réponse à l’opération *Get Analyze Layout Result* est une représentation structurée du document avec toutes les informations extraites. Reportez-vous à cet [exemple de fichier de document](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) et cet [exemple de sortie de disposition](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json) structurée.

La sortie JSON comporte deux parties :

* Le nœud `readResults` contient tout le texte reconnu et toutes les marques de sélection. Le texte est organisé par page, puis par ligne, puis par mots individuels. 
* Le nœud `pageResults` contient les tables et les cellules extraites avec leurs cadres englobants, la confiance et une référence aux lignes et aux mots dans « readResults ».

## <a name="example-output"></a>Exemple de sortie

### <a name="text"></a>Texte

L’API de disposition extrait le texte des documents (PDF, TIFF) et des images (JPG, PNG, BMP) avec plusieurs angles et couleurs de texte. Elle accepte les photos de documents, les télécopies, les textes imprimés et/ou manuscrits (en anglais uniquement) et les modes mixtes. Le texte est extrait avec des informations fournies sur les lignes, les mots, les cadres englobants, les scores de confiance et le style (manuscrit ou autre). Toutes les informations textuelles sont incluses dans la section `readResults` de la sortie JSON. 

### <a name="tables"></a>Tables

L’API de disposition extrait les tableaux des documents (PDF, TIFF) et des images (JPG, PNG, BMP). Les documents peuvent être scannés, photographiés ou numériques. Les tableaux peuvent être complexes avec des cellules ou des colonnes fusionnées, avec ou sans bordures et avec des angles irréguliers. Les informations extraites d’un tableau comprennent le nombre de colonnes et de lignes, la portée des lignes et la portée des colonnes. Chaque cellule est extraite avec son cadre englobant et sa référence au texte extrait dans la section `readResults`. Les informations de table se trouvent dans la section `pageResults` de la sortie JSON. 

![Exemple de table](./media/tables-example.jpg)

### <a name="selection-marks"></a>Marques de sélection

L’API de disposition extrait aussi les marques de sélection des documents. Les marques de sélection extraites incluent le cadre englobant, la confiance et l’état (sélectionné/non sélectionné). Les informations sur les marques de sélection sont extraites dans la section `readResults` de la sortie JSON. 

## <a name="next-steps"></a>Étapes suivantes

* Testez l’extraction d’une mise en page à l’aide de l’[outil d’exemple d’interface utilisateur Form Recognizer](https://fott-preview.azurewebsites.net/).
* Suivez un [démarrage rapide Form Recognizer](quickstarts/client-library.md) pour commencer à extraire des layouts dans le langage de développement de votre choix.

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que Form Recognizer ?](./overview.md)
* [Documentation de référence sur l’API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
