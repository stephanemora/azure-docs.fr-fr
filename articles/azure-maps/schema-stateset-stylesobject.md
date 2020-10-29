---
title: StylesObject pour les cartes dynamiques Azure Maps
description: Guide de référence de la syntaxe et du schéma JSON pour le StylesObject utilisé lors de la création de cartes dynamiques Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8eb4e49e6c0e3f011015d40b8eca036d5218674c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891697"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Guide de référence du schéma StylesObject pour les cartes dynamiques

Cet article est un guide de référence de la syntaxe et du schéma JSON pour `StylesObject`. `StylesObject` est un tableau de `StyleObject` représentant des styles de stateset. Utilisez le [service État de la fonctionnalité](/rest/api/maps/featurestate) d’Azure Maps Creator pour appliquer vos styles de stateset à des caractéristiques de données de cartes d’intérieur. Une fois que vous avez créé vos styles de stateset et que vous les avez associés à des caractéristiques de cartes d’intérieur, vous pouvez les utiliser pour créer des cartes d’intérieur dynamiques. Pour plus d’informations sur la création de cartes d’intérieur dynamiques, consultez [Implémenter des styles dynamiques pour les cartes d’intérieur du Créateur](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>StyleObject

Un `StyleObject` est un [`BooleanTypeStyleRule`](#booleantypestylerule) ou un [`NumericTypeStyleRule`](#numerictypestylerule).

Le code JSON ci-dessous montre un `BooleanTypeStyleRule` nommé `occupied` et un `NumericTypeStyleRule` nommé `temperature`.

```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 Un `NumericTypeStyleRule` est un [`StyleObject`](#styleobject) et est constitué des propriétés suivantes :

| Propriété | Type | Description | Obligatoire |
|-----------|----------|-------------|-------------|
| `keyName` | string | *État* ou nom de propriété dynamique. Un `keyName` doit être unique au sein du tableau `StyleObject`.| Oui |
| `type` | string | La valeur est « numérique ». | Oui |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Tableau de plages de style numériques avec les couleurs associées. Chaque plage définit une couleur à utiliser quand la valeur de l’ *état* correspond à la plage.| Oui |

### <a name="numberruleobject"></a>NumberRuleObject

Un `NumberRuleObject` se compose d’un [`RangeObject`](#rangeobject) et d’une propriété `color`. Si la valeur d’ *état* est comprise dans la plage, sa couleur d’affichage sera celle spécifiée dans la propriété `color`.

Si vous définissez plusieurs plages qui se chevauchent, la couleur choisie est celle qui est définie dans la première plage satisfaite.

Dans l’exemple JSON suivant, les deux plages seront satisfaites quand la valeur d’ *état* sera comprise entre 50 et 60. Toutefois, la couleur utilisée sera `#343deb`, car il s’agit de la première plage de la liste qui a été satisfaite.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Propriété | Type | Description | Obligatoire |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject) définit un ensemble de conditions de plage logique qui, si `true`, modifient la couleur d’affichage de l’ *état* en lui affectant celle spécifiée dans la propriété `color`. Si `range` n’est pas spécifié, la couleur définie dans la propriété `color` sera toujours utilisée.   | Non |
| `color` | string | Couleur à utiliser quand la valeur d’état est comprise dans la plage. La propriété `color` est une chaîne JSON ayant l’un des formats suivants : <ul><li> Valeurs hexadécimales de style HTML </li><li> RGB (« #ff0 », « #ffff00 », « rgb(255, 255, 0) »)</li><li> RGBA (« rgba(255, 255, 0, 1) »)</li><li> HSL(« hsl(100, 50%, 50%) »)</li><li> HSLA(« hsla(100, 50%, 50%, 1) »)</li><li> Des noms de couleurs HTML prédéfinis, tels que le jaune et le bleu.</li></ul> | Oui |

### <a name="rangeobject"></a>RangeObject

`RangeObject` définit une valeur de plage numérique d’un [`NumberRuleObject`](#numberruleobject). Pour que la valeur d’ *état* soit comprise dans la plage, toutes les conditions définies doivent être remplies. 

| Propriété | Type | Description | Obligatoire |
|-----------|----------|-------------|-------------|
| `minimum` | double | Tout nombre x où x ≥ `minimum`.| Non |
| `maximum` | double | Tout nombre x où x ≤ `maximum`. | Non |
| `exclusiveMinimum` | double | Tout nombre x où x > `exclusiveMinimum`.| Non |
| `exclusiveMaximum` | double | Tout nombre x où x < `exclusiveMaximum`.| Non |

### <a name="example-of-numerictypestylerule"></a>Exemple de NumericTypeStyleRule

Le code JSON suivant illustre un *état* `NumericTypeStyleRule` nommé `temperature`. Dans cet exemple, le [`NumberRuleObject`](#numberruleobject) contient deux plages de température définies et les styles de couleur qui leur sont associés. Si la plage de températures est 50-69, l’affichage doit utiliser la couleur `#343deb`.  Si la plage de températures est 31-70, l’affichage doit utiliser la couleur `#eba834`.

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

Un `BooleanTypeStyleRule` est un [`StyleObject`](#styleobject) et est constitué des propriétés suivantes :

| Propriété | Type | Description | Obligatoire |
|-----------|----------|-------------|-------------|
| `keyName` | string |  *État* ou nom de propriété dynamique.  Un `keyName` doit être unique au sein d’un tableau de styles.| Oui |
| `type` | string |La valeur est « booléenne ». | Oui |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)[1]| Paire booléenne avec des couleurs pour les valeurs d *’état* `true` et `false`.| Oui |

### <a name="booleanruleobject"></a>BooleanRuleObject

Un `BooleanRuleObject` définit des couleurs pour les valeurs `true` et `false`.

| Propriété | Type | Description | Obligatoire |
|-----------|----------|-------------|-------------|
| `true` | string | Couleur à utiliser quand la valeur d’ *état* est `true`. La propriété `color` est une chaîne JSON ayant l’un des formats suivants : <ul><li> Valeurs hexadécimales de style HTML </li><li> RGB (« #ff0 », « #ffff00 », « rgb(255, 255, 0) »)</li><li> RGBA (« rgba(255, 255, 0, 1) »)</li><li> HSL(« hsl(100, 50%, 50%) »)</li><li> HSLA(« hsla(100, 50%, 50%, 1) »)</li><li> Des noms de couleurs HTML prédéfinis, tels que le jaune et le bleu.</li></ul>| Oui |
| `false` | string | Couleur à utiliser quand la valeur d’ *état* est `false`. | Oui |

### <a name="example-of-booleantypestylerule"></a>Exemple de BooleanTypeStyleRule

Le code JSON suivant illustre un *état* `BooleanTypeStyleRule` nommé `occupied`. Le [`BooleanRuleObject`](#booleanruleobject) définit des couleurs pour les valeurs `true` et `false`.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```