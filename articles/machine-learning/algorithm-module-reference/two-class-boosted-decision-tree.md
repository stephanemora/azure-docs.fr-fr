---
title: 'Arbre de décision optimisé à deux classes : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Arbre de décision optimisé à deux classes dans Azure Machine Learning service pour créer un modèle Machine Learning basé sur l’algorithme d’arbres de décision optimisés.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 5795dc994872ac5da37a1226524dddd35aa35126
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692629"
---
# <a name="two-class-boosted-decision-tree-module"></a>Arbre de décision optimisé à deux classes

Cet article décrit un module de l’interface visuelle (préversion) d’Azure Machine Learning service.

Ce module vous permet de créer un modèle Machine Learning reposant sur l’algorithme d’arbres de décision optimisés. 

Un arbre de décision optimisé est une méthode d’apprentissage d’ensemble dans laquelle le second arbre corrige les erreurs du premier arbre, le troisième arbre corrige les erreurs du premier et du deuxième arbres, et ainsi de suite.  Les prédictions sont effectuées par l’ensemble d’arbres complet.
  
En règle générale, lorsque les arbres de décision sont correctement configurés, ils représentent le moyen le plus simple d’obtenir des performances optimales sur un large éventail de tâches Machine Learning. Toutefois, ils constituent également l’un des apprentissages qui utilisent le plus de mémoire, et l’implémentation actuelle conserve toutes les données en mémoire. Par conséquent, un modèle d’arbre de décision optimisé n’est pas toujours en mesure de traiter des jeux de données volumineux qui peuvent être gérés par certains apprentissages linéaires.

## <a name="how-to-configure"></a>Comment configurer

Ce module crée un modèle de classification non entraîné. Étant donné que la classification est une méthode d’apprentissage supervisée, pour effectuer l’apprentissage du modèle, vous devez disposer d’un *jeu de données balisé* qui inclut une colonne d’étiquette avec une valeur pour toutes les lignes.

Vous pouvez effectuer l’apprentissage de ce type de modèle à l’aide de l’article [Entraîner le modèle](././train-model.md). 

1.  Dans Azure Machine Learning, ajoutez le module **Arbre de décision optimisé** à votre pipeline.
  
2.  Spécifiez le mode d’apprentissage du modèle en définissant l’option **Créer un mode d’apprentissage**.
  
    + **Paramètre unique** : si vous savez comment vous voulez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs en tant qu’arguments.
  
  
3.  Dans le champ **Maximum number of leaves per tree** (Nombre maximal de nœuds terminaux par arbre), indiquez le nombre maximal de nœuds terminaux qui peuvent être créés dans un arbre quelconque.
  
     En augmentant cette valeur, vous augmentez potentiellement la taille de l’arbre et vous bénéficiez d’un surcroît de précision, au risque d’un surajustement et d’un temps d’apprentissage plus long.
  
4.  Dans le champ **Minimum number of samples per leaf node** (Nombre minimal d’échantillons par nœud terminal), indiquez le nombre de cas requis pour la création d’un nœud terminal dans un arbre.  
  
     En augmentant cette valeur, vous augmentez le seuil pour la création de règles. Par exemple, avec la valeur par défaut, 1, un seul cas suffit à entraîner la création d’une règle. Si vous définissez la valeur de ce champ sur 5, les données d’apprentissage doivent contenir au moins cinq cas remplissant les mêmes conditions.
  
5.  Dans le champ **Taux d’apprentissage**, saisissez un nombre compris entre 0 et 1 qui définit la taille de pas lors de l’apprentissage.  
  
     Le taux d’apprentissage détermine la vitesse à laquelle l’apprentissage converge vers la solution optimale. Si la taille de pas est trop élevée, vous risquez de passer à côté de la solution optimale. Si la taille de pas est trop faible, l’apprentissage nécessite plus de temps pour converger vers la meilleure solution.
  
6.  Dans le champ **Number of trees constructed** (Nombre d’arbres construits), indiquez le nombre total d’arbres de décision à créer dans l’ensemble. En créant plusieurs arbres de décision, vous pouvez obtenir une meilleure couverture, mais cette opération augmente la durée d’apprentissage.
  
     Cette valeur contrôle également le nombre d’arbres affichés lorsque vous examinez le modèle entraîné. Si vous souhaitez visualiser ou imprimer un arbre unique, définissez la valeur sur 1. Toutefois, lorsque vous procédez ainsi, un seul arbre est généré (l’arbre avec le jeu de paramètres initial), et aucune autre itération n’est effectuée.
  
7.  Dans le champ **Random number seed** (Valeur de départ aléatoire), saisissez si vous le souhaitez un entier non négatif à utiliser comme valeur initiale aléatoire. La spécification d’une valeur de départ garantit la reproductibilité entre les exécutions qui présentent les mêmes données et paramètres.  
  
     La valeur de départ aléatoire est définie par défaut sur 0, ce qui signifie qu’elle est obtenue à partir de l’horloge système.  Les exécutions successives qui utilisent une valeur de départ aléatoire peuvent avoir des résultats différents.
  

9. Effectuez l’apprentissage du modèle.
  
    + Si vous définissez **Create trainer mode** (Créer un mode d’apprentissage) sur **Single Parameter** (Paramètre unique), connectez un jeu de données balisé au module [Entraîner le modèle](./train-model.md).  
  
   
## <a name="results"></a>Résultats

Une fois l’apprentissage du modèle effectué, cliquez avec le bouton droit sur la sortie du module [Entraîner le modèle](./train-model.md) pour visualiser les résultats :

+ Pour afficher l’arbre qui a été créé sur chaque itération, sélectionnez **Visualiser**. 
+ Pour descendre dans la hiérarchie des fractionnements et afficher les règles de chaque nœud, cliquez sur chaque arbre.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 