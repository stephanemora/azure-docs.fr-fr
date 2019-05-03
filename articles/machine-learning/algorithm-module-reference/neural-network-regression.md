---
title: 'Régression de réseau neuronal : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module de régression de réseau neuronal dans le service Azure Machine Learning pour créer un modèle de régression à l’aide d’un algorithme de réseau neuronal personnalisable...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bc6a7505ab09e929e5add61eea687f871aedf242
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029309"
---
# <a name="neural-network-regression-module"></a>Module de régression de réseau neuronal

*Crée un modèle de régression à l’aide d’un algorithme de réseau neuronal*  
  
 Catégorie : L’apprentissage automatique / d’initialiser le modèle / régression
  
## <a name="module-overview"></a>Vue d’ensemble du module  

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle de régression à l’aide d’un algorithme de réseau neuronal personnalisable.
  
 Bien que les réseaux neuronaux sont largement connues pour une utilisation dans l’apprentissage profond et la modélisation des problèmes complexes telles que la reconnaissance d’image, ils sont facilement adaptables aux problèmes de régression. N’importe quelle classe de modèles statistiques peut être appelée réseau neuronal si elles utilisent les pondérations adaptatives et peuvent se rapprocher de fonctions non linéaires de leurs entrées. Par conséquent, régression de réseau neuronal est adaptée aux problèmes où un modèle de régression plus traditionnel ne rentrent pas une solution.
  
 Régression de réseau neuronal est une méthode d’apprentissage supervisé et nécessite donc un *balisés dataset*, qui inclut une colonne d’étiquette. Car un modèle de régression prédit une valeur numérique, la colonne d’étiquette doit être un type de données numériques.  
  
 Vous pouvez former le modèle en fournissant le modèle et le jeu de données avec balises en tant qu’entrée [former le modèle](./train-model.md). Le modèle formé peut ensuite être utilisé pour prédire des valeurs pour les nouveaux exemples d’entrée.  
  
## <a name="configure-neural-network-regression"></a>Configurer la régression de réseau neuronal 

Réseaux neuronaux peuvent être largement personnalisés. Cette section décrit comment créer un modèle à l’aide de deux méthodes :
  
+ [Créer un modèle de réseau neuronal à l’aide de l’architecture par défaut](#bkmk_DefaultArchitecture)  
  
    Si vous acceptez l’architecture de réseau neuronal par défaut, utilisez le **propriétés** volet pour définir les paramètres qui contrôlent le comportement du réseau neuronal, telles que le nombre de nœuds dans la couche masquée, le taux d’apprentissage et la normalisation.

    Commencez ici si vous ne connaissez pas les réseaux neuronaux. Le module prend en charge les nombreuses personnalisations, ainsi que le réglage de modèles, sans une connaissance approfondie des réseaux neuronaux. 

+ Définir une architecture personnalisée pour un réseau neuronal 

    Utilisez cette option si vous souhaitez ajouter des couches masquées supplémentaires ou personnaliser entièrement les fonctions d’architecture, ses connexions et l’activation de réseau.
    
    Cette option est recommandée si vous êtes déjà familiarisé avec les réseaux neuronaux. Vous utilisez le langage Net # pour définir l’architecture de réseau.  

##  <a name="bkmk_DefaultArchitecture"></a> Créer un modèle de réseau neuronal à l’aide de l’architecture par défaut
  
1.  Ajouter le **Neural Network Regression** module à votre expérience dans l’interface. Vous trouverez ce module sous **Machine Learning**, **initialiser**, dans le **régression** catégorie. 
  
2. Indiquer comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.  
  
    -   **L’unique paramètre**: Choisissez cette option si vous savez déjà comment vous souhaitez configurer le modèle.  

3.  Dans **masqué de spécification de la couche**, sélectionnez **entièrement connectée cas**. Cette option crée un modèle à l’aide de l’architecture de réseau neuronal par défaut, ayant pour un modèle de régression de réseau neuronal, ces attributs :  
  
    + Le réseau a exactement une seule couche cachée.
    + La couche de sortie est entièrement connectée à la couche masquée et la couche masquée est entièrement connectée à la couche d’entrée.
    + Le nombre de nœuds dans la couche masquée peut être défini par l’utilisateur (valeur par défaut est 100).  
  
    Étant donné que le nombre de nœuds dans la couche d’entrée est déterminé par le nombre de fonctionnalités dans les données d’apprentissage, dans un modèle de régression il peut y avoir qu’un seul nœud dans la couche de sortie.  
  
4. Pour **nombre de nœuds masqués**, tapez le nombre de nœuds masqués. La valeur par défaut est une seule couche cachée avec 100 nœuds. (Cette option n’est pas disponible si vous définissez une architecture personnalisée à l’aide de Net #.)
  
5.  Pour **taux d’apprentissage**, tapez une valeur qui définit l’opération effectuée à chaque itération, avant correction. Une valeur supérieure pour le taux d’apprentissage peut amener le modèle à converger plus rapidement, mais il peut dépasser les minima locaux.

6.  Pour **nombre d’itérations d’apprentissage**, spécifiez le nombre maximal de fois où l’algorithme traite les cas d’apprentissage.

7.  Pour ** l’apprentissage initial pondère le diamètre, tapez une valeur qui détermine les pondérations des nœuds au début du processus d’apprentissage.

8.  Pour **l’inertie**, tapez une valeur à appliquer au cours de formation en tant qu’un poids sur les nœuds des itérations précédentes.

10. Sélectionnez l’option **exemples de lecture aléatoire**, pour modifier l’ordre des cas entre les itérations. Si vous désélectionnez cette option, cas sont traités dans le même ordre chaque fois que vous exécutez l’expérience.
  
11. Pour **valeur initiale de nombre aléatoire**, vous pouvez éventuellement taper une valeur à utiliser comme valeur initiale. En spécifiant une valeur initiale valeur est utile lorsque vous souhaitez garantir la répétabilité entre les exécutions de la même expérience.
  
13. Connecter un jeu de données de formation et l’autre de la [modules de formation](module-reference.md): 
  
    -   Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, utilisez [former le modèle](./train-model.md).  
  
   
14. Exécutez l’expérience.  

## <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher un résumé des paramètres du modèle, ainsi que la fonctionnalité de poids tirées de formation et d’autres paramètres du réseau neuronal, avec le bouton droit la sortie de [former le modèle](./train-model.md), puis sélectionnez **visualiser**.  

+ Pour enregistrer un instantané du modèle formé, cliquez sur le **ajouté pour l’apprentissage modèle** de sortie et sélectionnez **enregistrer en tant que modèle formé**. Ce modèle n’est pas mis à jour lors des exécutions consécutives de la même expérience.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 