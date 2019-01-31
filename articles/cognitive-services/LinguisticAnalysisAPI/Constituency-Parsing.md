---
title: Analyse de circonscription - API Analyse linguistique
titlesuffix: Azure Cognitive Services
description: Découvrez comment l’analyse de circonscription, aussi appelée « analyse de structure d’expressions », identifie des expressions dans un texte.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 778005ee321c981fec1d1271cee54229bb3a522f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214170"
---
# <a name="constituency-parsing"></a>Analyse de circonscription

> [!IMPORTANT]
> La préversion de l’analyse linguistique a été désactivée le 9 août 2018. Nous vous recommandons d’utiliser les [modules d’analyse de texte Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) pour le traitement et l’analyse de texte.

Le but de l’analyse de circonscription (aussi appelée « analyse de structure d’expressions ») est d’identifier les expressions dans un texte.
Cela peut être utile lors de l’extraction d’informations dans un texte.
Les clients pourraient vouloir trouver des noms de fonctionnalités ou des expressions clé dans un corps de texte conséquent, et voir les modificateurs et actions entourant chaque groupe de mots.

## <a name="phrases"></a>Expressions

Pour un linguiste, une *expression* est bien plus qu’une simple suite de mots.
Pour constituer une expression, un groupe de mots doit être assemblé et chacun joue un rôle spécifique dans la phrase.
Ces mots peuvent être déplacés et remplacés, et la phrase doit rester fluide et grammaticalement correcte.

Analysons cette phrase

> Je veux trouver un nouveau véhicule hybride avec Bluetooth.

Cette phrase contient le groupe nominal : « un nouveau véhicule hybride avec Bluetooth ».
Comment savoir s’il s’agit d’une expression ?
Nous pouvons réécrire la phrase (de façon éloquente) en déplaçant ce groupe nominal en début de phrase :

> Un nouveau véhicule hybride avec Bluetooth, je veux trouver.

Ou bien remplacer ce groupe par un groupe à la fonction et au sens similaire, comme « une belle voiture neuve » :

> Je veux trouver une belle voiture neuve.

Si nous avions choisi un autre sous-ensemble de mots, ces modifications auraient créé des phrases bizarres et illisibles.
Examinons ce qu’il se passe en déplaçant « trouver un nouveau » en début de phrase :

> Trouver un nouveau je veux véhicule hybride avec Bluetooth.

Le résultat est très difficile à lire et à comprendre.

L’objectif d’un analyseur est de détecter des expressions de ce type.
Chose intéressante, dans un langage naturel, les expressions ont tendance à être imbriquées les unes dans les autres.
Il est possible de représenter naturellement ces expressions sous forme d’arborescence, comme ci-après :

![Arborescence](./Images/tree.png)

Dans cette arborescence, les branches avec « GN » sont des groupes nominaux.
Il y en a plusieurs : *Je*, *un nouveau véhicule hybride*, *Bluetooth* et *un nouveau véhicule hybride avec Bluetooth*.

## <a name="phrase-types"></a>Types d’expressions

| Étiquette | Description | Exemples |
|-------|-------------|---------|
|ADJP   | Locution adjectivale | « à la mode » |
|ADVP   | Locution adverbiale | « en général » |
|CONJP  | Locution conjonctive | « ainsi que » |
|FRAG   | Fragment, utilisé pour les entrées incomplètes ou fragmentaires | « Fortement recommandé » |
|INTJ   | Interjection | « Zut » |
|LST    | Marqueur de liste, comme les marques de ponctuation | "#4)" |
|NAC    | Non composante, utilisé pour indiquer l’étendue d’une expression non composante |  « et pour un bon prix » dans « vous aurez droit à ça, et pour un bon prix » |
|NP | Groupe nominal | « une délicieuse galette de pommes de terre » |
|NX | Utilisé dans certains groupes nominaux complexes pour désigner le sujet principal| |
|PP | Groupe prépositionnel| « dans la maison » |
|PRN    | Entre parenthèses| « (soit disant) » |
|PRT    | Particule| « outre » dans « passer outre » |
|QP | Déterminant quantitatif (des mesures ou volumes complexes) dans un groupe nominal| « autour de 75 € » |
|RRC    | Proposition relative restrictive.| « toujours pas résolus » dans « beaucoup de problèmes toujours pas résolus » |
|S  | Proposition ou phrase. | « Ceci est une phrase. »
|SBAR   | Proposition subordonnée, souvent introduite par une conjonction de subordination | « au moment où je suis parti » dans « J’ai regardé autour de moi au moment où je suis parti »|
|SBARQ  | Question directe introduite par un mot ou expression en -qu | « Quel était l’intérêt ? » |
|SINV   | Phrase déclarative inversée | « À aucun moment ne furent-ils conscients. » (Vous pouvez remarquer que le sujet « ils » a été placé après le verbe « furent ») |
|SQ | Question oui/non inversée, ou proposition principale d’une question en -qu | « Ont-ils acheté la voiture ? » |
|UCP    | Conjonction de coordination| « petit et avec des bogues » (vous pouvez remarquer qu’une locution adjectivale et prépositionnelle sont liées par « et »)|
|VP | Groupe verbal | « courrait dans les bois » |
|WHADJP | Locution adjectivale | « bleu marine » |
|WHADVP | Locution adverbiale| « quand » |
|WHNP   | Groupe nominal| « quelle pomme de terre », « quelle quantité de soupe »|
|WHPP   | Locution prépositionnelle| « compte tenu de l’heure »|
|X  | Inconnu, incertain| Premier « la » dans « la... la soupe » |


## <a name="specification"></a>Caractéristique

Ces arborescences utilisent les expressions en S dans [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42).
