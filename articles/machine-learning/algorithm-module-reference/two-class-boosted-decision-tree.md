---
title: 'Arbre de décision optimisé à deux classes : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Two-Class Boosted Decision Tree dans le service Azure Machine Learning pour créer un modèle machine learning qui est basé sur l’algorithme d’arbres de décision optimisé.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09ea530cac5bdbd62208f134177e5ceaccb545e2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027944"
---
# <a name="two-class-boosted-decision-tree-module"></a>Module de Two-Class Boosted Decision Tree

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle d’apprentissage qui est basé sur l’algorithme d’arbres de décision optimisé. 

Un arbre de décision optimisé est un méthode dans laquelle l’arborescence de la deuxième corrige les erreurs de la première arborescence, d’apprentissage d’ensemble de l’arborescence de la troisième corrige les erreurs des arborescences de première et deuxième et ainsi de suite.  Prédictions sont basées sur l’ensemble entier d’arbres ensemble qui effectue la prédiction.
  
En règle générale, lorsque correctement configuré, arbres de décision sont les méthodes les plus simples obtenir des performances optimales sur un large éventail de tâches d’apprentissage automatique. Toutefois, ils sont également des apprenants plus gourmandes en mémoire, et l’implémentation actuelle conserve tout en mémoire. Par conséquent, un modèle d’arbre de décision augmentés peut-être pas en mesure de traiter des jeux de données volumineux certains apprenants.

## <a name="how-to-configure"></a>Comment configurer

Ce module crée un modèle de classification formé. Étant donné que la classification est une méthode d’apprentissage supervisé, pour former le modèle, vous devez un *balisés dataset* qui inclut une colonne d’étiquette avec une valeur pour toutes les lignes.

Vous pouvez former ce type de modèle à l’aide [former le modèle](././train-model.md). 

1.  Dans Azure Machine Learning, ajouter le **Boosted Decision Tree** module à votre expérience.
  
2.  Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.
  
    + **L’unique paramètre**: Si vous savez comment vous souhaitez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs comme arguments.
  
  
3.  Pour **nombre maximal de feuilles par arbre**, indiquer le nombre maximal de nœuds terminaux (feuilles) qui peuvent être créés dans n’importe quel arbre.
  
     En augmentant cette valeur, vous potentiellement augmentez la taille de l’arborescence et obtenez une meilleure précision, au risque d’un surajustement et temps d’apprentissage.
  
4.  Pour **nombre minimal d’échantillons par nœud terminal**, indiquer le nombre de cas requis pour créer un nœud terminal (feuille) dans une arborescence.  
  
     En augmentant cette valeur, vous augmentez le seuil pour la création de nouvelles règles. Par exemple, la valeur par défaut de 1, même un seul cas peut entraîner une nouvelle règle à créer. Si vous augmentez la valeur à 5, les données d’apprentissage doivent contenir au moins cinq cas qui remplissent les mêmes conditions.
  
5.  Pour **taux d’apprentissage**, tapez un nombre compris entre 0 et 1 qui définit la taille d’étape lors de l’apprentissage.  
  
     Le taux d’apprentissage détermine rapidement ou lentement l’apprenant converge vers la solution optimale. Si la taille d’étape est trop volumineuse, vous pouvez dépassement la solution optimale. Si la taille d’étape est trop petite, formation prend plus de temps à converger sur la meilleure solution.
  
6.  Pour **nombre d’arbres construits**, indique le nombre total d’arbres de décision à créer dans l’ensemble. En créant plusieurs arbres de décision, vous pouvez obtenir une meilleure couverture, mais les temps d’apprentissage augmente.
  
     Cette valeur contrôle également le nombre d’arbres affichés lorsque vous examinez le modèle formé. Si vous souhaitez afficher ou imprimer une arborescence unique, définissez la valeur sur 1. Toutefois, lorsque vous procédez ainsi, qu’un seul arbre est généré (l’arborescence avec le jeu de paramètres initial) et aucune itération n’est effectuée.
  
7.  Pour **valeur initiale de nombre aléatoire**, vous pouvez éventuellement taper un entier non négatif à utiliser comme la valeur de départ aléatoire. En spécifiant une valeur initiale garantit la reproductibilité entre les exécutions qui ont les mêmes données et paramètres.  
  
     La valeur de départ aléatoire est définie par défaut sur 0, ce qui signifie que la valeur de départ initiale est obtenue à partir de l’horloge système.  Les exécutions consécutives à l’aide d’une valeur de départ aléatoire peuvent avoir des résultats différents.
  

9. Apprentissage du modèle.
  
    + Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, connectez un jeu de données avec balises et la [former le modèle](./train-model.md) module.  
  
   
## <a name="results"></a>Résultats

Après l’apprentissage du modèle est terminée, cliquez sur la sortie de [former le modèle](./train-model.md) pour afficher les résultats :

+ Pour afficher l’arborescence qui a été créé sur chaque itération, sélectionnez **visualiser**. 
+ Pour Explorer les divisions et afficher les règles pour chaque nœud, cliquez sur chacune d’elles.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 