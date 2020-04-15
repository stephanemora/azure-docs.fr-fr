---
title: Obtenir des informations sur un modèle converti
description: Description de tous les paramètres de conversion de modèle
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679299"
---
# <a name="get-information-about-a-converted-model"></a>Obtenir des informations sur un modèle converti

Le fichier arrAsset produit par le service de conversion est destiné exclusivement à l’usage du service de rendu. Toutefois, il peut vous arriver de vouloir accéder à des informations sur un modèle sans démarrer de session de rendu. C’est pourquoi le service de conversion place un fichier JSON à côté du fichier arrAsset dans le conteneur de sortie. Par exemple, si un fichier `buggy.gltf` est converti, le conteneur de sortie contient un fichier nommé `buggy.info.json` en regard de la ressource convertie `buggy.arrAsset`. Ce fichier contient des informations sur le modèle source, le modèle converti et la conversion proprement dite.

## <a name="example-info-file"></a>Exemple de fichier *info*

Voici un exemple de fichier *info* produit par la conversion d’un fichier nommé `buggy.gltf` :

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Informations contenues dans le fichier info

### <a name="the-files-section"></a>Section *files*

Cette section contient les noms de fichiers fournis.

* `input`: Nom du fichier source.
* `output`: Nom du fichier de sortie, quand l’utilisateur a spécifié un nom autre que celui par défaut.

### <a name="the-conversionsettings-section"></a>Section *conversionSettings*

Cette section contient une copie des paramètres [ConversionSettings](configure-model-conversion.md#settings-file) spécifiés lors de la conversion du modèle.

### <a name="the-inputinfo-section"></a>Section *inputInfo*

Cette section enregistre des informations sur le format du fichier source.

* `sourceAssetExtension`: Extension de fichier du fichier source.
* `sourceAssetFormat`: Description du format du fichier source.
* `sourceAssetFormatVersion`: Version du format du fichier source.
* `sourceAssetGenerator`: Nom de l’outil qui a généré le fichier source, s’il est disponible.

### <a name="the-inputstatistics-section"></a>Section *inputStatistics*

Cette section fournit des informations sur la scène source. Il y a souvent des discordances entre les valeurs de cette section et les valeurs équivalentes dans l’outil qui a créé le modèle source. De telles différences sont attendues, car le modèle est modifié pendant les étapes d’exportation et de conversion.

* `numMeshes`: Nombre de composants du maillage, où chaque composant peut référencer un seul matériau.
* `numFaces`: Nombre total de _triangles_ dans le modèle. Notez que le maillage est triangulé lors de la conversion.
* `numVertices`: Nombre total de vertex dans le modèle.
* `numMaterial`: Nombre total de matériaux dans le modèle.
* `numFacesSmallestMesh`: Nombre de triangles dans le plus petit maillage du modèle.
* `numFacesBiggestMesh`: Nombre de triangles dans le plus grand maillage du modèle.
* `numNodes`: Nombre de nœuds dans le graphique de scène du modèle.
* `numMeshUsagesInScene`: Nombre de fois que des nœuds référencent des maillages. Plusieurs nœuds peuvent référencer le même maillage.
* `maxNodeDepth`: Profondeur maximale des nœuds dans le graphique de scène.

### <a name="the-outputinfo-section"></a>Section *outputInfo*

Cette section enregistre des informations générales sur la sortie générée.

* `conversionToolVersion`: Version du convertisseur de modèle.
* `conversionHash`: Hachage des données dans l’arrAsset, qui peut contribuer au rendu. Peut être utilisé pour déterminer si le service de conversion a produit un résultat différent lors de la réexécution sur le même fichier.

### <a name="the-outputstatistics-section"></a>Section *outputStatistics*

Cette section enregistre les informations calculées à partir de la ressources converti.

* `numMeshPartsCreated`: Nombre de maillages dans l’arrAsset. Il peut différer de la valeur `numMeshes` dans la section `inputStatistics`, car l’instanciation est affectée par le processus de conversion.
* `numMeshPartsInstanced`: Nombre de maillages réutilisés dans l’arrAsset.
* `recenteringOffset`: Quand l’option `recenterToOrigin` dans la section [ConversionSettings](configure-model-conversion.md) est activée, cette valeur indique la translation qui ramènerait le modèle converti dans sa position d’origine.
* `boundingBox`: Limites du modèle.

## <a name="next-steps"></a>Étapes suivantes

* [Conversion de modèle](model-conversion.md)
* [Configurer la conversion de modèle](configure-model-conversion.md)
