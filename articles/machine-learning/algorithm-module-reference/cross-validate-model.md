---
title: 'Modèle de validation croisée : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Modèle de validation croisée dans Azure Machine Learning Service afin d’effectuer une validation croisée des estimations de paramètre pour les modèles de classification ou de régression en partitionnant les données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5eea61ee8284010531e80e17bf1110ab470d04c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510286"
---
# <a name="cross-validate-model"></a>Modèle de validation croisée

Cet article décrit comment utiliser le module **Modèle de validation croisée** dans le concepteur Azure Machine Learning (préversion). La *validation croisée* est une technique importante souvent utilisée en machine learning pour évaluer la variabilité d’un jeu de données et la fiabilité de tout modèle entraîné à l’aide de ces données.  

Le module **Modèle de validation croisée** prend comme entrée un jeu de données étiqueté ainsi qu’un modèle de classification ou de régression non entraîné. Il divise le jeu de données en un certain nombre de sous-ensembles (*échantillons*), crée un modèle sur chaque échantillon, puis retourne un jeu de statistiques de justesse pour chaque échantillon. En comparant les statistiques de justesse pour tous les échantillons, vous pouvez interpréter la qualité du jeu de données et déterminer si le modèle est sensible aux variations des données.  

La validation croisée retourne également des résultats prédits et des probabilités pour le jeu de données, afin que vous puissiez évaluer la fiabilité des prédictions.  

### <a name="how-cross-validation-works"></a>Fonctionnement de la validation croisée

1. La validation croisée divise de manière aléatoire les données d’entraînement en plusieurs partitions, également appelées *échantillons*. 

    + L’algorithme se base par défaut sur 10 échantillons si vous n’avez pas déjà partitionné le jeu de données. 
    + Pour diviser le jeu de données en un nombre différent d’échantillons, vous pouvez utiliser le module [Partition et échantillon](partition-and-sample.md) et indiquer le nombre d’échantillons à utiliser.  

2.  Le module met de côté les données de l’échantillon 1 à utiliser pour la validation (parfois appelée *échantillon de données d’exclusion*) et utilise les échantillons restants pour entraîner un modèle. 

    Par exemple, si vous créez cinq échantillons, le module génère cinq modèles au cours de la validation croisée, chaque modèle entraîné utilisant quatre cinquièmes des données et étant testé sur le cinquième restant.  

3.  Lors du test du modèle pour chaque échantillon, plusieurs statistiques de justesse sont évaluées. Les statistiques utilisées dépendent du type de modèle en cours d’évaluation. Différentes statistiques sont utilisées pour évaluer les modèles de classification et les modèles de régression.  

4.  Quand le processus de génération et d’évaluation est terminé pour tous les échantillons, le module **Modèle de validation croisée** génère un ensemble de métriques de performances et de résultats notés pour toutes les données. Vous devez passer en revue ces métriques pour déterminer si le moindre échantillon a une justesse particulièrement élevée ou faible. 

### <a name="advantages-of-cross-validation"></a>Avantages de la validation croisée

Une méthode différente et courante pour l’évaluation d’un modèle consiste à diviser les données en un jeu d’entraînement et de test à l’aide du module [Fractionner les données](split-data.md), puis à valider le modèle sur les données d’entraînement. Toutefois, la validation croisée offre certains avantages :  

-   La validation croisée utilise davantage de données de test.

     La validation croisée mesure les performances du modèle avec les paramètres spécifiés dans un espace de données plus grand. Autrement dit, la validation croisée utilise la totalité du jeu de données d’entraînement pour l’entraînement et l’évaluation, au lieu d’une partie. En revanche, si vous validez un modèle à l’aide de données générées à partir d’un fractionnement aléatoire, vous évaluez généralement le modèle uniquement sur 30 % ou moins des données disponibles.  

     Toutefois, étant donné que la validation croisée entraîne et valide le modèle plusieurs fois sur un jeu de données plus grand, elle est beaucoup plus gourmande en ressources de calcul et prend beaucoup plus de temps que la validation sur un fractionnement aléatoire.  

-   La validation croisée évalue le jeu de données ainsi que le modèle.

     La validation croisée ne mesure pas simplement la justesse d’un modèle, mais vous donne également une idée du degré de représentativité du jeu de données et de la sensibilité du modèle aux variations des données.  

## <a name="how-to-use-cross-validate-model"></a>Comment utiliser le modèle de validation croisée

L’exécution de la validation croisée peut prendre beaucoup de temps si votre jeu de données est volumineux.  Ainsi, vous pouvez utiliser le module **Modèle de validation croisée** dans la phase initiale de création et de test de votre modèle, afin d’évaluer l’intérêt des paramètres du modèle (en supposant que le temps de calcul est tolérable), puis effectuer l’entraînement et l’évaluation de votre modèle à l’aide des paramètres établis avec les modules [Entraîner un modèle](train-model.md) et [Évaluer un modèle](evaluate-model.md).

Dans ce scénario, vous entraînez et testez le modèle à l’aide du module **Modèle de validation croisée**.

