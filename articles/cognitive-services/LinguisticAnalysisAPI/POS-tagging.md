---
title: Balisage morphosyntaxique dans l’API Analyse linguistique | Microsoft Docs
description: Découvrez comment le balisage morphosyntaxique dans Microsoft Cognitive Services identifie la catégorie ou nature grammaticale de chaque mot d’un texte.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 09/27/2016
ms.author: lesun
ms.openlocfilehash: 35d2042db5f8d64c7dbd1df6bfdebcba6d6aab28
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368120"
---
# <a name="part-of-speech-tagging"></a>Balisage morphosyntaxique

## <a name="background-and-motivation"></a>Contexte et motivation

Une fois qu’un texte a été séparé en phrases et unités lexicales, la prochaine étape de l’analyse consiste à identifier la catégorie ou nature grammaticale de chaque mot.
Sont inclues des catégories comme *substantif* (représentant généralement des gens, des lieux, des objets, des idées, etc) et *verbe* (représentant généralement des actions, des changements de statut, etc). Pour certains mots, la nature grammaticale ne comporte aucune ambiguïté (par exemple, *bourbier* n’est qu’un nom), mais elle peut être, dans de nombreux cas, très ambigüe.
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
| NN | nom commun, singulier ou pluriel | patate, argent, chaussures |
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

Pour la segmentation du texte en unités lexicales, nous comptons sur la spécification de [Penn Treebank](https://www.cis.upenn.edu/~treebank/).
