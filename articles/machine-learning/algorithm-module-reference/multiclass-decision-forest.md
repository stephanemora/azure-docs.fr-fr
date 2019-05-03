---
title: 'Multiclass Decision Forest : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Multiclass Decision Forest dans le service Azure Machine Learning pour créer un modèle machine learning basé sur le *forêt de décision* algorithme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d5ff1809886198f7f1e4f4b9c36f877ce61d512e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029429"
---
# <a name="multiclass-decision-forest-module"></a>Module de forêt de décision multiclasse

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle machine learning basé sur le *forêt de décision* algorithme. Une forêt de décision est un ensemble modèle rapidement une série d’arbres de décision, lors de l’apprentissage à partir des données avec balises.

## <a name="more-about-decision-forests"></a>Plus d’informations sur les forêts de décision

L’algorithme de forêt de décision est un méthode pour la classification d’apprentissage d’ensemble. L’algorithme fonctionne en créant plusieurs arbres de décision, puis *vote* sur la classe de sortie la plus populaire. Le vote n’est un formulaire d’agrégation, dans chaque arbre dans une décision de classement forêt génère un histogramme de fréquence non normalisée des étiquettes. Le processus d’agrégation additionne ces histogrammes et normalise le résultat pour obtenir les « probabilités » pour chaque étiquette. Les arborescences qui ont confiance dans les prévisions élevée ont un poids supérieur à la décision finale de l’ensemble.

En général, les arbres de décision sont des modèles non paramétriques, ce qui signifie qu’ils prennent en charge les données avec diverses distributions. Dans chaque arborescence, une série de tests simples est exécutée pour chaque classe, augmentant les niveaux d’une structure arborescente jusqu'à atteindre un nœud terminal (décision).

Arbres de décision présentent plusieurs avantages :

+ Ils peuvent représenter des limites de décision non linéaires.
+ Ils sont efficaces dans le calcul et l’utilisation de la mémoire au cours de formation et de prédiction.
+ Ils effectuent la sélection des fonctionnalités intégrées et classification.
+ Ils sont résilients en présence de fonctionnalités bruyantes.

Le classifieur de forêt de décision dans Azure Machine Learning se compose d’un ensemble d’arbres de décision. En règle générale, les modèles d’ensemble fournissent une meilleure couverture et précision que les arbres de décision uniques. Pour plus d’informations, consultez [arbres de décision](http://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Comment configurer la forêt de décision Multiclasse



1. Ajouter le **Multiclass Decision Forest** module à votre expérience dans l’interface. Vous trouverez ce module sous **Machine Learning**, **initialiser le modèle**, et **Classification**.

2. Double-cliquez sur le module pour ouvrir le **propriétés** volet.

3. Pour **méthode de rééchantillonnage**, choisissez la méthode utilisée pour créer les arbres individuels.  Vous pouvez choisir entre le bagging et la réplication.

    + **Bagging**: ENSACHAGE est également appelé *agrégation bootstrap*. Dans cette méthode, chaque arborescence est développé sur un nouvel échantillon, créé par le jeu de données d’origine avec le remplacement de l’échantillonnage au hasard jusqu'à ce que vous avez un jeu de données de la taille d’origine. Les sorties des modèles sont combinées par *vote*, qui est un formulaire d’agrégation. Pour plus d’informations, consultez la page Wikipedia pour agréger les données d’amorçage.

    + **Répliquer**: Dans la réplication, chaque arborescence est formé sur exactement les mêmes données d’entrée. La détermination du fractionner le prédicat est utilisé pour chaque nœud d’arbre reste aléatoire, la création d’arborescences différentes.

   

4. Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.

    + **L’unique paramètre**: Sélectionnez cette option si vous connaissez la façon dont vous souhaitez configurer le modèle et fournir un ensemble de valeurs comme arguments.


5. **Nombre d’arbres de décision**: Tapez le nombre maximal d’arbres de décision qui peuvent être créés dans l’ensemble. En créant plusieurs arbres de décision, vous pouvez obtenir une meilleure couverture, mais peut augmenter la durée d’apprentissage.

    Cette valeur contrôle également le nombre d’arbres affichée dans les résultats, lorsque vous examinez le modèle formé. Pour afficher ou imprimer une arborescence unique, vous pouvez définir la valeur sur 1 ; Toutefois, cela signifie que qu’une seule arborescence peut être générés (l’arborescence avec le jeu de paramètres initial), et aucune itération n’est effectuée.

6. **Profondeur maximale des arbres de décision**: Tapez un nombre pour limiter la profondeur maximale de n’importe quel arbre de décision. Augmentation de la profondeur de l’arborescence peut accroître la précision, avec comme corolaire des temps de formation surajustement et d’augmentation.

7. **Nombre de fractionnements aléatoires par nœud**: Tapez le nombre de divisions à utiliser lors de la génération de chaque nœud de l’arborescence. Un *fractionner* signifie que les fonctionnalités de chaque niveau de l’arbre (nœud) est réparties au hasard.

8. **Nombre minimal d’échantillons par nœud terminal**: Indiquer le nombre minimal de cas qui sont nécessaires pour créer un nœud terminal (feuille) dans une arborescence. En augmentant cette valeur, vous augmentez le seuil pour la création de nouvelles règles.

    Par exemple, la valeur par défaut de 1, même un seul cas peut entraîner une nouvelle règle à créer. Si vous augmentez la valeur à 5, les données d’apprentissage doivent contenir au moins cinq cas qui remplissent les mêmes conditions.



10. Connectez un jeu de données étiquetée et un des modules de formation :

    + Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, utilisez le [former le modèle](./train-model.md) module.

11. Exécutez l’expérience.

## <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher l’arborescence qui a été créé sur chaque itération, cliquez sur la sortie de la [former le modèle](./train-model.md) module, puis sélectionnez **visualiser**.
+ Pour afficher les règles pour chaque nœud, cliquez sur chaque arbre pour Explorer les divisions.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 