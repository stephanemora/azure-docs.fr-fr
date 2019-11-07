---
title: 'Importance de la fonctionnalité de permutation : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Importance de la fonctionnalité de permutation dans le service Azure Machine Learning pour calculer les scores d’importance des fonctionnalités de permutation des variables de fonctionnalité en fonction d’un modèle formé et d’un jeu de données de test.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 8a2ddb92101957a3dcadebb17dffa39113825e4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511854"
---
# <a name="permutation-feature-importance"></a>Importance de la fonctionnalité de permutation

Cet article explique comment utiliser le module [Importance de la fonctionnalité de permutation](permutation-feature-importance.md) dans Azure Machine Learning Designer (préversion), pour calculer un ensemble de scores d’importance de fonctionnalité pour votre jeu de données. Vous utilisez ces scores pour vous aider à déterminer les meilleures fonctionnalités à utiliser dans un modèle.

Dans ce module, les valeurs des fonctionnalités sont mélangées aléatoirement, une colonne à la fois, et les performances du modèle sont mesurées avant et après. Vous pouvez choisir l’une des métriques standards fournies pour mesurer les performances.

Les scores retournés par le module représentent la **modification** dans les performances d’un modèle formé, après permutation. Les fonctionnalités importantes sont généralement plus sensibles au processus de mélange et entraînent donc des scores d’importance plus élevés. 

Cet article fournit une vue d’ensemble générale de l’importance de la fonctionnalité de permutation, sa base théorique et ses applications dans Machine Learning : [Importance de la fonctionnalité de permutation](http://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)  

## <a name="how-to-use-permutation-feature-importance"></a>Comment utiliser l’importance de la fonctionnalité de permutation

Pour générer un ensemble de scores de fonctionnalités, vous devez disposer d’un modèle déjà formé, ainsi que d’un jeu de données de test.  

1.  Ajoutez le module **Importance de la fonctionnalité de permutation** à votre pipeline. Ce module figure dans la catégorie **Sélection des caractéristiques**. 

2.  Connectez un modèle formé à l’entrée gauche. Le modèle doit être un modèle de régression ou un modèle de classification.  

3.  Sur l’entrée droite, connectez un jeu de données, de préférence différent de celui utilisé pour l’apprentissage du modèle. Ce jeu de données est utilisé pour le calcul des scores basés sur le modèle formé et pour l’évaluation du modèle après la modification des valeurs des fonctionnalités.  

4.  Pour **Valeur de départ aléatoire**, saisissez une valeur à utiliser comme valeur de départ pour la répartition aléatoire. Si vous spécifiez 0 (valeur par défaut), un nombre est généré en fonction de l’horloge système.

     Une valeur de départ est facultative, mais vous devez fournir une valeur si vous souhaitez que la reproductibilité s’effectue entre les exécutions du même pipeline.  

5.  Pour **Métrique pour la mesure des performances**, sélectionnez une métrique unique à utiliser lors du calcul de la qualité du modèle après la permutation.  

     Le concepteur Azure Machine Learning prend en charge les métriques suivantes, selon que vous évaluez un modèle de classification ou de régression :  

    -   **Classification**

        Exactitude, Précision, Rappel, Perte moyenne de journal  

    -   **Régression**

        Précision, Rappel, Erreur d’absolue moyenne, Erreur carrée moyenne racine, Erreur d’absolue relative, Erreur carrée relative, Coefficient de détermination  

     Pour obtenir une description plus détaillée de ces mesures d’évaluation et de la façon dont elles sont calculées, consultez [Évaluer le modèle](evaluate-model.md).  

6.  Exécuter le pipeline.  

7.  Le module génère une liste de colonnes de fonctionnalités et les scores qui leur sont associés, classés dans l’ordre des scores, décroissant.  


##  <a name="technical-notes"></a>Notes techniques

Cette section contient des détails, des conseils et des réponses aux questions fréquentes concernant l’implémentation.

### <a name="how-does-this-compare-to-other-feature-selection-methods"></a>En quoi cela peut-il être comparé aux autres méthodes de sélection de fonctionnalités ?

L’importance de la fonctionnalité de permutation consiste à modifier de façon aléatoire les valeurs de chaque colonne de fonctionnalité, une colonne à la fois, puis à évaluer le modèle. 

Les classements fournis par l’importance de la fonctionnalité de permutation sont souvent différents de ceux que vous obtenez à partir de la [sélection de caractéristiques par filtrage](filter-based-feature-selection.md), qui calcule les scores **avant** la création d’un modèle. 

En effet, l’importance de la fonctionnalité de permutation ne mesure pas l’association entre une fonctionnalité et une valeur cible, mais elle capture à la place l’influence de chaque fonctionnalité sur les prédictions du modèle.
  
## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 
