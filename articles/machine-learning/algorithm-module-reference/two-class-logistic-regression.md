---
title: 'Régression logistique à deux classes : informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module de régression logistique à deux classes dans le service Azure Machine Learning pour créer un modèle de régression logistique qui peut être utilisé pour prédire seulement deux résultats.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 818b8627decd5ee7db711abc417f71c83e32b6c0
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128381"
---
# <a name="two-class-logistic-regression-module"></a>Module de régression logistique à deux classes

Cet article décrit un module de l’interface visuelle (préversion) du service Azure Machine Learning.

Utilisez ce module pour créer un modèle de régression logistique qui peut être utilisé pour prédire seulement deux résultats. 

La régression logistique est une technique statistique connue utilisée pour modéliser de nombreux types de problèmes. Cet algorithme est une méthode d’*apprentissage supervisé*. Par conséquent, vous devez fournir un jeu de données qui contient déjà les résultats pour former le modèle.  

### <a name="about-logistic-regression"></a>À propos de la régression logistique  

La régression logistique est une méthode bien connue en statistiques qui sert à déterminer la probabilité d’un résultat et est particulièrement populaire pour les tâches de classification. L’algorithme prédit la probabilité d’occurrence d’un événement en ajustant les données à une fonction logistique.
  
Dans ce module, l’algorithme de classification est optimisé pour les variables dichotomiques ou binaires. Si vous avez besoin de classer plusieurs résultats, utilisez le module de [régression logistique multiclasse](./multiclass-logistic-regression.md).

##  <a name="how-to-configure"></a>Comment configurer  

Pour entraîner ce modèle, vous devez fournir un jeu de données qui contient une colonne d’étiquette ou de classe. Étant donné que ce module est destiné aux problèmes à deux classes, la colonne d’étiquette ou de classe doit contenir exactement deux valeurs. 

Par exemple, la colonne d’étiquette peut être [Voté], avec les valeurs possibles « Oui » ou « Non ». Ou bien, elle peut être [Risque de crédit], avec les valeurs possibles « Élevé » ou « Faible ». 
  
1.  Ajouter le module de **régression logistique à deux classes** à votre expérience.  
  
2.  Spécifiez le mode d’apprentissage du modèle en définissant l’option **Create trainer mode** (Créer un mode d’apprentissage).  
  
    -   **Paramètre unique** : si vous savez comment vous voulez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs en tant qu’arguments.  
  
3.  Pour la **tolérance d’optimisation**, spécifiez une valeur de seuil à utiliser lors de l’optimisation du modèle. Si l’amélioration entre les itérations tombe au-dessous du seuil spécifié, on considère que l’algorithme a convergé vers une solution, et l’entraînement cesse.  
  
4.  Pour la **pondération de régularisation L1** et la **pondération de régularisation L2**, saisissez une valeur à utiliser pour les paramètres de régularisation L1 et L2. Une valeur non nulle est recommandée pour les deux.  
  
     La *régularisation* est une méthode qui sert à empêcher le surajustement en pénalisant les modèles avec des valeurs de coefficient extrêmes. La régularisation fonctionne en ajoutant la pénalité associée aux valeurs de coefficient à l’erreur de l’hypothèse. Par conséquent, un modèle précis avec des valeurs de coefficient extrêmes est plus pénalisé, mais un modèle moins précis avec des valeurs plus conservatrices est moins pénalisé.  
  
     Les régularisations L1 et L2 ont différents effets et cas d’utilisation.  
  
    -   La régularisation L1 est applicable aux modèles dispersés, ce qui est utile lorsque vous travaillez avec des données de grande dimension.  
  
    -   En revanche, la régularisation L2 est préférable pour les données qui ne sont pas dispersées.  
  
     Cet algorithme prend en charge une combinaison linéaire de valeurs de régularisation L1 et L2 : autrement dit, si <code>x = L1</code> et <code>y = L2</code>, <code>ax + by = c</code> définit l’étendue linéaire des termes du contrat de régularisation.  
  
    > [!NOTE]
    >  Vous souhaitez en savoir plus sur la régularisation L1 et L2 ? L’article suivant explore les différences entre les régularisations L1 et L2 et comment elles influent sur l’ajustement du modèle, avec des exemples de code pour les modèles de régression logistique et de réseau neuronal :  [Régularisations L1 et L2 pour l’apprentissage automatique](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Différentes combinaisons linéaires de termes L1 et L2 ont été imaginées pour les modèles de régression logistique : par exemple, [régularisation de réseau élastique](https://wikipedia.org/wiki/Elastic_net_regularization). Nous vous suggérons de faire référence à ces combinaisons pour définir une combinaison linéaire est efficace dans votre modèle.
      
5.  Pour la **taille de la mémoire pour L-BFGS**, spécifiez la quantité de mémoire à utiliser pour l’optimisation *L-BFGS*.  
  
     L-BFGS est l’acronyme de « limited memory Broyden-Fletcher-Goldfarb-Shanno ». Il s’agit d’un algorithme d’optimisation souvent utilisé pour l’estimation des paramètres. Ce paramètre indique le nombre de positions passées et de gradients à stocker pour le calcul de l’étape suivante.  
  
     Ce paramètre d’optimisation limite la quantité de mémoire utilisée pour calculer l’étape suivante et la direction. Lorsque vous spécifiez moins de mémoire, l’apprentissage est plus rapide mais moins précis.  
  
6.  Pour la **valeur initiale aléatoire**, saisissez une valeur entière. Il est important de définir une valeur de départ si vous souhaitez obtenir des résultats reproductibles sur plusieurs exécutions de la même expérience.  
  
  
8. Ajoutez un jeu de données avec balises à l’expérience, puis connectez l’un des [modules de formation](module-reference.md).  
  
    -   Si vous définissez **Create trainer mode** (Créer un mode d’apprentissage) sur **Single Parameter** (Paramètre unique), utilisez le module [Train Model](./train-model.md) (Entraîner le modèle).  
  
9. Exécutez l’expérience.  
  
## <a name="results"></a>Résultats

Une fois l’apprentissage terminé :

+ Pour afficher un résumé des paramètres du modèle avec les pondérations de caractéristiques tirées de l’apprentissage, cliquez avec le bouton droit sur la sortie du module [Train Model](./train-model.md) (Entraîner le modèle), puis sélectionnez **Visualiser**.   
  
+ Pour effectuer des prévisions sur de nouvelles données, utilisez le modèle entraîné et les nouvelles données en tant qu’entrée pour le module de [notation de modèle](./score-model.md). 


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 