---
title: Modèles personnalisés et composés de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Découvrez comment créer, utiliser et gérer des modèles personnalisés et composés de Form Recognizer.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 181d96d2e722d7f9b79c47285bab417bf0337133
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754669"
---
# <a name="form-recognizer-custom-and-composed-models"></a>Modèles personnalisés et composés de Form Recognizer

Form Recognizer utilise la technologie de Machine Learning avancée pour détecter et extraire des informations à partir d’images de document et retourner les données extraites dans une sortie JSON structurée. Avec Form Recognizer, vous pouvez effectuer l’apprentissage de modèles personnalisés autonomes ou combiner des modèles personnalisés pour créer des modèles composés.

* **Modèles personnalisés**. Les modèles personnalisés Form Recognizer vous permettent d’analyser et d’extraire des données à partir de formulaires et de documents spécifiques à votre entreprise. Les modèles personnalisés sont entraînés pour vos données et cas d’usage spécifiques.

* **Modèles composés**. Un modèle composé est créé sur la base d’une collection de modèles personnalisés qui sont affectés à un modèle unique englobant les types de formulaire. Quand un document est soumis à un modèle composé, le service effectue une étape de classification pour déterminer le modèle personnalisé qui représente précisément le formulaire présenté pour l’analyse.

:::image type="content" source="media/analyze.png" alt-text="Capture d’écran : outil Form Recognizer, fenêtre de l’analyse de formulaire personnalisé.":::


## <a name="what-is-a-custom-model"></a>Qu’est-ce qu’un modèle personnalisé ?

Un modèle personnalisé est un programme de Machine Learning dont l’apprentissage a pour but de reconnaître les champs de formulaire dans vos contenus spécifiques et d’extraire des paires clé-valeur et des données de table. Vous avez seulement besoin de cinq exemples du même type de formulaire pour commencer. L’apprentissage de votre modèle personnalisé peut s’effectuer avec ou sans jeux de données étiquetés.

## <a name="what-is-a-composed-model"></a>Qu’est-ce qu’un modèle composé ?

Avec les modèles composés, il est possible d’affecter plusieurs modèles personnalisés à un modèle composé qui est appelé avec un ID de modèle unique. Cela est utile quand vous avez effectué l’apprentissage de plusieurs modèles et que vous souhaitez les regrouper pour analyser des types de formulaire similaires. Par exemple, votre modèle composé peut inclure des modèles personnalisés dont l’apprentissage a pour but d’analyser vos approvisionnements, vos équipements et vos bons de commande de mobilier. Au lieu d’essayer de sélectionner manuellement le modèle approprié, vous pouvez utiliser un modèle composé pour déterminer le modèle personnalisé approprié pour chaque analyse et extraction.

## <a name="try-form-recognizer-studio-preview"></a>Essayer Form Recognizer Studio (préversion)

* Form Recognizer Studio est disponible avec l’API (v 3.0) en préversion.

* Analysez des formulaires d’un type spécifique ou unique grâce à notre fonctionnalité de formulaire personnalisé Form Recognizer Studio :

> [!div class="nextstepaction"]
> [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/customform/projects)

## <a name="try-form-recognizer-sample-labeling-tool"></a>Essayer l’outil d’étiquetage des exemples Form Recognizer

Vous pouvez voir comment les données sont extraites de formulaires personnalisés en testant notre outil d'étiquetage des exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, cliquez sur **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le Portail Azure.":::

> [!div class="nextstepaction"]
> [Essayer](https://fott-2-1.azurewebsites.net/projects/create)

Dans l’interface utilisateur de Form Recognizer :

1. Sélectionnez **Utiliser Personnalisé pour effectuer l’apprentissage d’un modèle avec des étiquettes et rechercher des paires clé-valeur**.

      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Capture d’écran : FOTTtool sélection de l’option personnalisée.":::

1. Dans la fenêtre suivante, sélectionnez **Nouveau projet** :

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Capture d’écran : FOTTtools sélectionnez Nouveau projet.":::

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

  > [!TIP]
  > **Données de formation**
  >
  >* Si possible, utilisez des documents PDF utilisant du texte au lieu d’images. Les fichiers PDF numérisés sont traités comme des images.
  > * Pour les formulaires remplis, utilisez les exemples dont les champs sont tous renseignés.
  > * Utilisez des formulaires avec des valeurs différentes dans chaque champ.
  >* Si vos images de formulaire sont de faible qualité, utilisez un plus grand jeu de données (par exemple, 10 à 15 images).

> [!NOTE]
> L'[outil d’étiquetage des exemples](https://fott-2-1.azurewebsites.net/) ne prend pas en charge le format de fichier BMP. Il s’agit d’une limitation de l’outil et non du service Form Recognizer.

## <a name="supported-languages-and-locales"></a>Langues et régions prises en charge

 La préversion de Form Recognizer introduit une prise en charge linguistique supplémentaire pour des modèles personnalisés. Pour obtenir la liste complète des textes manuscrits et imprimés pris en charge, *consultez* [Prise en charge de la langue](language-support.md#layout-and-custom-model).

## <a name="form-recognizer-preview-v30"></a>Form Recognizer préversion v3.0

 Form Recognizer v3.0 (préversion) introduit plusieurs nouvelles fonctionnalités et capacités :

* **L'API de modèle personnalisé (v 3.0)** prend en charge la détection de signatures pour les formulaires personnalisés. Lors de l’apprentissage de modèles personnalisés, vous pouvez spécifier certains champs comme signatures.  Lorsqu’un document est analysé avec votre modèle personnalisé, il indique si une signature a été détectée ou non.

* Suivez le [**Guide de migration de Form Recognizer v3.0**](v3-migration-guide.md) pour apprendre à utiliser la version préliminaire dans vos applications et vos flux de travail.

* Découvrez notre [**API REST (préversion)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) pour en savoir plus sur la préversion et les nouvelles fonctionnalités.

### <a name="try-signature-detection"></a>Tester la détection de signature

1. [**Créez votre jeu de données d'apprentissage**](build-training-data-set.md#custom-model-input-requirements).

1. Accédez à l’[**outil d’étiquetage des exemples Form Recognizer**](https://fott-preview-private.azurewebsites.net), puis sélectionnez l’option permettant d’**utiliser un modèle personnalisé pour effectuer l'apprentissage d’un modèle avec des étiquettes et obtenir des paires clé-valeur** :

    :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Capture d’écran : FOTTtools sélection de l’option personnalisée.":::

1. Dans la fenêtre suivante, sélectionnez **Nouveau projet** :

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Capture d’écran : FOTTtools sélectionnez Nouveau projet.":::

1. Suivez les [**exigences d’entrée de modèle personnalisé**](build-training-data-set.md#custom-model-input-requirements).

1. Créez une étiquette avec le type **Signature**.

1. **Étiquetez vos documents**.  Pour les champs de signature, l’utilisation de l’étiquetage des régions est recommandée car elle offre une meilleure précision.

1. Une fois votre jeu d’apprentissage étiqueté, vous pouvez **effectuer l'apprentissage de votre modèle personnalisé** et l’utiliser pour analyser des documents. Les champs de signature spécifient si une signature a été détectée ou non.

## <a name="next-steps"></a>Étapes suivantes

* Suivre un démarrage rapide Form Recognizer :

  > [!div class="nextstepaction"]
  > [Démarrage rapide Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Découvrir notre API REST :

    > [!div class="nextstepaction"]
    > [API Form Recognizer v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