1. Ajoutez le module **Modèle de validation croisée** à votre pipeline. Vous pouvez le trouver dans le concepteur Azure Machine Learning, dans la catégorie **Scoring & évaluation de modèle**. 

2. Connectez la sortie de n’importe quel modèle de **classification** ou de **régression**. 

    Par exemple, si vous utilisez une **machine de point de Bayes à deux classes** pour la classification, configurez le modèle avec les paramètres souhaités, puis faites glisser un connecteur depuis le port **Modèle non entraîné** du classifieur vers le port correspondant de **Modèle de validation croisée**. 

    > [!TIP] 
    > Le modèle n’a pas besoin d’être entraîné, car le module **Modèle de validation croisée** l’entraîne automatiquement dans le cadre de l’évaluation.  
3.  Sur le port **Jeu de données** du module **Modèle de validation croisée**, connectez tout jeu de données d’entraînement étiqueté.  

4.  Dans le volet **Propriétés** du module **Modèle de validation croisée**, cliquez sur **Lancer le sélecteur de colonne** et choisissez la colonne unique qui contient l’étiquette de classe ou la valeur prévisible. 

5. Définissez une valeur pour le paramètre **Valeur de départ aléatoire** si vous souhaitez pouvoir répéter les résultats de la validation croisée entre des exécutions successives sur les mêmes données.  

6.  Exécuter le pipeline.

7. Pour obtenir une description des rapports, consultez la section [Résultats](#results).

    Pour obtenir une copie du modèle en vue d’une réutilisation ultérieure, cliquez avec le bouton droit sur la sortie du module qui contient l’algorithme (par exemple, la **machine de point de Bayes à deux classes**), puis cliquez sur **Enregistrer en tant que modèle entraîné**.

## <a name="results"></a>Résultats

Une fois toutes les itérations terminées, le module **Modèle de validation croisée** crée des scores pour l’ensemble du jeu de données ainsi que des métriques de performances que vous pouvez utiliser pour évaluer la qualité du modèle.

### <a name="scored-results"></a>Résultats notés

La première sortie du module fournit les données sources pour chaque ligne ainsi que des valeurs prédites et des probabilités associées. 

Pour voir ces résultats, dans le pipeline, cliquez avec le bouton droit sur le module **Modèle de validation croisée**, sélectionnez **Résultats notés**, puis cliquez sur **Visualiser**.

| Nouveau nom de colonne      | Description                              |
| -------------------- | ---------------------------------------- |
| Étiquettes notées        | Cette colonne est ajoutée à la fin du jeu de données et contient la valeur prédite pour chaque ligne. |
| Probabilités notées | Cette colonne est ajoutée à la fin du jeu de données et indique la probabilité estimée de la valeur dans **Étiquettes notées**. |
| Nombre d’échantillons          | Indique l’index de base 0 de l’échantillon auquel chaque ligne de données a été attribuée lors de la validation croisée. |

 ### <a name="evaluation-results"></a>Evaluation results

Le deuxième rapport est regroupé par échantillons. N’oubliez pas que, pendant l’exécution, le module **Modèle de validation croisée** fractionne de façon aléatoire les données d’entraînement en *n* échantillons (par défaut, 10). Dans chaque itération sur le jeu de données, le module **Modèle de validation croisée** utilise un échantillon comme jeu de données de validation et utilise les *n-1* échantillons restants pour entraîner un modèle. Chacun des *n* modèles est testé par rapport aux données de tous les autres échantillons.

Dans ce rapport, les échantillons sont listés par valeur d’index, dans l’ordre croissant.  Pour effectuer un tri sur une autre colonne, vous pouvez enregistrer les résultats sous la forme d’un jeu de données.

Pour voir ces résultats, dans le pipeline, cliquez avec le bouton droit sur le module **Modèle de validation croisée**, sélectionnez **Résultats de l’évaluation par échantillon**, puis cliquez sur **Visualiser**.


|Nom de la colonne| Description|
|----|----|
|Nombre d’échantillons| Identificateur pour chaque échantillon. Si vous avez créé 5 échantillons, il y a 5 sous-ensembles de données, numérotés de 0 à 4.
|Nombre d’exemples dans l’échantillon|Nombre de lignes affectées à chaque échantillon. Ils doivent être à peu près égaux. |


En outre, les métriques suivantes sont incluses pour chaque échantillon, en fonction du type de modèle que vous évaluez. 

+ **Modèles de classification** : précision, rappel, F-score, AUC, justesse  

+ **Modèles de régression** : erreur d’absolue moyenne, erreur carrée moyenne racine, erreur d’absolue relative, erreur carrée relative et coefficient de détermination


## <a name="technical-notes"></a>Notes techniques  

+ Il est recommandé de normaliser les jeux de données avant de les utiliser pour la validation croisée. 

+ Étant donné que le module **Modèle de validation croisée** entraîne et valide le modèle plusieurs fois, il est beaucoup plus gourmand en calculs et prend plus de temps que si vous validiez le modèle à l’aide d’un jeu de données divisé de manière aléatoire. 

+ Il n’est pas nécessaire de fractionner le jeu de données en jeux d’entraînement et de test quand vous utilisez la validation croisée pour mesurer la justesse du modèle. 


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 

