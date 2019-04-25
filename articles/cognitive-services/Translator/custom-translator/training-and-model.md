---
title: Que sont un entraînement et un modèle ? - Custom Translator
titleSuffix: Azure Cognitive Services
description: 'Un modèle est le système qui fournit une traduction pour une paire de langues spécifique. Le résultat d’un entraînement réussi est un modèle. Lors de l’entraînement d’un modèle, trois jeux de données qui s’excluent mutuellement sont nécessaires : le jeu de données d’entraînement, le jeu de données de paramétrage et le jeu de données de test.'
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-rada
ms.openlocfilehash: adc78b176afb0a43a7e1e3a7bb882282a914761d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509916"
---
# <a name="what-are-trainings-and-models"></a>Que sont les entraînements et les modèles ?

Un modèle est le système qui fournit une traduction pour une paire de langues spécifique.
Le résultat d’un entraînement réussi est un modèle. Lors de l’entraînement d’un modèle, trois jeux de données qui s’excluent mutuellement sont nécessaires : le jeu de données d’entraînement, le jeu de données de paramétrage et le jeu de données de test. Des données de dictionnaire peuvent également être fournies.

Si seules les données d’entraînement sont fournies lors de la mise en file d’attente d’un entraînement, Custom Translator assemble automatiquement les jeux de données de paramétrage et de test. Il exclut 5 000 phrases de vos données d’entraînement et en utilise 2 500 pour assembler les jeux de paramétrage et de test, respectivement.

## <a name="training-dataset-for-custom-translator"></a>Jeu de données d’entraînement pour Custom Translator

Les documents inclus dans le jeu d’entraînement sont utilisés par Custom Translator comme base pour la création de votre modèle. Pendant l’exécution de l’entraînement, les phrases qui sont présentes dans ces documents sont alignées (ou appairées). Vous pouvez prendre la liberté de composer votre jeu de documents d’entraînement. Vous pouvez inclure des documents que vous considérez d’une pertinence indirecte dans un modèle. Là encore, vous devez les exclure d’un autre jeu pour voir l’impact sur le [score BLEU (Bilingual Evaluation Understudy)](what-is-bleu-score.md). Tant que les jeux de paramétrage et de test restent constants, n’hésitez pas à faire des essais avec la composition du jeu d’entraînement. Cette approche est un moyen efficace de modifier la qualité de votre système de traduction.

Vous pouvez exécuter plusieurs entraînements dans un projet et comparer les [scores BLEU](what-is-bleu-score.md) entre toutes les exécutions d’entraînements. Quand vous exécutez plusieurs entraînements pour une comparaison, vérifiez que les mêmes données de paramétrage/test sont spécifiées chaque fois. Veillez également à examiner les résultats manuellement sous l’onglet [Test](how-to-view-system-test-results.md).

## <a name="tuning-dataset-for-custom-translator"></a>Jeu de données de paramétrage pour Custom Translator

Les documents parallèles inclus dans ce jeu sont utilisés par Custom Translator afin de régler le système de traduction pour des résultats optimaux.

Le jeu de paramétrage est utilisé au cours de l’entraînement pour ajuster tous les paramètres et pondérations du système de traduction sur les valeurs optimales. Choisissez votre jeu de paramétrage avec soin : il doit représenter le contenu des documents que vous souhaitez traduire dans le futur. Le jeu de paramétrage a des répercussions importantes sur la qualité des traductions produites. Le paramétrage permet au système de traduction de générer des traductions qui sont le plus proche des exemples que vous fournissez dans le jeu de données de paramétrage. Vous n’avez pas besoin de plus de 2 500 phrases comme jeu de paramétrage. Pour une qualité optimale de traduction, il est recommandé de sélectionner le jeu de paramétrage manuellement en choisissant la sélection la plus représentative des phrases.

Quand vous créez votre jeu de paramétrage, choisissez des phrases d’une longueur significative et représentative des futures phrases que vous prévoyez de traduire. Vous devez également choisir des phrases avec des mots et expressions que vous allez traduire dans la distribution approximative attendue dans vos futures traductions. En pratique, une longueur de phrase de 8 à 18 mots produit les meilleurs résultats, car ces phrases contiennent suffisamment de contexte pour illustrer l’inflexion et fournir une longueur d’expression qui est significative, sans être trop complexe.

Une bonne description du type de phrases à utiliser dans le jeu de paramétrage est la prose : des phrases concrètes fluides. Pas de cellules de tableau, pas de poèmes, pas de listes de choses, pas seulement des signes de ponctuation ni des nombres dans une phrase : un langage normal.

Si vous sélectionnez manuellement votre jeu de données de paramétrage, il ne doit comporter aucune des phrases contenues dans vos données d’entraînement et de test. Comme le jeu de paramétrage a un impact significatif sur la qualité des traductions, choisissez les phrases avec soin.

Si vous ne savez pas quoi choisir pour votre jeu de paramétrage, sélectionnez le jeu d’entraînement et laissez Custom Translator sélectionner votre jeu de paramétrage pour vous. Quand vous laissez Custom Translator choisir le jeu de paramétrage automatiquement, il utilise un sous-ensemble aléatoire de phrases de vos documents d’entraînement bilingues et exclut ces phrases du support d’entraînement lui-même.

## <a name="testing-dataset-for-custom-translator"></a>Jeu de données de test pour Custom Translator

Les documents parallèles inclus dans le jeu de test sont utilisés pour calculer le score BLEU (Bilingual Evaluation Understudy). Ce score indique la qualité de votre système de traduction. Ce score vous signale en réalité dans quelle mesure les traductions effectuées par le système de traduction à l’issue de cet entraînement correspondent aux phrases de référence du jeu de données de test.

Le score BLEU est une mesure de la différence entre la traduction automatique et la traduction de référence. Sa valeur est comprise entre 0 et 100. Un score de 0 indique que pas un seul mot de la référence n’apparaît dans la traduction. Un score de 100 indique que la traduction automatique correspond exactement à la référence : le même mot figure à la même position exacte. Le score que vous recevez est la moyenne du score BLEU pour toutes les phrases du jeu de test.

Le jeu de test doit inclure des documents parallèles où les phrases de la langue cible sont les traductions les plus souhaitables des phrases de la langue source correspondante dans la paire. Vous pouvez utiliser les mêmes critères que ceux utilisés pour composer le jeu de paramétrage. Toutefois, le jeu de test n’a aucune influence sur la qualité du système de traduction. Il est utilisé exclusivement pour générer le score BLEU pour vous et pour rien d’autre.

Vous n’avez pas besoin de plus de 2 500 phrases comme jeu de test. Quand vous laissez le système choisir le jeu de test automatiquement, il utilise un sous-ensemble aléatoire de phrases de vos documents d’entraînement bilingues et exclut ces phrases du support d’entraînement lui-même.

Vous pouvez afficher les traductions personnalisées du jeu de test et les comparer aux traductions fournies dans votre jeu de test, en accédant à l’onglet de test dans un modèle.
