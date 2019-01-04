---
title: Balisage morphosyntaxique – API Analyse linguistique
description: Découvrez comment le balisage morphosyntaxique dans l’API Analyse linguistique identifie la catégorie ou la nature grammaticale de chaque mot d’un texte.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 09/27/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 9af4f5223dfe269ace459c8a7de07fe6dd0c2427
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973959"
---
# <a name="part-of-speech-tagging"></a>Balisage morphosyntaxique

> [!IMPORTANT]
> La préversion de l’analyse linguistique a été désactivée le 9 août 2018. Nous vous recommandons d’utiliser les [modules d’analyse de texte Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) pour le traitement et l’analyse de texte.

## <a name="background-and-motivation"></a>Contexte et motivation

Une fois qu’un texte a été séparé en phrases et unités lexicales, la prochaine étape de l’analyse consiste à identifier la catégorie ou nature grammaticale de chaque mot.
Sont inclues des catégories comme *substantif* (représentant généralement des gens, des lieux, des objets, des idées, etc) et *verbe* (représentant généralement des actions, des changements de statut, etc). Pour certains mots, la nature grammaticale ne comporte aucune ambiguïté (par exemple, *bourbier* n’est qu’un nom) mais, dans de nombreux cas, elle peut être très ambigüe.
Une *table* peut être un endroit où manger (ou un tableau de chiffres en 2D), mais on peut aussi dire « tabler sur un ami ».

## <a name="list-of-part-of-speech-tags"></a>Liste de balises morphosyntaxiques

| Tag | Description | Exemple de mots |
|-----|-------------|---------------|
| $ | dollar | $ |
| \`\` | guillemet ouvrant | \` \`\` |
| « | guillemet fermant | ' '' |
| ( | parenthèse ouvrante | ( [ { |
| ) | parenthèse fermante | ) ] } |
| . | virgule | . |
| -- | tiret | -- |
| . | ponctuation de fin de phrase | . ! ? |
| : | deux points ou points de suspension | : ; ... |
| CC | conjonction, coordination | mais ou et donc or ni car|
| CD | nombre cardinal | neuf 20 1980 '96 |
| DT | déterminant |un, le, la, les, tout, tous, aucun, les deux|
| EX | Le Il existentiel | il |
| FW | Mot étranger | enfant terrible hoi polloi je ne sais quoi |
| IN | préposition ou conjonction de subordination| que, quand, comme, quoique, si, lorsque |
| JJ | adjectif ou nombre ordinal | neuvième multimodale assez exécrable |
| JJR | adjectif comparatif | meilleur, pire, moindre, mieux |
| JJS | adjectif superlatif | le meilleur, le pire, le moindre, le mieux |
| LS | marqueur d’élément de liste | (a) (b) 1 2 A B A. B. |
| MD | auxiliaire modal | peut doit semble paraît |
| NN | nom, commun, singulier ou pluriel | patate, argent, chaussures |
| NNP | nom propre singulier | Kennedy, Roosevelt, Chicago, Weehauken |
| NNPS | nom propre pluriel | Les Ardennes |
| NNS | nom commun pluriel | travaux, maux, journaux |
| PDT | prédéterminant | tout, tous, les deux, la moitié, beaucoup, quelques |
| POS | marqueur génitif | ' 's |
| PRP | Pronom personnel | elle, il, je, nous, ils, vous |
| PRP$ | Pronom possessif | son mon notre leur votre |
| RB | adverbe | uniquement clinique |
| RBR | Adjectif comparatif | plus loin, plus glauque, plus grand, plus grave, plus important, plus difficile, plus sain, plus lourd, supérieur, plus long, etc |
| RBS | adverbe superlatif | meilleur, majeur, le plus tôt, le plus loin, le premier, extrêmement, le plus haut, le plus grand, le plus proche, le pire |
| RP | particule | bas, outre |
| SYM | symbole | % & |
| À | « à » comme marque de préposition ou infinitive | to |
| UH | interjection | hein, salut, allo, zut |
| VB | base verbale | donner, assigner, voler |
| VBD | verbe, passé | donnait, assigna, a volé |
| VBG | verbe, participe présent ou gérondif | en donnant, assignant, en volant |
| VBN | verbe, participe passé | donné, assigné, volé |
| VBP | verbe, présent, autre que 3e personne du singulier | donne, assignons, volaient |
| VBZ | verbe, présent, 3e personne du singulier | donne, assigne, vole |
| WDT | déterminant | ce, cet, celui |
| WP | pronom en -qu | qui, que, qu’, quoi |
| WP$ | Pronom possessif | à qui, duquel, de qui, dont |
| WRB | adverbes | comment, quand, où |

## <a name="specification"></a>Caractéristique

Pour la segmentation du texte en unités lexicales, nous comptons sur la spécification de [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42).
