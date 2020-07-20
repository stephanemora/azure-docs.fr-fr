---
title: Convertir en répertoire d’images
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Convertir en répertoire d’images pour convertir un jeu de données au format de répertoire d’images.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: b29b5fa1beb19bc055f94c56b064ae2c0ae175b5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171140"
---
# <a name="convert-to-image-directory"></a>Convertir en répertoire d’images

Cet article explique comment utiliser le module Convertir en répertoire d’images pour faciliter la conversion d’un jeu de données d’images vers le type de données « Répertoire d’images », qui est un format de données standardisé dans les tâches associées aux images, telles que la classification d’images dans le concepteur Azure Machine Learning (préversion).

## <a name="how-to-use-convert-to-image-directory"></a>Utilisation du module Convertir en répertoire d’images  

1.  Ajoutez le module **Convertir en répertoire d’images** à votre expérience. Vous trouverez ce module dans la catégorie « Computer Vision/Image Data Transformation » (Vision par ordinateur/Transformation de données d’image) de la liste des modules. 

2.  [Inscrivez un jeu de données d’image](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) et connectez-le au port d’entrée de module. Assurez-vous qu’une image figure dans le jeu de données d’entrée. 
    Les formats de jeu de données suivants sont pris en charge :

    - Fichier compressé avec l’une des extensions suivantes : « .zip », « .tar », « .gz », « .bz2 ».
    - Dossier contenant des images. **Il est fortement recommandé de commencer par compresser ce dossier, puis d’utiliser le fichier compressé comme jeu de données**.

    > [!WARNING]
    > Vous **ne pouvez pas** utiliser le module **Importer des données** pour importer un jeu de données image, car le type de sortie du module **Importer des données** est le répertoire DataFrame, qui contient uniquement la chaîne de chemin de fichier.
    

    > [!NOTE]
    > Si vous utilisez un jeu de données d’image dans un apprentissage supervisé, une étiquette est requise.
    > Pour la tâche de classification d’image, l’étiquette peut être générée en tant que « catégorie » d’image dans la sortie du module si le jeu de données d’image est organisé au format Torchvision ImageFolder. Dans le cas contraire, seules les images sont enregistrées sans étiquette. Voici un exemple de la façon dont vous pouvez organiser le jeu de données d’image pour obtenir une étiquette, en utilisant la catégorie d’image comme nom de sous-dossier. Pour plus d’informations, reportez-vous aux [jeux de données Torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder).
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  Envoyez le pipeline.

## <a name="results"></a>Résultats

La sortie du module **Convertir en répertoire d’images** est au format de répertoire d’images et peut être connectée à d’autres modules associés à des images dont le format du port d’entrée est également Répertoire d’images.

## <a name="technical-notes"></a>Notes techniques 

###  <a name="expected-inputs"></a>Entrées attendues  

| Nom          | Type                  | Description   |
| ------------- | --------------------- | ------------- |
| Jeu de données d'entrée | AnyDirectory, ZipFile | Jeu de données d'entrée |

###  <a name="output"></a>Output  

| Nom                   | Type           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Répertoire d’images de sortie | ImageDirectory | Répertoire d’images de sortie |

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 
