---
title: Phrases et unités lexicales dans l’API Analyse linguistique | Microsoft Docs
description: Apprenez-en plus sur la séparation des phrases et la segmentation du texte en unités lexicales dans l’API Analyse linguistique dans Cognitive Services.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: davl
ms.openlocfilehash: 78e539f365728ad540308e9cfb07af44bf6d8fe7
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084040"
---
# <a name="sentence-separation-and-tokenization"></a>Séparation des phrases et segmentation du texte en unités lexicales

## <a name="background-and-motivation"></a>Contexte et motivation

Lorsque vous disposez d’un corps de texte, la première étape de l’analyse consiste à le fractionner en phrases et unités lexicales.

### <a name="sentence-separation"></a>Séparation des phrases

À première vue, fractionner un texte en phrases est simple : il suffit de trouver les marqueurs de fin de phrases, et de les fractionner à cet endroit.
Toutefois, ces marques sont souvent complexes et ambiguës.

Considérez l'exemple de texte suivant :

> What did you say?!? I didn't hear about the director's "new proposal." It's important to Mr. and Mrs. Smith.

Ce texte contient trois phrases :

- What did you say?!?
- I didn't hear about the director's "new proposal."
- It's important to Mr. and Mrs. Smith.

Vous remarquez que la fin de chaque phrase est marquée de façon différente.
La première se termine par une combinaison de point d’interrogation et d’exclamation. (aussi appelé point exclarrogatif).
La deuxième se termine par un point, mais le guillemet qui suit devrait se situer avant.
Dans la troisième phrase, vous pouvez que ce même point peut être utilisé pour indiquer une abréviation.
La ponctuation de ces phrases fournit de bons indices, mais il faut aller plus pour pouvoir identifier les véritables limites d’une phrase.

### <a name="tokenization"></a>Segmentation du texte en unités lexicales

La tâche suivante consiste à segmenter ces phrases en unités lexicales.
Dans la plupart des cas, les unités lexicales sont délimitées par des espaces blancs en anglais.
(Trouver des unités lexicales ou mots est bien plus facile en anglais qu’en chinois, en raison de l’absence globale d’espace entre les mots.
La première phrase peut s’écrire « Whatdidyousay? »)

Il existe quelques cas complexes.
Tout d’abord, la ponctuation doit souvent (mais pas toujours) être séparée de son contexte.
Ensuite, l’anglais dispose de *contractions*, comme « didn’t » ou « it’s », où des mots ont été compressés et abrégés. Le but de la segmentation est de fractionner la séquence de caractères en mots.

Revenons aux exemples ci-dessus.
Nous avons placé un « point central » (&middot;) entre chaque unité lexicale distincte.

- What &middot; did &middot; you &middot; say &middot; ?!?
- I &middot; did &middot; n't &middot; hear &middot; about &middot; the &middot; director &middot; 's &middot; " &middot; new &middot; proposal &middot; . &middot; "
- It &middot; 's &middot; important &middot; to &middot; Mr. &middot; and &middot; Mrs. &middot; Smith &middot; .

Vous remarquez que la plupart des unités lexicales sont des mots que vous pouvez trouver dans le dictionnaire (par exemple, *important*, *director*).
D’autres ne sont que des marques de ponctuation.
Enfin, il existe des unités lexicales qui représentent des contractions comme *n’t* pour *not*, et qui marquent la possession comme *’s*, etc. Cette segmentation du texte en unités lexicales nous permet de gérer le mot *didn’t* et l’expression *did not* de façon plus consistante, par exemple.

## <a name="specification"></a>Caractéristique

Il est important de prendre ces décisions quant à ce qui constitue une phrase et une unité lexicale.
Nous nous appuyons sur la spécification du [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42) (certains détails supplémentaires sont disponibles sur ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html).
