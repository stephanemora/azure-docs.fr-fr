---
title: Guide des packages de dessin dans Microsoft Azure Maps Creator (préversion)
description: Apprenez à préparer un paquet de dessin pour le service Conversion Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: eb74ffb55536b18e48a4b5dcea83fac4db20abe0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531624"
---
# <a name="conversion-drawing-package-guide"></a>Guide des packages de dessin Conversion

Ce guide vous montre comment préparer votre package de dessin pour le [service Conversion Azure Maps](/rest/api/maps/v2/conversion) à l’aide de commandes de CAO spécifiques afin de préparer correctement vos fichiers DWG et votre fichier manifeste pour le service Conversion.

Pour commencer, assurez-vous que votre package de dessin est au format .zip et qu’il contient les fichiers suivants :

* Un ou plusieurs fichiers de dessin au format DWG.
* Un fichier manifeste décrivant les fichiers DWG et les métadonnées de l’installation.

Si vous n’avez pas votre propre package à référencer avec ce guide, vous pouvez télécharger l’[exemple de package de dessin](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

Vous pouvez choisir n’importe quel logiciel de CAO pour ouvrir et préparer les fichiers de dessin de votre installation. Toutefois, ce guide a été créé à l’aide du logiciel AutoCAD® d’Autodesk. Toutes les commandes référencées dans ce guide sont destinées à être exécutées à l’aide du logiciel AutoCAD® d’Autodesk.  

>[!TIP]
>Pour plus d’informations sur les spécifications des packages de dessin qui ne sont pas traitées dans ce guide, consultez [Exigences du package de dessin](drawing-requirements.md).

## <a name="glossary-of-terms"></a>Glossaire des termes

Voici quelques termes et définitions importants pour faciliter la lecture de ce guide.

| Terme  | Définition |
|:-------|:------------|
| Couche | Couche AutoCAD DWG du fichier de dessin.|
| Entité | Entité AutoCAD DWG du fichier de dessin. |
| Level  |Zone d’un immeuble à une élévation définie. Par exemple, l’étage d’un immeuble.    |
| Fonctionnalité | Objet combinant une géométrie avec davantage d’informations de métadonnées. |
| Classes de caractéristiques | Blueprint commun pour les caractéristiques. Par exemple, une *unité* est une classe de caractéristiques et un *bureau* est une caractéristique. |

## <a name="step-1-dwg-file-requirements"></a>Étape 1 : Exigences relatives aux fichiers DWG

Lorsque vous préparez les fichiers de dessin de vos installations pour le service Conversion, veillez à respecter les exigences et recommandations préliminaires suivantes :

* Les fichiers de dessin des installations doivent être enregistrés au format DWG, qui est le format de fichier natif du logiciel AutoCAD® d’Autodesk.

* Le service de conversion fonctionne avec le format de fichier DWG AutoCAD.AC1032 correspond à la version interne du format des fichiers DWG, et il apparaît judicieux de sélectionner AC1032 pour la version interne du format de fichier DWG.

* Un fichier DWG ne peut contenir qu’un seul étage. Chaque étage d’une installation doit être fourni dans son propre fichier DWG distinct.  Ainsi, si vous avez cinq étages dans une installation, vous devez créer cinq fichiers DWG distincts.

## <a name="step-2-prepare-the-dwg-files"></a>Étape 2 : Préparer les fichiers DWG

Cette partie du guide vous montre comment utiliser les commandes de CAO pour vous assurer que vos fichiers DWG répondent aux exigences du service Conversion.

Vous pouvez choisir n’importe quel logiciel de CAO pour ouvrir et préparer les fichiers de dessin de votre installation. Toutefois, ce guide a été créé à l’aide du logiciel AutoCAD® d’Autodesk. Toutes les commandes référencées dans ce guide sont destinées à être exécutées à l’aide du logiciel AutoCAD® d’Autodesk.  

### <a name="bind-external-references"></a>Lier les références externes

Chaque étage d’une installation doit être fourni sous la forme d’un fichier DWG. S’il n’y a pas de références externes, il n’y a rien de plus à faire. Toutefois, s’il existe des références externes, elles doivent être liées à un seul dessin. Pour lier une référence externe, vous pouvez utiliser la commande `XREF`. Après la liaison, chaque dessin de référence externe sera ajouté en tant que référence de bloc. Si vous devez apporter des modifications à l’une de ces couches, n’oubliez pas d’éclater les références de bloc en utilisant la commande `XPLODE`.

### <a name="unit-of-measurement"></a>Unité de mesure

Les dessins peuvent être créés en utilisant n’importe quelle unité de mesure. Toutefois, tous les dessins doivent utiliser la même unité de mesure. Ainsi, si un étage de l’installation utilise des millimètres, tous les autres étages (dessins) doivent également être en millimètres. Vous pouvez vérifier ou modifier l’unité de mesure en utilisant la commande `UNITS`.

L’image suivante montre la fenêtre Unités de dessin du logiciel AutoCAD® d’Autodesk que vous pouvez utiliser pour vérifier l’unité de mesure.  

:::image border="true" type="content" source="./media/drawing-package-guide/units.png" alt-text="Fenêtre Unités de dessin du logiciel AutoCAD® d’Autodesk":::

### <a name="alignment"></a>Alignment

Chaque étage d’une installation est fourni sous la forme d’un fichier DWG individuel. Par conséquent, il est possible que les étages ne soient pas parfaitement alignés lorsqu’ils sont empilés les uns sur les autres. Le service Conversion Azure Maps exige que tous les dessins soient alignés sur l’espace physique. Pour vérifier l’alignement, utilisez un point de référence qui peut s’étendre sur plusieurs étages, comme un ascenseur ou une colonne qui s’étend sur plusieurs étages. Vous pouvez visualiser tous les étages en ouvrant un nouveau dessin, puis en utilisant la commande `XATTACH` pour charger tous les dessins d’étage. Si vous devez résoudre des problèmes d’alignement, vous pouvez utiliser les points de référence et la commande `MOVE` pour réaligner les étages qui le nécessitent.

### <a name="layers"></a>Calques

Vérifiez que chaque calque d’un dessin contient des entités d’une seule classe de caractéristiques. Si un calque contient des entités pour les murs, il ne peut pas contenir d’autres caractéristiques telles que des unités ou des portes.  Toutefois, une classe de caractéristiques peut être répartie sur plusieurs calques. Par exemple, vous pouvez avoir trois calques dans le dessin qui contiennent des entités de mur.

En outre, chaque calque possède une liste de types d’entités pris en charge et tout autre type est ignoré. Par exemple, si le calque Étiquette d’unité ne prend en charge que du texte sur une seule ligne, un texte multiligne ou une polyligne sur le même calque est ignoré.

Pour mieux comprendre les calques et les classes de caractéristiques, consultez [Exigences du package de dessin](drawing-requirements.md).

### <a name="exterior-layer"></a>Calque Extérieur

Une seule caractéristique de niveau est créée à partir de chaque calque ou calques extérieurs. Cette caractéristique de niveau définit le périmètre du niveau. Il est important de s’assurer que les entités du calque extérieur répondent aux exigences du calque. Par exemple, une polyligne fermée est prise en charge, mais une polyligne ouverte ne l’est pas. Si votre calque extérieur est composé de plusieurs segments de ligne, ceux-ci doivent être fournis sous la forme d’une polyligne fermée. Pour joindre plusieurs segments de ligne ensemble, sélectionnez tous les segments de ligne et utilisez la commande `JOIN`.

L’image suivante est tirée de l’exemple de package et montre le calque extérieur de l’installation en rouge. Le calque d’unité est désactivé pour faciliter la visualisation.

:::image border="true" type="content" source="./media/drawing-package-guide/exterior.png" alt-text="Calque extérieur d’une installation.":::

### <a name="unit-layer"></a>Calque Unité

Les unités sont des espaces navigables dans le bâtiment, tels que des bureaux, des couloirs, des escaliers et des ascenseurs.Un type d’entité fermé tel que Polygone, Polyligne fermée, Cercle ou Ellipse fermée est nécessaire pour représenter chaque unité. Ainsi, les murs et les portes seuls ne créent pas une unité, car il n’y a pas d’entité qui représente l’unité.  

L’image suivante est tirée de l’[exemple de package de dessin](https://github.com/Azure-Samples/am-creator-indoor-data-examples) et montre le calque d’étiquette d’unité et le calque d’unité en rouge. Tous les autres calques sont désactivés pour faciliter la visualisation. De plus, une unité est sélectionnée pour aider à montrer que chaque unité est une polyligne fermée.  

:::image border="true" type="content" source="./media/drawing-package-guide/unit.png" alt-text="Calque d’unité d’une installation.":::

### <a name="unit-label-layer"></a>Calque d’étiquette d’unité

Si vous souhaitez ajouter une propriété de nom à une unité, vous devez ajouter un calque distinct pour les étiquettes d’unité. Les étiquettes doivent être fournies sous forme d’entités de texte sur une seule ligne qui se trouvent à l’intérieur des limites d’une unité. Une propriété d’unité correspondante doit être ajoutée au fichier manifeste où `unitName` correspond au contenu du texte.  Pour en savoir plus sur toutes les propriétés d’unité prises en charge, consultez [`unitProperties`](#unitproperties).

### <a name="door-layer"></a>Calque Porte

Les portes sont facultatives. Toutefois, elles peuvent être utilisées si vous souhaitez spécifier le(s) point(s) d’entrée d’une unité. Les portes peuvent être dessinées de quelque façon que ce soit s’il s’agit d’un type d’entité pris en charge par le calque de porte. La porte doit chevaucher la limite d’une unité et le bord chevauché de l’unité est alors traité comme une ouverture de l’unité.  

L’image suivante est tirée de l’[exemple de package de dessin](https://github.com/Azure-Samples/am-creator-indoor-data-examples) et montre une unité avec une porte (en rouge) dessinée sur la limite de l’unité.

:::image border="true" type="content" source="./media/drawing-package-guide/door.png" alt-text="Calque de porte d’une installation.":::

### <a name="wall-layer"></a>Calque Mur

Le calque de mur est destiné à représenter les extensions physiques d’une installation, telles que les murs et les colonnes. Le service Conversion Azure Maps perçoit les murs comme des structures physiques qui constituent un obstacle au routage. Dans cette optique, un mur doit être considéré comme une structure physique que l’on peut voir, mais pas traverser. Tout ce qui ne peut pas être vu ne sera pas capturé dans ce calque. Si un mur a des murs intérieurs ou des colonnes à l’intérieur, alors seul l’extérieur doit être capturé.  

## <a name="step-3-prepare-the-manifest"></a>Étape 3 : Préparer le manifeste

Le manifeste du package de dessin est un fichier JSON. Le manifeste indique au service Conversion Azure Maps comment lire les métadonnées et les fichiers DWG de l’installation. Il peut s’agir, par exemple, des informations spécifiques que contient chaque calque DWG ou de l’emplacement géographique de l’installation.

Pour réussir une conversion, toutes les propriétés « obligatoires » doivent être définies. Un exemple de fichier manifeste est disponible dans l’[exemple de package de dessin](https://github.com/Azure-Samples/am-creator-indoor-data-examples). Ce guide ne couvre pas les propriétés prises en charge par le manifeste. Pour plus d’informations sur les propriétés du manifeste, consultez [Propriétés du fichier manifeste](drawing-requirements.md#manifest-file-requirements).

### <a name="building-levels"></a>Niveaux de construction

Le niveau de construction spécifie le fichier DWG à utiliser pour chaque niveau. Un niveau doit avoir un nom de niveau et un ordinal qui décrit l’ordre vertical de chaque niveau. Chaque installation doit avoir un ordinal 0, qui correspond au rez-de-chaussée d’une installation. Un ordinal 0 doit être fourni même si les dessins occupent quelques étages d’une installation. Par exemple, les étages 15 à 17 peuvent être définis comme les ordinaux 0 à 2, respectivement.

L’exemple suivant est tiré de l’[exemple de package de dessin](https://github.com/Azure-Samples/am-creator-indoor-data-examples). L’installation comporte trois niveaux : le sous-sol, le rez-de-chaussée et le 1er étage. Le nom de fichier contient le nom de fichier complet et le chemin d’accès du fichier relatif au fichier manifeste dans le package de dessin .zip.  

```json
    "buildingLevels": { 
      "levels": [ 
       { 
           "levelName": "Basement", 
           "ordinal": -1, 
           "filename": "./Basement.dwg" 
            }, { 

            "levelName": "Ground", 
            "ordinal": 0, 
            "filename": "./Ground.dwg" 
            }, { 

            "levelName": "Level 2", 
            "ordinal": 1, 
             "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
```

### <a name="georeference"></a>georeference

L’objet `georeference` est utilisé pour spécifier l’emplacement géographique de l’installation et le degré de rotation de l’installation. Le point d’origine du dessin doit correspondre à la latitude et à la longitude fournies avec l’objet `georeference`. Angle, exprimé en degrés, entre le nord réel et l’axe vertical (Y) du dessin dans le sens des aiguilles d’une montre.  

### <a name="dwglayers"></a>dwgLayers

L’objet `dwgLayers` est utilisé pour spécifier les noms de calques DWG où il est possible de trouver des classes de caractéristiques. Pour obtenir une installation convertie en propriétés, il est important de fournir les noms de calques corrects. Par exemple, un calque de mur DWG doit être fourni comme un calque de mur et non comme un calque d’unité. Le dessin peut comporter d’autres calques, tels que le mobilier ou la plomberie, mais ils seront ignorés par le service Conversion Azure Maps s’ils ne sont pas spécifiés dans le manifeste.  

Voici un exemple de l’objet `dwgLayers` dans le manifeste.  

```json
"dwgLayers": { 
        "exterior": [ 
            "OUTLINE" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    } 
```

L’image suivante montre les calques du dessin DWG correspondant dans le logiciel AutoCAD® d’Autodesk.

:::image border="true" type="content" source="./media/drawing-package-guide/layer.png" alt-text="Calques dwgLayers dans le logiciel AutoCAD® d’Autodesk":::

### <a name="unitproperties"></a>unitProperties

L’objet `unitProperties` vous permet de définir d’autres propriétés pour une unité, ce que vous ne pouvez pas faire dans le fichier DWG. Il peut s’agir, par exemple, des informations de répertoire d’une unité ou du type de catégorie d’une unité. Une propriété d’unité est associée à une unité en faisant correspondre l’objet `unitName` à l’étiquette du calque `unitLabel`.  

L’image suivante est tirée de l’[exemple de package de dessin](https://github.com/Azure-Samples/am-creator-indoor-data-examples). Elle affiche l’étiquette d’unité qui est associée à la propriété d’unité dans le manifeste.

:::image border="true" type="content" source="./media/drawing-package-guide/unit-property.png" alt-text="Étiquette d’unité qui sera associée à la propriété d’unité dans le manifeste":::

L’extrait de code suivant montre l’objet de propriété d’unité associé à l’unité.  

```json
 "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
```

## <a name="step-4-prepare-the-drawing-package"></a>Étape 4 : Préparer le package de dessin

Vous devriez maintenant avoir préparé tous les dessins DWG pour répondre aux exigences du service Conversion Azure Maps. Un fichier manifeste a également été créé pour vous aider à décrire l’installation. Tous les fichiers doivent être compressés dans un seul fichier d’archive, avec l’extension `.zip`. Il est important que le fichier manifeste soit nommé `manifest.json` et qu’il soit placé dans le répertoire racine du package compressé. Tous les autres fichiers peuvent se trouver dans n’importe quel répertoire du package compressé si le nom de fichier comprend le chemin d’accès relatif au manifeste. Pour obtenir un exemple de package de dessin, consultez l’[exemple de package de dessin](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Création d’un carte d’intérieur du Créateur](tutorial-creator-indoor-maps.md)
