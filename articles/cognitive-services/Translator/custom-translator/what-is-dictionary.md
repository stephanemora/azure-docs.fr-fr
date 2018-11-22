---
title: Qu’est un dictionnaire ? – Custom Translator
titleSuffix: Azure Cognitive Services
description: Un dictionnaire est un document aligné spécifiant une liste d’expressions ou de phrases (avec leur traduction) que Microsoft Translator devra traduire toujours de la même façon. Les dictionnaires sont parfois appelés glossaires ou bases terminologiques.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 3a92e41ad9611981777012cd231845587a6f947b
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626779"
---
# <a name="what-is-a-dictionary"></a>Qu’est un dictionnaire ?

Un dictionnaire est une paire alignée de documents qui spécifie une liste d’expressions ou de phrases avec les traductions correspondantes. Utilisez un dictionnaire dans votre apprentissage si vous souhaitez que Microsoft Translator traduise toujours toutes les instances de l’expression ou de la phrase source selon la traduction que vous avez indiquée dans le dictionnaire. Les dictionnaires sont parfois appelés glossaires ou bases terminologiques. On peut considérer le dictionnaire comme un « copier-remplacer » en force pour tous les termes listés.

Les dictionnaires ne fonctionnent que pour les projets des paires de langues qui s’appuient sur un système de traduction automatique neuronale (NMT, pour « neural machine translation ») Microsoft entièrement pris en charge. Pour connaître la liste complète des langues, voir http://www.aka.ms/translatorlanguages.

## <a name="phrase-dictionary"></a>Dictionnaire d’expressions 
Si un dictionnaire d’expressions est ajouté dans le cadre de l’apprentissage du modèle, tous les mots et expressions listés sont traduits de la manière spécifiée. La traduction du reste de la phrase reste inchangée. Vous pouvez utiliser un dictionnaire d’expressions pour spécifier des expressions à ne pas traduire : indiquez pour cela la même expression non traduite dans le fichier source et dans le fichier cible du dictionnaire.

## <a name="sentence-dictionary"></a>Dictionnaire de phrases
Le dictionnaire de phrases permet de spécifier la traduction cible exacte d’une phrase source. La totalité (et pas seulement une partie) de la phrase soumise doit être identique à l’entrée de dictionnaire source  pour qu’il y ait correspondance.  En cas de correspondance, l’entrée cible du dictionnaire de phrases est retournée.

## <a name="dictionary-only-trainings"></a>Apprentissage sur dictionnaire uniquement
Il est possible d’entraîner un modèle en n’utilisant que des données de dictionnaire. Pour cela, sélectionnez seulement le ou les documents de type dictionnaire que vous souhaitez inclure, puis appuyez sur Créer un modèle. Comme il s’agit d’un apprentissage sur dictionnaire uniquement, il n’y a pas de nombre minimal de phrases d’apprentissage nécessaires. L’apprentissage est généralement beaucoup plus rapide pour ce type de modèle que dans le cas standard.  Les modèles obtenus utilisent les modèles de référence de Microsoft pour la traduction, en y joignant les dictionnaires ajoutés.  Aucun rapport de test n’est généré.

>[!Note]
>Dans la mesure où Custom Translator n’aligne pas les fichiers de dictionnaire en phrases, il est important qu’il y ait le même nombre d’expressions/phrases sources et cibles dans les documents de dictionnaire et qu’elles soient précisément alignées.

## <a name="recommendations"></a>Recommandations

- Les dictionnaires ne peuvent pas remplacer un modèle entraîné avec les données d’apprentissage.  En substance, ils recherchent et remplacent des mots ou des phrases.  Il est généralement préférable de laisser le système apprendre à partir de ressources d’apprentissage composées de phrases complètes que d’utiliser un dictionnaire. 
- Le dictionnaire d’expressions doit être utilisé avec parcimonie. Lorsqu’une expression est remplacée au sein d’une phrase, le contexte de cette phrase est perdu ou limité pour traduire le reste de la phrase. D’un côté, l’expression ou le mot dans la phrase est traduit en fonction du dictionnaire d’expressions, mais, de l’autre, la qualité de traduction globale de la phrase en s’en trouve souvent diminuée.
- Le dictionnaire d’expressions fonctionne bien pour les noms composés comme les noms de produits (« Microsoft SQL Server »), les noms propres (« ville de Hambourg ») et les fonctionnalités de produit (« tableau croisé dynamique »). Il n’est pas aussi performant pour les verbes et les adjectifs, car ceux-ci sont généralement fléchis dans la langue source ou la langue cible. Évitez les entrées de dictionnaire d’expressions pour tout ce qui n’est pas un nom composé. 
- Si vous utilisez un dictionnaire, la mise en majuscules et les signes de ponctuation de vos traductions reflètent la mise en majuscules et la ponctuation indiquées dans votre fichier cible. La mise en majuscules et les signes de ponctuation sont ignorés au moment d’identifier des correspondances entre la phrase d’entrée et les phrases sources du fichier de dictionnaire. Supposons par exemple que nous ayons entraîné un système anglais > français qui utilise un dictionnaire contenant « City of Hamburg » dans le fichier source et « ville de Hambourg » dans le fichier cible. Si je demande la traduction d’une phrase comportant l’expression « city of Hamburg », celle-ci est mise en correspondance avec l’entrée « City of Hamburg » du fichier de dictionnaire et donne « ville de Hambourg » dans la traduction finale.
- Si un mot apparaît plusieurs fois dans un fichier de dictionnaire, le système utilise toujours la dernière entrée indiquée. Un dictionnaire ne doit pas contenir plusieurs traductions pour un même mot.

## <a name="next-steps"></a>Étapes suivantes

- Lisez les [instructions sur les formats de documents](document-formats-naming-convention.md).