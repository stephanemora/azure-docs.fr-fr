---
title: 'Forêt d’arbres de décision multiclasse : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Forêt d’arbres de décision multiclasse dans Azure Machine Learning pour créer un modèle Machine Learning basé sur l’algorithme de *forêt d’arbres de décision*.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 85459fe586ccbb5adb1e86b153ec63252c0cce2d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466022"
---
# <a name="multiclass-decision-forest-module"></a>Module Forêt d’arbres de décision multiclasse

Cet article décrit un module dans le concepteur Azure Machine Learning (préversion).

Ce module vous permet de créer un modèle Machine Learning basé sur l’algorithme de *forêt d’arbres de décision*. Une forêt d’arbres de décision est un modèle d’ensemble qui génère rapidement une série d’arbres de décision, tout en apprenant grâce à des données avec balises.

## <a name="more-about-decision-forests"></a>En savoir plus sur la forêt d’arbres de décision

L’algorithme de forêt d’arbres de décision est une méthode d’apprentissage d’ensemble pour la classification. Pour fonctionner, l’algorithme crée plusieurs arbres de décision, puis il *vote* pour sélectionner la classe de sortie la plus populaire. Le vote est une forme d’agrégation dans laquelle chaque arbre d’une forêt d’arbres de décision génère un histogramme de fréquence non normalisé pour les étiquettes. Le processus d’agrégation additionne ces histogrammes et normalise le résultat pour obtenir les « probabilités » pour chaque étiquette. Les arbres présentant un niveau de confiance élevé en matière de prédiction influent davantage sur la décision finale de l’ensemble.

En général, les arbres de décision sont des modèles non paramétriques, ce qui signifie qu’ils prennent en charge les données avec des distributions variées. Dans chaque arbre, une séquence de tests simples est exécutée pour chaque classe, augmentant les niveaux d’une arborescente jusqu’à atteindre un nœud terminal (une décision).

Les arbres de décision présentent plusieurs avantages :

+ Ils peuvent représenter les limites de décisions non linéaires.
+ Ils sont efficaces en matière de calcul et d’utilisation de la mémoire lors de la formation et de la prédiction.
+ Ils sélectionnent et classifient les fonctionnalités intégrées.
+ Ils sont résilients en cas de fonctionnalités bruyantes.

Le classifieur de forêt d’arbres de décision dans Azure Machine Learning est un ensemble d’arbres de décision. En règle générale, les modèles d’ensemble fournissent une couverture et une précision plus élevée par rapport aux arbres de décision individuels. Pour plus d’informations, consultez [Arbres de décision](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Configuration du module Forêt d’arbres de décision multiclasse

1. Ajoutez le module **Forêt d’arbres de décision multiclasse** à votre pipeline dans le concepteur. Vous pouvez trouver ce module sous **Machine Learning**, **Initialiser le modèle** et **Classification**.

2. Double-cliquez sur le module pour ouvrir le volet **Propriétés**.

3. Pour **Méthode de rééchantillonnage**, choisissez la méthode utilisée pour créer les arbres individuels.  Vous pouvez choisir l’ensachage ou la réplication.

    + **Ensachage** : le bagging, également appelé *agrégation boostrap*, est une méthode selon laquelle chaque arbre est développé sur un nouvel échantillon, créé en échantillonnant le jeu de données d’origine de manière aléatoire à l’aide d’un ensemble de remplacement jusqu’à obtenir un jeu de données de taille identique à l’original. Les sorties des modèles sont combinées grâce à un *vote* qui est une forme d’agrégation. Pour plus d’informations, consultez la page Wikipedia relative au « bootstrap aggregating ».

    + **Réplication** : avec la réplication, l’apprentissage de chaque arbre est effectué à partir des mêmes données d’entrée. Le prédicat fractionné utilisé pour chaque nœud d’arbre est toujours déterminé de manière aléatoire, ce qui crée des arbres variés.

   

4. Spécifiez la méthode d’apprentissage que vous souhaitez appliquer au modèle en configurant l’option **Créer un mode d’apprentissage**.

    + **Paramètre unique** : sélectionnez cette option si vous savez comment vous voulez configurer le modèle et vous fournissez un ensemble spécifique de valeurs en tant qu’arguments.


5. **Nombre d’arbres de décision** : saisissez le nombre maximal d’arbres de décision qui peuvent être créés dans l’ensemble. En créant plusieurs arbres de décision, vous pouvez obtenir une meilleure couverture, mais cette opération peut augmenter la durée de la formation.

    Cette valeur contrôle également le nombre d’arbres affichés dans les résultats lorsque vous examinez le modèle entraîné. Si vous souhaitez afficher ou imprimer un arbre unique, vous pouvez définir la valeur sur 1. Toutefois, cela signifie qu’un seul arbre peut être généré (celui avec le jeu de paramètres initial) et aucune autre itération n’est effectuée.

6. **Profondeur maximale des arbres de décision** : saisissez un nombre destiné à limiter la profondeur maximale de n’importe quel arbre de décision. Augmenter la profondeur de l’arbre peut améliorer la précision, mais cela peut entraîner un surajustement et rallonger la durée de la formation.

7. **Nombre de fractionnements aléatoires par nœud** : saisissez le nombre de fractionnements à utiliser lors de la génération de chaque nœud de l’arbre. Un *fractionnement* signifie que les fonctionnalités à chaque niveau de l’arbre (nœud) sont séparées au hasard.

8. **Nombre minimal d’échantillons par nœud terminal** : indiquez le nombre minimal de cas requis pour créer un nœud terminal (feuille) dans un arbre. En augmentant cette valeur, vous augmentez le seuil pour la création de règles.

    Par exemple, avec la valeur par défaut, 1, un seul cas suffit à entraîner la création d’une règle. Si vous définissez la valeur de ce champ sur 5, les données de la formation doivent contenir au moins cinq cas remplissant les mêmes conditions.



10. Connectez un jeu de données étiqueté et l’un des modules de formation :

    + Si vous définissez **Créer un mode d’apprentissage** sur **Paramètre unique**, utilisez le module [Entraîner le du modèle](./train-model.md).

11. Exécuter le pipeline.

## <a name="results"></a>Résultats

Une fois l’apprentissage terminé :

+ Pour afficher l’arbre créé pour chaque itération, cliquez sur la sortie du module [Effectuer l’apprentissage du modèle](./train-model.md) et sélectionnez **Visualiser** .
+ Pour descendre dans la hiérarchie des fractionnements et afficher les règles de chaque nœud, cliquez sur chaque arbre.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 