---
title: Création d’un jeu de données d’apprentissage pour un modèle personnalisé - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Découvrez comment vous assurer que votre jeu de données d’apprentissage est optimisé pour l’entraînement d’un modèle Form Recognizer.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380624"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Créer un jeu de données d’apprentissage pour un modèle personnalisé

Lorsque vous utilisez le modèle Form Recognizer personnalisé, vous fournissez vos propres données d’entraînement afin d’entraîner le modèle en fonction de vos formulaires spécifiques au secteur. Vous pouvez entraîner un modèle avec cinq formulaires remplis ou avec un formulaire vide (vous devez inclure le mot « empty » (vide) dans le nom de fichier) et deux formulaires remplis. Même si vous avez suffisamment de formulaires remplis pour effectuer l’entraînement, vous pouvez améliorer la précision du modèle en ajoutant un formulaire vide à votre jeu de données d’apprentissage.

Si vous souhaitez utiliser des données d’apprentissage étiquetées manuellement, vous devez commencer par au moins cinq formulaires du même type. Vous pouvez également utiliser des formulaires sans étiquette et un formulaire vide au sein du même jeu de données.

## <a name="training-data-tips"></a>Conseils relatifs aux données d’entraînement

Il est important d’utiliser un jeu de données optimisé pour l’entraînement. Suivez les conseils ci-dessous pour vous assurer que l’opération [Entraîner un modèle personnalisé](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) vous fournisse les meilleurs résultats :

* Si possible, utilisez des documents PDF utilisant du texte au lieu d’images. Les fichiers PDF numérisés sont traités comme des images.
* Pour les formulaires remplis, utilisez les exemples dont les champs sont tous renseignés.
* Utilisez des formulaires avec des valeurs différentes dans chaque champ.
* Si vos images de formulaire sont de faible qualité, utilisez un plus grand jeu de données (par exemple, 10 à 15 images).
* La taille totale du jeu de données d’apprentissage peut atteindre jusqu’à 500 pages.

## <a name="general-input-requirements"></a>Critères généraux des entrées

Vérifiez que votre jeu de données d’apprentissage respecte également les critères d’entrée pour l’ensemble du contenu de Form Recognizer. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Charger vos données d’entraînement

Une fois que vous avez combiné l’ensemble des documents de formulaire que vous utiliserez pour l’entraînement, vous devez charger cet ensemble sur un conteneur de stockage blob Azure. Si vous ignorez comment créer un compte de stockage Azure avec un conteneur, consultez le [démarrage rapide du stockage Azure pour le portail Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Organiser vos données dans des sous-dossiers (facultatif)

Par défaut, l’API [Entraîner un modèle personnalisé](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) utilisera uniquement les formulaires situés à la racine de votre conteneur de stockage. Toutefois, l’entraînement peut être effectué avec des données dans les sous-dossiers si vous le spécifiez dans l’appel d’API. Normalement, le corps de l’appel [Entraîner un modèle personnalisé](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) présente la forme suivante, où `<SAS URL>` correspond à l’URL de signature d’accès partagé de votre conteneur :

```json
{
  "source":"<SAS URL>"
}
```

Si vous ajoutez le contenu suivant au corps de la demande, l’API sera entraînée en utilisant les documents situés dans les sous-dossiers. Le champ `"prefix"` est facultatif et limitera le jeu de données d’apprentissage de manière à utiliser uniquement les fichiers dont les chemins d’accès commencent par la chaîne donnée. Ainsi, avec une valeur de `"Test"` par exemple, l’API n’examinera que les fichiers ou les dossiers qui commencent par le mot « Test ».

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer un jeu de données d’apprentissage, suivez un guide de démarrage rapide pour entraîner un modèle Form Recognizer personnalisé et commencer à l’utiliser sur vos formulaires.

* [Démarrage rapide : entraîner un modèle et extraire des données de formulaire à l’aide de cURL](./quickstarts/curl-train-extract.md)
* [Démarrage rapide : entraîner un modèle et extraire des données de formulaire à l’aide d’une API REST avec Python](./quickstarts/python-train-extract.md)
* [Entraîner avec des étiquettes en utilisant l’API REST et Python](./quickstarts/python-labeled-data.md)