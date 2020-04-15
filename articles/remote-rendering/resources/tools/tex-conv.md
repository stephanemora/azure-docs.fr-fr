---
title: TexConv - Outil de conversion de texture
description: Manuel de l’utilisateur de l’outil de ligne de commande TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678839"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - Outil de conversion de texture

TexConv est un outil de ligne de commande qui permet de traiter les textures issues de formats d’entrée classiques tels que PNG, TGA, JPEG et DDS dans des formats optimisés à des fins de consommation du runtime.
Bien que le scénario le plus courant consiste à convertir un fichier d’entrée unique `A.xxx` dans un format optimisé `B.yyy`, l’outil propose de nombreuses options supplémentaires destinées à des utilisations avancées.

## <a name="command-line-help"></a>Aide relative à la ligne de commande

L’exécution de TexConv. exe avec le paramètre `--help` répertorie toutes les options disponibles. De plus, lors de son exécution, TexConv imprime les options utilisées à des fins de compréhension. Pour plus d’informations, consultez cette sortie.

## <a name="general-usage"></a>Utilisation générale

TexConv produit systématiquement un fichier **de sortie**. Il peut utiliser **plusieurs fichiers d'entrée** à partir desquels assembler la sortie. Pour l’assembly, il a également besoin d’un **mappage de canaux** indiquant le canal (*rouge, vert, bleu* ou *alpha*) à utiliser à partir du fichier d'entrée et à déplacer vers le canal de l’image de sortie.

