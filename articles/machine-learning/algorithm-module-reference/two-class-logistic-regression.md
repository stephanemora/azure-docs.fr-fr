---
title: 'Régression logistique à deux classes : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Two-Class Logistic Regression dans le service Azure Machine Learning pour créer un modèle de régression logistique qui peut être utilisé pour prédire les résultats de deux (et uniquement deux).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aacaf6c64ef77d0e694f97e3675060eca33794ed
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029249"
---
# <a name="two-class-logistic-regression-module"></a>Module de régression logistique à deux classes

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle de régression logistique qui peut être utilisé pour prédire les résultats de deux (et uniquement deux). 

La régression logistique est une technique statistique connue utilisée pour modéliser les nombreux types de problèmes. Cet algorithme est un *apprentissage supervisé* méthode ;  Par conséquent, vous devez fournir un jeu de données qui contient déjà les résultats pour former le modèle.  

### <a name="about-logistic-regression"></a>À propos de la régression logistique  

La régression logistique est une méthode bien connue dans les statistiques qui sont utilisés pour prédire la probabilité d’un résultat et est particulièrement populaire pour les tâches de classification. L’algorithme prédit la probabilité d’occurrence d’un événement en ajustant les données à une fonction logistique.
  
Dans ce module, l’algorithme de classification est optimisé pour les variables dichotomiques ou binaire. Si vous avez besoin de classifier plusieurs résultats, utilisez le [Multiclass Logistic Regression](./multiclass-logistic-regression.md) module.

##  <a name="how-to-configure"></a>Comment configurer  

Pour former ce modèle, vous devez fournir un jeu de données qui contient une colonne d’étiquette ou une classe. Étant donné que ce module est destiné aux problèmes de deux classes, la colonne étiquette ou une classe doit contenir exactement deux valeurs. 

Par exemple, la colonne d’étiquette peut être [voté] avec les valeurs possibles de « Oui » ou « Non ». Ou bien, il peut être [risque de crédit], avec les valeurs possibles de « Élevé » ou « Faible ». 
  
1.  Ajouter le **Two-Class Logistic Regression** module à votre expérience.  
  
2.  Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.  
  
    -   **L’unique paramètre**: Si vous savez comment vous souhaitez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs comme arguments.  
  
3.  Pour **tolérance d’optimisation**, spécifiez une valeur de seuil à utiliser lors de l’optimisation du modèle. Si l’amélioration entre les itérations tombe au-dessous du seuil spécifié, l’algorithme est considérée comme ont convergé sur une solution, et cesse de formation.  
  
4.  Pour **poids de régularisation L1** et **poids de régularisation L2**, tapez une valeur à utiliser pour les paramètres de régularisation L1 et L2. Une valeur non nulle est recommandée pour les deux.  
  
     *La régularisation* est une méthode pour empêcher le surajustement en pénaliser quant modèles avec des valeurs de coefficient extrêmes. La régularisation fonctionne en ajoutant la pénalité associée aux valeurs de coefficient à l’erreur de l’hypothèse. Par conséquent, un modèle précis avec des valeurs de coefficient extrêmes est pénalisé plus, mais un modèle moins précis avec des valeurs plus conservatrices est moins pénalisé.  
  
     Régularisations L1 et L2 ont différents effets et utilise.  
  
    -   L1 peut être appliqué aux modèles épars, ce qui est utile lorsque vous travaillez avec des données de grande dimension.  
  
    -   En revanche, la régularisation L2 est préférable pour les données qui ne sont pas éparses.  
  
     Cet algorithme prend en charge une combinaison de valeurs de régularisation L1 et L2 : autrement dit, si <code>x = L1</code> et <code>y = L2</code>, puis <code>ax + by = c</code> définit le délai linéaire des termes du contrat de régularisation.  
  
    > [!NOTE]
    >  Vous souhaitez en savoir plus sur la régularisation L1 et L2 ? L’article suivant fournit une description de la régularisation L1 et L2 sont différents et comment elles affectent l’ajustement du modèle, avec des exemples de code pour la régression logistique et de modèles de réseau neuronal :  [Régularisations L1 et L2 pour l’apprentissage](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Différentes combinaisons de linéaire des termes L1 et L2 ont été imaginés pour les modèles de régression logistique : par exemple, [élastique régularisation nette](https://wikipedia.org/wiki/Elastic_net_regularization). Nous vous suggérons de que vous référencez ces combinaisons pour définir une combinaison linéaire est efficace dans votre modèle.
      
5.  Pour **taille de la mémoire pour L-BFGS**, spécifiez la quantité de mémoire à utiliser pour *L-BFGS* optimisation.  
  
     L-BFGS est l’acronyme de « limited memory Broyden-Fletcher-Goldfarb-Shanno ». Il est un algorithme d’optimisation qui est souvent utilisé pour l’estimation des paramètres. Ce paramètre indique le nombre de positions passées et de gradients à stocker pour le calcul de l’étape suivante.  
  
     Ce paramètre d’optimisation limite la quantité de mémoire qui est utilisée pour calculer l’étape suivante et la direction. Lorsque vous spécifiez moins de mémoire, l’apprentissage est plus rapide mais moins précis.  
  
6.  Pour **valeur initiale de nombre aléatoire**, tapez une valeur entière. Définition d’une valeur de départ est important si vous souhaitez que les résultats puissent être reproductible sur plusieurs exécutions de la même expérience.  
  
  
8. Ajouter un jeu de données avec balises à l’expérience et deux de la [modules de formation](module-reference.md).  
  
    -   Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, utilisez le [former le modèle](./train-model.md) module.  
  
9. Exécutez l’expérience.  
  
## <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher un résumé des paramètres du modèle, ainsi que les poids de fonctionnalité tirées de formation, cliquez sur la sortie de [former le modèle](./train-model.md) et sélectionnez **visualiser**.   
  
+ Pour effectuer des prévisions sur de nouvelles données, utilisez le modèle formé et les nouvelles données en tant qu’entrée pour le [noter le modèle](./score-model.md) module. 


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 