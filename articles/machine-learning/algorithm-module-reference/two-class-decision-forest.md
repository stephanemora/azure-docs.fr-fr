---
title: 'Two-Class Decision Forest : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Two-Class Decision Forest dans le service Azure Machine Learning pour créer un modèle machine learning basé sur l’algorithme de forêts de décision.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f3e7cab6e33fa9373095441685f6ecb04f1d91a5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029369"
---
# <a name="two-class-decision-forest-module"></a>Module de Two-Class Decision Forest

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle machine learning basé sur l’algorithme de forêts de décision.  

Forêts décisionnelles sont des modèles d’ensemble rapides et supervisés. Ce module est un bon choix si vous souhaitez prédire une cible avec un maximum de deux résultats. 

## <a name="understanding-decision-forests"></a>Présentation des forêts de décision

Cet algorithme de forêt de décision est un méthode conçue pour les tâches de classification d’apprentissage d’ensemble. Méthodes de l’ensemble sont basées sur le principe général qui plutôt que d’utiliser un modèle unique, vous pouvez obtenir les meilleurs résultats et un modèle plus généralisé en créant plusieurs modèles associés et en les combinant d’une certaine façon. En règle générale, les modèles d’ensemble fournissent une meilleure couverture et précision que les arbres de décision uniques. 

Il existe de nombreuses façons de créer des modèles individuels et de les combiner dans un ensemble. Cette implémentation particulière d’une forêt de décision fonctionne en créant plusieurs arbres de décision, puis **vote** sur la classe de sortie la plus populaire. Le vote n’est une des méthodes plus connus pour générer des résultats dans un modèle d’ensemble. 

+ Nombreux arbres de classification individuels sont créés, à l’aide de l’ensemble du dataset, mais différents (généralement aléatoire) points de départ. Cela diffère de l’approche de forêt aléatoire, dans lequel les arbres de décision individuels utiliseriez uniquement une partie aléatoire des données ou des fonctionnalités.
+ Chaque arborescence dans l’arborescence de forêt de décision affiche un histogramme de fréquence non normalisée des étiquettes. 
+ Le processus d’agrégation additionne ces histogrammes et normalise le résultat pour obtenir les « probabilités » pour chaque étiquette. 
+ Les arborescences qui ont confiance dans les prévisions haute aura un poids supérieur à la décision finale de l’ensemble.

Arbres de décision présentent en général de nombreux avantages pour les tâches de classification :
  
- Elles peuvent capturer les limites de décision non linéaires.
- Vous pouvez effectuer l’apprentissage et établir des prédictions sur un grand nombre de données, tels qu’ils sont efficaces dans l’utilisation de calcul et de la mémoire.
- Sélection de fonctionnalités est intégrée dans les processus de formation et de classification.  
- Arborescences peuvent prendre en charge les données bruyantes et nombreuses fonctionnalités.  
- Ils sont des modèles non paramétriques, ce qui signifie qu’elles peuvent traiter les données avec diverses distributions. 

Toutefois, les arbres de décision simple peuvent dépasser sur les données et sont moins généralisables que les ensembles d’arborescence.

