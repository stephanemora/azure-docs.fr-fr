---
title: Phrases et jetons – API Analyse linguistique
titlesuffix: Azure Cognitive Services
description: Apprenez-en davantage sur la séparation des phrases et la segmentation du texte en unités lexicales dans l’API Analyse linguistique.
services: cognitive-services
author: DavidLiCIG
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: davl
ROBOTS: NOINDEX
ms.openlocfilehash: 96c2bd1c11554481e441662a6051620f4b2e2993
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972360"
---
# <a name="sentence-separation-and-tokenization"></a>Séparation des phrases et segmentation du texte en unités lexicales

> [!IMPORTANT]
> La préversion de l’analyse linguistique a été désactivée le 9 août 2018. Nous vous recommandons d’utiliser les [modules d’analyse de texte Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) pour le traitement et l’analyse de texte.

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
La première se termine par une combinaison de point d’interrogation et d’exclamation (également appelée point exclarrogatif).
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
Ensuite, l’anglais dispose de *contractions*, comme « didn’t » ou « it’s », où des mots ont été compressés et abrégés.
Le but de la segmentation est de fractionner la séquence de caractères en mots.

Revenons aux exemples ci-dessus.
Nous avons placé un « point central » (&middot;) entre chaque unité lexicale distincte.

- What &middot; did &middot; you &middot; say &middot; ?!?
- I &middot; did &middot; n't &middot; hear &middot; about &middot; the &middot; director &middot; 's &middot; " &middot; new &middot; proposal &middot; . &middot; "
- It &middot; 's &middot; important &middot; to &middot; Mr. &middot; and &middot; Mrs. &middot; Smith &middot; .

Vous remarquez que la plupart des unités lexicales sont des mots que vous pouvez trouver dans le dictionnaire (par exemple, *important*, *director*).
D’autres ne sont que des marques de ponctuation.
Enfin, il existe des unités lexicales en anglais qui représentent des contractions, comme *n’t* pour *not*, ou qui marquent la possession comme *’s* .
En anglais, cette segmentation du texte en unités lexicales nous permet de traiter le mot *didn’t* et l’expression *did not* de façon plus cohérente.

## <a name="specification"></a>Caractéristique

Il est important de prendre ces décisions quant à ce qui constitue une phrase et une unité lexicale.
Nous nous appuyons sur la spécification du [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) (certains détails supplémentaires sont disponibles sur ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html).