La ligne de commande la plus simple est la suivante :

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out` spécifie le fichier de sortie et son format
- `-in0` spécifie la première image d'entrée
- `-rgba` indique que l’image de sortie doit utiliser les quatre canaux, et qu'ils doivent être, pris à 1:1 depuis l’image d’entrée

## <a name="multiple-input-files"></a>Fichiers d’entrée multiples

Pour assembler la sortie de plusieurs fichiers d’entrée, spécifiez chaque fichier d’entrée à l’aide de l’option `-in` avec un nombre croissant :

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Lors de l’assemblage d’un cubemap à partir de textures 2D, vous pouvez également utiliser `-right`, `-left`, `-top`, `-bottom`, `-front`, `-back` ou `-px`, `-nx`, `-py`, `-ny`, `-pz`, `-nz`.

Pour mapper ces entrées au fichier de sortie, un mappage de canaux adapté est nécessaire.

## <a name="channel-mappings"></a>Mappages de canaux

Les options de mappage de canaux spécifient l'entrée à partir de laquelle remplir les canaux de sortie donnés. Vous pouvez spécifier l’entrée de chaque canal comme suit :

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Ici, les canaux RVB de la sortie seront remplis à l’aide de la première image d’entrée, mais le rouge et le bleu seront permutés. Le canal alpha de la sortie sera rempli avec les valeurs du canal rouge de la deuxième image d’entrée.

Spécifier le mappage de chaque canal séparément offre plus de flexibilité. Pour des raisons pratiques, la même chose peut être écrite à l’aide d’opérateurs de « réagencement » :

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Canaux de sortie

Les options de mappage de canaux suivantes sont disponibles :

- `-r`, `-g`, `-b`, `-a` : Elles spécifient les affectations de canaux uniques
- `-rg` : Spécifie les affectations des canaux rouge et vert.
- `-rgb` : Spécifie les affectations des canaux rouge, vert et bleu.
- `-rgba` : Spécifie les affectations des quatre canaux.

Mentionner uniquement le canal R, RV ou RVB indique à TexConv de créer un fichier de sortie avec uniquement 1, 2 ou 3 canaux respectivement.

### <a name="input-swizzling"></a>Réagencement de l'entrée

Lorsque vous indiquez la texture d’entrée qui doit remplir le canal de sortie, vous pouvez effectuer un réagencement de l’entrée :

- `-rgba in0` équivaut à `-rgba in0.rgba`
- `-rgba in0.bgra` réagence les canaux d’entrée
- `-rgb in0.rrr` duplique le canal rouge dans tous les canaux

Vous pouvez également utiliser le noir ou le blanc pour remplir les canaux :

- `-rgb in0 -a white` crée une texture de sortie à 4 canaux, mais définit alpha sur entièrement opaque
- `-rg black -b white` crée une texture entièrement bleue

## <a name="common-options"></a>Options courantes

Les options les plus intéressantes sont répertoriées ci-dessous. D’autres options sont répertoriées par `TexConv --help`.

### <a name="output-type"></a>Type de sortie

- `-type 2D` : La sortie correspondra à une image 2D classique.
- `-type Cubemap` : La sortie correspondra à une image de cubemap. Uniquement pris en charge pour les fichiers de sortie DDS. Lorsque cela est spécifié, vous pouvez assembler le cubemap à partir de 6 images d’entrée 2D classiques.

### <a name="image-compression"></a>Compression d'image

- `-compression none` : L’image de sortie sera décompressée.
- `-compression medium` : Si cela est pris en charge, l’image de sortie utilisera la compression sans sacrifier la qualité.
- `-compression high` : Si cela est pris en charge, l’image de sortie utilisera la compression au détriment de la qualité pour un fichier plus petit.

### <a name="mipmaps"></a>Mipmaps

Par défaut, TexConv génère des mipmaps lorsque le format de sortie les prend en charge.

- `-mipmaps none` : Aucun mipmap ne sera pas généré.
- `-mipmaps Linear` : S'ils sont pris en charge, des mipmaps seront générés à l’aide d’un filtre de zone.

### <a name="usage-srgb--gamma-correction"></a>Utilisation (correction sRVB/gamma)

L’option `-usage` spécifie l’objectif de la sortie et indique à TexConv s’il convient d'appliquer la correction gamma aux fichiers d’entrée et de sortie. Son utilisation affecte uniquement les canaux RVB. Le canal alpha est toujours considéré comme contenant des valeurs « linéaires ». Si son utilisation n'est pas spécifiée, le mode « auto » tentera de la détecter à partir du format et du nom de fichier de la première image d’entrée. Par exemple, les formats de sortie des canaux simples et doubles sont toujours linéaires. Consultez la sortie pour connaître la décision prise par TexConv.

- `-usage Linear` : L’image de sortie contient des valeurs qui ne représentent pas de couleurs. C’est généralement le cas pour les textures métalliques et rugueuses, ainsi que pour tous les types de masques.

- `-usage Color` : L’image de sortie représente une couleur, telle que des cartographies diffuses/albedo. L’indicateur sRVB sera défini dans l’en-tête DDS de sortie.

- `-usage HDR` : À des fins d'encodage, le fichier de sortie doit utiliser plus de 8 bits par pixel. Ainsi, toutes les valeurs sont stockées dans un espace linéaire. Pour les textures HDR, peu importe si les données représentent une couleur ou d’autres données.

- `-usage NormalMap` : L’image de sortie représente une cartographie normale à espace tangent. Les valeurs sont normalisées et le calcul des mipmaps est légèrement optimisé.

- `-usage NormalMap_Inverted` : La sortie correspond à une cartographie normale à espace tangent avec Y pointant dans le sens opposé de l’entrée.

### <a name="image-rescaling"></a>Remise à l’échelle des images

- `-minRes 64` : Spécifie la résolution minimale de la sortie. Si l’image d’entrée est plus petite, elle est mise à l’échelle.
- `-maxRes 1024` : Spécifie la résolution maximale de la sortie. Si l’image d’entrée est plus grande, elle est mise à l'échelle.
- `-downscale 1` : En cas de valeur nulle, les images d’entrée sont réduites en fonction de la résolution. Utilisez cette valeur pour appliquer une réduction de la qualité globale.

## <a name="examples"></a>Exemples

### <a name="convert-a-color-texture"></a>Convertir une texture de couleur

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Convertir une cartographie normale

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Créer un cubemap HDR

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

[hdrihaven.com](https://hdrihaven.com/hdris/) est une excellente source pour les cubemaps HDR.

### <a name="bake-multiple-images-into-one"></a>Effectuer un bake de plusieurs images

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Extraire un canal unique

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
