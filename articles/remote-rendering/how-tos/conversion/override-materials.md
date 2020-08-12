---
title: Remplacer des matériaux pendant la conversion d’un modèle
description: Explique le workflow de remplacement de matériaux au moment de la conversion
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 2e9cb216c100f1732230a90572284bd3f8462584
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433136"
---
# <a name="override-materials-during-model-conversion"></a>Remplacer des matériaux pendant la conversion d’un modèle

Les paramètres de matériau dans le modèle source sont utilisés pour définir les [matériaux PBR](../../overview/features/pbr-materials.md) utilisés par le renderer.
Parfois, la [conversion par défaut](../../reference/material-mapping.md) ne donne pas les résultats souhaités et vous devez apporter des modifications.
Quand un modèle est converti pour être utilisé dans Azure Remote Rendering, vous pouvez fournir un fichier de remplacement de matériau pour personnaliser la façon dont la conversion est effectuée pour chaque matériau.
La section relative à la [configuration de la conversion de modèle](configure-model-conversion.md) contient des instructions sur la déclaration du nom du fichier de remplacement de matériau.

## <a name="the-override-file-used-during-conversion"></a>Fichier de remplacement utilisé pendant la conversion

Pour illustrer cela de façon simple, imaginons qu’un modèle de boîte possède un seul matériau appelé « Default ».
Par ailleurs, supposons que la couleur albedo doit être ajustée pour une utilisation dans ARR.
Dans ce cas, un fichier `box_materials_override.json` peut être créé comme suit :

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

Le fichier `box_materials_override.json` est placé dans le conteneur d’entrée, et `box.ConversionSettings.json` est ajouté à côté de `box.fbx`. Ceci indique à la conversion où trouver le fichier de remplacement (consultez [Configuration de la conversion de modèle](configure-model-conversion.md)) :

```json
{
    "material-override" : "box_materials_override.json"
}
```

Quand le modèle est converti, les nouveaux paramètres s’appliquent.

### <a name="color-materials"></a>Matériaux couleur

Le modèle de [matériau de couleur](../../overview/features/color-materials.md) décrit une surface constamment ombrée indépendante de l’éclairage.
Les matériaux couleur s’avèrent utiles pour les composants créés avec des algorithmes de photogrammétrie, par exemple.
Dans les fichiers de remplacement de matériau, vous pouvez déclarer un matériau comme matériau de couleur en définissant `unlit` sur `true`.

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignorer des cartes de texture spécifiques

Dans certains cas, vous souhaiterez peut-être que le processus de conversion ignore des cartes de texture spécifiques, notamment quand votre modèle a été généré par un outil qui crée des cartes spéciales que le renderer ne comprend pas correctement. Il peut s’agir, par exemple, d’une « OpacityMap » utilisée pour définir autre chose que l’opacité ou d’un modèle où la « NormalMap » est stockée comme « BumpMap ». (Dans ce dernier cas, vous devrez ignorer « NormalMap ». Ainsi, le convertisseur utilisera « BumpMap » comme « NormalMap ».)

Le principe est simple. Ajoutez simplement une propriété appelée `ignoreTextureMaps` et ajoutez les cartes de texture que vous souhaitez ignorer :

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Pour obtenir la liste complète des cartes de texture que vous pouvez ignorer, consultez le schéma JSON ci-dessous.

## <a name="json-schema"></a>Schéma JSON

Le schéma JSON complet pour les fichiers de matériaux est fourni ici. À l’exception de `unlit` et `ignoreTextureMaps`, les propriétés disponibles sont un sous-ensemble des propriétés décrites dans les sections sur les modèles de [matériau de couleur](../../overview/features/color-materials.md) et de [matériau PBR](../../overview/features/pbr-materials.md).

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Matériaux de couleur](../../overview/features/color-materials.md)
* [Matériaux PBR](../../overview/features/pbr-materials.md)