Pour plus d’informations, consultez [forêts décisionnelles](http://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Comment configurer
  
1.  Ajouter le **Two-Class Decision Forest** module à votre expérience dans Azure Machine Learning, puis ouvrez le **propriétés** volet du module. 

    Vous pouvez rechercher le module sous **Machine Learning**. Développez **initialiser**, puis **Classification**.  
  
2.  Pour **méthode de rééchantillonnage**, choisissez la méthode utilisée pour créer les arbres individuels.  Vous pouvez choisir parmi **Bagging** ou **répliquer**.  
  
    -   **Bagging**: ENSACHAGE est également appelé *agrégation bootstrap*. Dans cette méthode, chaque arborescence est développé sur un nouvel échantillon, créé par le jeu de données d’origine avec le remplacement de l’échantillonnage au hasard jusqu'à ce que vous avez un jeu de données de la taille d’origine.  
  
         Les sorties des modèles sont combinées par *vote*, qui est un formulaire d’agrégation. Chaque arborescence dans une forêt de décision de classification affiche un histogramme de fréquence non normalisé d’étiquettes. L’agrégation consiste à additionner ces histogrammes et normaliser pour obtenir les « probabilités » pour chaque étiquette. De cette manière, les arborescences qui ont confiance dans les prévisions haute aura un poids supérieur à la décision finale de l’ensemble.  
  
         Pour plus d’informations, consultez la page Wikipedia pour agréger les données d’amorçage.  
  
    -   **Répliquer**: Dans la réplication, chaque arborescence est formé sur exactement les mêmes données d’entrée. La détermination du fractionner le prédicat est utilisé pour chaque nœud d’arbre reste aléatoire et les arborescences seront différents.   
  
3.  Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.  
  
    -   **L’unique paramètre**: Si vous savez comment vous souhaitez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs comme arguments.
  
4.  Pour **nombre d’arbres de décision**, tapez le nombre maximal d’arbres de décision qui peuvent être créés dans l’ensemble. En créant plusieurs arbres de décision, vous pouvez obtenir une meilleure couverture, mais augmente la durée d’apprentissage.  
  
    > [!NOTE]
    >  Cette valeur contrôle également le nombre d’arbres affichés lorsque vous examinez le modèle formé. Si vous souhaitez afficher ou imprimer une arborescence unique, vous pouvez définir la valeur sur 1. Toutefois, qu’une seule arborescence peut être générés (l’arborescence avec le jeu de paramètres initial) et aucune itération n’est effectuée.
  
5.  Pour **profondeur maximale des arbres de décision**, tapez un nombre pour limiter la profondeur maximale de n’importe quel arbre de décision. Augmentation de la profondeur de l’arborescence peut accroître la précision, avec comme corolaire des temps de formation surajustement et d’augmentation.
  
6.  Pour **nombre de fractionnements aléatoires par nœud**, tapez le nombre de divisions à utiliser lors de la génération de chaque nœud de l’arborescence. Un *fractionner* signifie que les fonctionnalités de chaque niveau de l’arbre (nœud) est réparties au hasard.
  
7.  Pour **nombre minimal d’échantillons par nœud terminal**, indiquer le nombre minimal de cas qui sont nécessaires pour créer un nœud terminal (feuille) dans une arborescence.
  
     En augmentant cette valeur, vous augmentez le seuil pour la création de nouvelles règles. Par exemple, la valeur par défaut de 1, même un seul cas peut entraîner une nouvelle règle à créer. Si vous augmentez la valeur à 5, les données d’apprentissage doivent contenir au moins cinq cas qui remplissent les mêmes conditions.  
  
8.  Sélectionnez le **autoriser les valeurs inconnues pour les fonctionnalités catégorielles** option pour créer un groupe pour les valeurs inconnues dans les jeux d’apprentissage ou de validation. Le modèle peut être moins précis pour les valeurs connues, mais il peut fournir de meilleures prédictions pour les nouvelles valeurs (inconnues). 

     Si vous désélectionnez cette option, le modèle peut accepter uniquement les valeurs qui sont contenus dans les données d’apprentissage.
  
9. Joindre un jeu de données étiquetée et l’autre de la [modules de formation](module-reference.md):  
  
    -   Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, utilisez le [former le modèle](./train-model.md) module.  
  
    
## <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher l’arborescence qui a été créé sur chaque itération, cliquez sur la sortie de la [former le modèle](./train-model.md) module, puis sélectionnez **visualiser**.
  
    Cliquez sur chaque arbre pour Explorer les divisions et d’afficher les règles pour chaque nœud.

+ Pour enregistrer un instantané du modèle, cliquez sur le **formé** de sortie, puis sélectionnez **enregistrer le modèle**. Le modèle enregistré n’est pas mis à jour lors des exécutions consécutives de l’expérience.

+ Pour utiliser le modèle pour calculer les scores, ajoutez le **noter le modèle** module à une expérience.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 