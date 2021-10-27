---
title: Dispositions - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Découvrez les concepts liés à l’analyse des dispositions avec l’API Form Recognizer - utilisation et limites.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 913eb155820832936295a6cc9df6a22175a9bec5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041972"
---
# <a name="form-recognizer-layout-model"></a>Modèle de disposition Form Recognizer

L’API de disposition d’Azure Form Recognizer extrait du texte, des tableaux, des marques de sélection et des informations de structure à partir de documents (PDF, TIFF) et d’images (JPG, PNG, BMP). Elle permet aux clients de prendre des documents dans différents formats et de retourner des représentations de données structurées des documents. Elle combine une version améliorée de nos puissantes capacités de [reconnaissance optique de caractères (OCR)](../../cognitive-services/computer-vision/overview-ocr.md) avec des modèles de deep learning (apprentissage approfondi) pour extraire du texte, des tableaux, des marques de sélection et la structure du document.

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>Exemple de formulaire traité avec la fonctionnalité de disposition [Outil d’étiquetage des exemples Form Recognizer](https://fott-2-1.azurewebsites.net/) :

:::image type="content" source="media/layout-demo.gif" alt-text="{alt-text}":::

**Fonctionnalités d’extraction des données**

| **Modèle de disposition**   | **Extraction de texte**   | **Marques de sélection**   | **Tables**  |
| --- | --- | --- | --- |
| Layout  | ✓  | ✓  | ✓  |

## <a name="try-form-recognizer-studio-preview"></a>Essayer Form Recognizer Studio (préversion)

* Form Recognizer Studio est disponible avec l’API (v 3.0) en préversion.

* Extrayez des tables, des cases à cocher et du texte à partir de formulaires et de documents à l’aide de notre fonctionnalité de disposition Form Recognizer Studio :

> [!div class="nextstepaction"]
> [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/layout)

## <a name="try-form-recognizer-sample-labeling-tool"></a>Essayer l’outil d’étiquetage des exemples Form Recognizer

Vous pouvez voir comment les données de disposition sont extraites en essayant notre outil d’étiquetage des exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, cliquez sur **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le Portail Azure.":::

* Un document de formulaire. Vous pouvez utiliser notre [exemple de document de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf).

> [!div class="nextstepaction"]
  > [Essayer](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  Dans l’interface utilisateur de Form Recognizer :

  1. Sélectionnez **Utiliser le service Disposition pour obtenir du texte, des tables et des marques de sélection**.
  1. Dans le menu déroulant, sélectionnez **fichier local** .
  1. Téléchargez votre fichier et sélectionnez **Exécuter la disposition**

  :::image type="content" source="media/try-layout.png" alt-text="Capture d’écran : capture d’écran : menu de sélection de fichier source de disposition du menu déroulant de l’outil d’étiquetage des exemples.":::

## <a name="input-requirements"></a>Critères des entrées

* Pour de meilleurs résultats, fournissez une photo nette ou une copie de qualité par document.
* Formats de fichier pris en charge : JPEG, PNG, BMP, TIFF et PDF (texte incorporé ou numérisé). Les PDF avec du texte incorporé sont préférables pour éviter tout risque d’erreur au niveau de l’extraction et de l’emplacement des caractères.
* Pour PDF et TIFF, il est possible de traiter jusqu’à 2 000 pages (avec un abonnement gratuit, seules les deux premières pages sont traitées).
* La taille du fichier doit être inférieure à 50 Mo.
* Les dimensions des images doivent être comprises entre 50 x 50 et 10 000 x 10 000 pixels.
* Les dimensions des PDF vont jusqu’à 17x17 pouces, ce qui correspond au format papier Legal, A3 ou plus petit.
* La taille totale des données d’entraînement doit être de 500 pages maximum.
* Si vos fichiers PDF sont verrouillés par mot de passe, vous devez supprimer le verrou avant leur envoi.
* Pour l’apprentissage non supervisé (sans données étiquetées) :
  * Les données doivent contenir des clés et des valeurs.
  * Les clés doivent apparaître au-dessus ou à gauche des valeurs, pas en dessous ni à droite.

> [!NOTE]
> L'[outil d’étiquetage des exemples](https://fott-2-1.azurewebsites.net/) ne prend pas en charge le format de fichier BMP. Il s’agit d’une limitation de l’outil et non du service Form Recognizer.

## <a name="supported-languages-and-locales"></a>Langues et régions prises en charge

 La préversion de la version préliminaire de Form Recognizer introduit une prise en charge linguistique supplémentaire pour le modèle de disposition. Pour obtenir la liste complète des textes manuscrits et imprimés pris en charge, *consultez* [Prise en charge de la langue](language-support.md#layout-and-custom-model).

## <a name="features"></a>Fonctionnalités

### <a name="tables-and-table-headers"></a>Tableaux et en-têtes de tableau

L’API de disposition extrait des tableaux dans la section `pageResults` de la sortie JSON. Les documents peuvent être scannés, photographiés ou numériques. Les tableaux peuvent être complexes avec des cellules ou des colonnes fusionnées, avec ou sans bordures et avec des angles irréguliers. Les informations extraites d’un tableau comprennent le nombre de colonnes et de lignes, la portée des lignes et la portée des colonnes. Chaque cellule avec son cadre englobant est une sortie avec des informations indiquant si elle soit reconnue comme faisant partie, ou non, d’un en-tête. Les cellules d’en-tête prédites du modèle peuvent s’étendre sur plusieurs lignes et ne sont pas nécessairement les premières lignes d’un tableau. Elles fonctionnent également avec les tableaux pivotés. Chaque cellule de tableau comprend également le texte complet avec des références aux mots individuels de la section `readResults`.

:::image type="content" source="./media/layout-table-headers-example.png" alt-text="Sortie des en-têtes de tableau de disposition":::

### <a name="selection-marks"></a>Marques de sélection

L’API de disposition extrait aussi les marques de sélection des documents. Les marques de sélection extraites incluent le cadre englobant, la confiance et l’état (sélectionné/non sélectionné). Les informations sur les marques de sélection sont extraites dans la section `readResults` de la sortie JSON.

:::image type="content" source="./media/layout-selection-marks.png" alt-text="Sortie des marques de sélection de disposition":::

### <a name="text-lines-and-words"></a>Lignes de texte et mots

L’API de disposition extrait du texte des documents et des images avec plusieurs angles et couleurs de texte. Elle accepte les photos de documents, les télécopies, les textes imprimés et/ou manuscrits (en anglais uniquement) et les modes mixtes. Le texte est extrait avec des informations fournies sur les lignes, les mots, les cadres englobants, les scores de confiance et le style (manuscrit ou autre). Toutes les informations textuelles sont incluses dans la section `readResults` de la sortie JSON.

:::image type="content" source="./media/layout-text-extraction.png" alt-text="Sortie d’extraction de texte de disposition":::

### <a name="natural-reading-order-for-text-lines-latin-only"></a>Ordre de lecture naturel pour les lignes de texte (langues latines uniquement)

Vous pouvez spécifier l’ordre dans lequel les lignes de texte sont générées avec le paramètre de requête `readingOrder`. Utilisez `natural` pour une sortie d’ordre de lecture plus conviviale, comme illustré dans l’exemple suivant. Cette fonctionnalité est prise en charge uniquement pour les langues latines.

:::image type="content" source="./media/layout-reading-order-example.png" alt-text="Disposition – Exemple d’ordre de lecture" lightbox="../../cognitive-services/Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Classification manuscrite pour les lignes de texte (Latin uniquement)

La réponse inclut le classement de chaque ligne de texte selon qu’elle est de style manuscrit ou non, avec un score de confiance. Cette fonctionnalité est prise en charge uniquement pour les langues latines. L’exemple suivant illustre la classification manuscrite pour le texte de l’image.

:::image type="content" source="./media/layout-handwriting-classification.png" alt-text="Exemple de classification d’écriture manuscrite":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Sélectionner des numéros de page ou des plages de pages pour l’extraction de texte

Pour les documents volumineux comportant plusieurs pages, utilisez le paramètre de requête `pages` pour indiquer des numéros de page ou des plages de pages spécifiques pour l’extraction de texte. L’exemple suivant montre un document de 10 pages, avec le texte extrait pour les deux cas : toutes les pages (1 à 10) et certaines pages (3 à 6).

:::image type="content" source="./media/layout-select-pages-for-text.png" alt-text="Disposition – Sortie des pages sélectionnées":::

## <a name="form-recognizer-preview-v30"></a>Form Recognizer préversion v3.0

 La version préliminaire de Form Recognizer introduit plusieurs nouvelles fonctionnalités.

* Suivez le [**Guide de migration de Form Recognizer v3.0**](v3-migration-guide.md) pour apprendre à utiliser la version préliminaire dans vos applications et vos flux de travail.

* Découvrez notre [**API REST (préversion)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) pour en savoir plus sur la préversion et les nouvelles fonctionnalités.

## <a name="next-steps"></a>Étapes suivantes

* Suivre un démarrage rapide Form Recognizer :

  > [!div class="nextstepaction"]
  > [Démarrage rapide Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Découvrir notre API REST :

    > [!div class="nextstepaction"]
    > [API Form Recognizer v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)