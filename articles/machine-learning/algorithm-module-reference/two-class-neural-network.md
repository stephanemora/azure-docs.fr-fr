---
title: 'Réseau neuronal à deux classes : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Two-Class Neural Network dans le service Azure Machine Learning pour créer un modèle de réseau neuronal qui peut être utilisé pour prédire une cible ayant uniquement deux valeurs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7ea852fcd312c6f7b1b716278ed538b7accde5bd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029219"
---
# <a name="two-class-neural-network-module"></a>Module de réseau neuronal à deux classes

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle de réseau neuronal qui peut être utilisé pour prédire une cible ayant uniquement deux valeurs.

Classification à l’aide de réseaux neuronaux est une méthode d’apprentissage supervisé et nécessite donc un *balisés dataset*, qui inclut une colonne d’étiquette. Par exemple, vous pouvez utiliser ce modèle de réseau neuronal pour prédire les résultats binaires telles qu’ou non un patient d’une maladie, ou si un ordinateur est susceptible d’échouer au sein d’une fenêtre de temps donnée.  

Après avoir défini le modèle, effectuez l’apprentissage en fournissant un jeu de données avec balises et le modèle en tant qu’entrée [former le modèle](./train-model.md). Le modèle formé peut ensuite être utilisé pour prédire des valeurs pour les nouvelles entrées.

### <a name="more-about-neural-networks"></a>Informations sur les réseaux neurones

Un réseau neuronal est un ensemble de couches connectées entre elles. Les entrées sont la première couche et sont connectées à une couche de sortie via un graphique acyclique constitué de nœuds et de bords pondérés.

Entre les couches d’entrée et de sortie, vous pouvez insérer plusieurs couches masquées. La plupart des tâches prédictives peuvent être accomplies facilement avec seulement une ou plusieurs couches masquées. Toutefois, recherche récente a montré que les réseaux neuronaux profonds (DNN) avec nombreuses couches peuvent être efficaces dans les tâches complexes telles que la reconnaissance vocale ou des images. Les couches successives sont utilisées pour modéliser des niveaux accrus de profondeur sémantique.

La relation entre les entrées et sorties tirée d’apprentissage du réseau neuronal sur les données d’entrée. Le sens du graphique se poursuit à partir des entrées via la couche masquée, vers la couche de sortie. Tous les nœuds dans une couche sont connectés par les bords pondérés aux nœuds de la couche suivante.

Pour calculer la sortie du réseau pour une entrée donnée, une valeur est calculée sur chaque nœud dans les couches masquées et dans la couche de sortie. La valeur est définie en calculant la somme pondérée des valeurs des nœuds de la couche précédente. Une fonction d’activation est ensuite appliquée à cette somme pondérée.
  
## <a name="how-to-configure"></a>Comment configurer

1.  Ajouter le **Two-Class Neural Network** module à votre expérience. Vous trouverez ce module sous **Machine Learning**, **initialiser**, dans le **Classification** catégorie.  
  
2.  Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.  
  
    -   **L’unique paramètre**: Choisissez cette option si vous savez déjà comment vous souhaitez configurer le modèle.  

3.  Pour **masqué de spécification de la couche**, sélectionnez le type d’architecture de réseau à créer.  
  
    -   **Entièrement connecté cas**: Utilise l’architecture de réseau neuronal par défaut défini pour les réseaux neuronaux deux classes comme suit :
  
        -   A une couche cachée.
  
        -   La couche de sortie est entièrement connectée à la couche masquée, et la couche masquée est entièrement connectée à la couche d’entrée.
  
        -   Le nombre de nœuds dans la couche d’entrée est égal au nombre de fonctionnalités dans les données d’apprentissage.
  
        -   Le nombre de nœuds de la couche masquée est défini par l’utilisateur. La valeur par défaut est 100.
  
        -   Le nombre de nœuds est égal au nombre de classes. Pour un réseau neuronal à deux classes, cela signifie que toutes les entrées doivent mapper à un des deux nœuds dans la couche de sortie.

5.  Pour **taux d’apprentissage**, définissez la taille de l’opération effectuée à chaque itération, avant correction. Une valeur supérieure pour le taux d’apprentissage peut amener le modèle à converger plus rapidement, mais il peut dépasser les minima locaux.

6.  Pour **nombre d’itérations d’apprentissage**, spécifiez le nombre maximal de fois où l’algorithme doit traiter les cas d’apprentissage.

7.  Pour **l’apprentissage initial pondère le diamètre**, spécifiez les pondérations des nœuds au début du processus d’apprentissage.

8.  Pour **l’inertie**, spécifier une pondération à appliquer pendant l’apprentissage aux nœuds des itérations précédentes  

10. Sélectionnez le **exemples de lecture aléatoire** option réorganisation de cas entre les itérations. Si vous désélectionnez cette option, cas sont traités dans le même ordre chaque fois que vous exécutez l’expérience.
  
11. Pour **valeur initiale de nombre aléatoire**, tapez une valeur à utiliser comme valeur initiale.
  
     En spécifiant une valeur initiale valeur est utile lorsque vous souhaitez garantir la répétabilité entre les exécutions de la même expérience.  Sinon, une valeur de l’horloge système est utilisée comme valeur initiale, ce qui peut entraîner des résultats légèrement différents chaque fois que vous exécutez l’expérience.
  
13. Ajouter un jeu de données avec balises à l’expérience et deux de la [modules de formation](module-reference.md).  
  
    -   Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, utilisez le [former le modèle](train-model.md) module.  
  
14. Exécutez l’expérience.

## <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher un résumé des paramètres du modèle, ainsi que la fonctionnalité de poids tirées de formation et d’autres paramètres du réseau neuronal, avec le bouton droit la sortie de [former le modèle](./train-model.md), puis sélectionnez **visualiser**.  

+ Pour enregistrer un instantané du modèle formé, cliquez sur le **ajouté pour l’apprentissage modèle** de sortie et sélectionnez **enregistrer en tant que modèle formé**. Ce modèle n’est pas mis à jour lors des exécutions consécutives de la même expérience.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 
