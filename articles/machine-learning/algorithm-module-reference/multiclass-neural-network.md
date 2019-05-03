---
title: 'Réseau neuronal multiclasse : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Multiclass Neural Network dans le service Azure Machine Learning pour créer un modèle de réseau neuronal qui peut être utilisé pour prédire une cible ayant plusieurs valeurs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7bef225c001ebd9bbb9a45c8bcc301cfb49edb6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029384"
---
# <a name="multiclass-neural-network-module"></a>Module de réseau neuronal multiclasse

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle de réseau neuronal qui peut être utilisé pour prédire une cible ayant plusieurs valeurs. 

Par exemple, les réseaux neuronaux de ce type peuvent être utilisés dans les tâches de vision informatiques complexes, telles que reconnaissance de chiffres ou de lettres, de classification de documents et de reconnaissance de suites logiques.

Classification à l’aide de réseaux neuronaux est une méthode d’apprentissage supervisé et nécessite donc un *balisés dataset* qui inclut une colonne d’étiquette.

Vous pouvez former le modèle en fournissant le modèle et le jeu de données avec balises en tant qu’entrée [former le modèle](./train-model.md). Le modèle formé peut ensuite être utilisé pour prédire des valeurs pour les nouveaux exemples d’entrée.  

## <a name="about-neural-networks"></a>Les réseaux neuronaux

Un réseau neuronal est un ensemble de couches connectées entre elles. Les entrées sont la première couche et sont connectées à une couche de sortie via un graphique acyclique constitué de nœuds et de bords pondérés.

Entre les couches d’entrée et de sortie, vous pouvez insérer plusieurs couches masquées. La plupart des tâches prédictives peuvent être accomplies facilement avec seulement une ou plusieurs couches masquées. Toutefois, recherche récente a montré que les réseaux neuronaux profonds (DNN) avec nombreuses couches peuvent être efficaces dans les tâches complexes telles que la reconnaissance vocale ou des images. Les couches successives sont utilisées pour modéliser des niveaux accrus de profondeur sémantique.

La relation entre les entrées et sorties tirée d’apprentissage du réseau neuronal sur les données d’entrée. Le sens du graphique se poursuit à partir des entrées via la couche masquée, vers la couche de sortie. Tous les nœuds dans une couche sont connectés par les bords pondérés aux nœuds de la couche suivante.

Pour calculer la sortie du réseau pour une entrée donnée, une valeur est calculée sur chaque nœud dans les couches masquées et dans la couche de sortie. La valeur est définie en calculant la somme pondérée des valeurs des nœuds de la couche précédente. Une fonction d’activation est ensuite appliquée à cette somme pondérée.

## <a name="configure-multiclass-neural-network"></a>Configurer le réseau neuronal Multiclasse

1. Ajouter le **MultiClass Neural Network** module à votre expérience dans l’interface. Vous trouverez ce module sous **Machine Learning**, **initialiser**, dans le **Classification** catégorie.

2. **Créer un mode d’entraînement**: Utilisez cette option pour spécifier comment vous voulez l’apprentissage du modèle :

    - **L’unique paramètre**: Choisissez cette option si vous savez déjà comment vous souhaitez configurer le modèle.

    

3. **Spécification de la couche de masqué**: Sélectionnez le type d’architecture de réseau à créer.

    - **Entièrement connecté cas**: Sélectionnez cette option pour créer un modèle à l’aide de l’architecture de réseau neuronal par défaut. Pour les modèles de réseau neuronal multiclasse, les valeurs par défaut sont les suivantes :

        - Une seule couche masquée
        - La couche de sortie est entièrement connectée à la couche masquée.
        - La couche masquée est entièrement connectée à la couche d’entrée.
        - Le nombre de nœuds dans la couche d’entrée est déterminé par le nombre de fonctionnalités dans les données d’apprentissage.
        - Le nombre de nœuds dans la couche masquée peut être défini par l’utilisateur. La valeur par défaut est 100.
        - Le nombre de nœuds dans la couche de sortie varie selon le nombre de classes.
  
   

5. **Nombre de nœuds masqués**: Cette option vous permet de personnaliser le nombre de nœuds masqués dans l’architecture par défaut. Tapez le nombre de nœuds masqués. La valeur par défaut est une seule couche cachée avec 100 nœuds.

6. **Le taux d’apprentissage**: Définir la taille de l’opération effectuée à chaque itération, avant correction. Une valeur supérieure pour le taux d’apprentissage peut amener le modèle à converger plus rapidement, mais il peut dépasser les minima locaux.

7. **Nombre d’itérations d’apprentissage**: Spécifiez le nombre maximal de fois où que l’algorithme doit traiter les cas d’apprentissage.

8. **L’apprentissage initial pondère le diamètre**: Spécifier les pondérations des nœuds au début du processus d’apprentissage.

9. **L’inertie**: Spécifier une pondération à appliquer pendant l’apprentissage aux nœuds des itérations précédentes.
  
11. **Exemples de lecture aléatoire**: Sélectionnez cette option pour la lecture aléatoire de cas entre les itérations.

    Si vous désélectionnez cette option, cas sont traités dans le même ordre chaque fois que vous exécutez l’expérience.

12. **Valeur de départ de nombre aléatoire**: Tapez une valeur à utiliser comme valeur initiale, si vous souhaitez garantir la répétabilité entre les exécutions de la même expérience.

14. Connecter un jeu de données de formation et l’autre de la [modules de formation](module-reference.md): 

    - Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, utilisez [former le modèle](train-model.md).  
  

## <a name="results"></a>Résultats

Une fois la formation terminée :

- Pour afficher un résumé des paramètres du modèle, ainsi que la fonctionnalité de poids tirées de formation et d’autres paramètres du réseau neuronal, avec le bouton droit la sortie de [former le modèle](./train-model.md) et sélectionnez **visualiser**.  

- Pour enregistrer un instantané du modèle formé, cliquez sur le **ajouté pour l’apprentissage modèle** de sortie et sélectionnez **enregistrer en tant que modèle formé**. Ce modèle n’est pas mis à jour lors des exécutions consécutives de la même expérience.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 