---
title: 'Normaliser les données : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module normaliser les données dans le service Azure Machine Learning pour transformer un jeu de données via *normalisation*...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 95069bafa94770511c7ee40e82068960298fd6c5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029444"
---
# <a name="normalize-data-module"></a>Normaliser le module de données

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour transformer un jeu de données via *normalisation*.

La normalisation est une technique souvent appliquée dans le cadre de la préparation des données pour l’apprentissage. L’objectif de la normalisation consiste à modifier les valeurs des colonnes numériques dans le jeu de données à utiliser une échelle commune, sans distorsion des différences dans les plages de valeurs ou de perte d’informations. Normalisation est également requise pour certains algorithmes modéliser les données correctement.

Par exemple, supposons que votre jeu de données d’entrée contienne une colonne avec les valeurs comprises entre 0 et 1 et une autre colonne avec des valeurs comprises entre 10 000 à 100 000. Le grand écart en le *mise à l’échelle* des nombres peut entraîner des problèmes lorsque vous essayez de combiner les valeurs en tant que fonctionnalités pendant la modélisation.

*Normalisation* permet d’éviter ces problèmes en créant de nouvelles valeurs qui gèrent la distribution générale et les rapports dans les données sources, tout en conservant des valeurs au sein d’une mise à l’échelle appliquée à toutes les colonnes numériques utilisés dans le modèle.

Ce module offre plusieurs options pour la transformation des données numériques :

- Vous pouvez modifier toutes les valeurs à 0-1 mise à l’échelle, ou transformer les valeurs en représentant les comme centile indique plutôt que des valeurs absolues.
- Vous pouvez appliquer la normalisation à une seule colonne ou à plusieurs colonnes dans le même jeu de données.
- Si vous devez répéter l’expérience, ou appliquer les mêmes étapes de normalisation à d’autres données, vous pouvez enregistrer les étapes comme une transformation de normalisation et s’appliquent aux autres jeux de données qui ont le même schéma.

> [!WARNING]
> Certains algorithmes nécessitent que les données soient normalisées avant l’apprentissage d’un modèle. D’autres algorithmes effectuent leur propre mise à l’échelle des données ou la normalisation. Par conséquent, lorsque vous choisissez un algorithme à utiliser dans la création d’un modèle prédictif d’apprentissage, veillez à consulter les données requises de l’algorithme avant d’appliquer la normalisation pour les données d’apprentissage.

##  <a name="configure-normalize-data"></a>Configurer normaliser les données

Vous pouvez appliquer qu’une seule méthode de normalisation à l’aide de ce module. Par conséquent, la même méthode de normalisation est appliquée à toutes les colonnes que vous sélectionnez. Pour utiliser les méthodes de normalisation différente, utilisez une deuxième instance de **normaliser les données**.

1. Ajouter le **normaliser les données** module à votre expérience. Vous pouvez rechercher le module dans Azure Machine Learning, sous **Transformation des données**, dans le **Scale and Reduce** catégorie.

2. Connectez un jeu de données qui contient au moins une colonne de tous les nombres.

3. Utilisez le sélecteur de colonne pour choisir les colonnes numériques à normaliser. Si vous ne choisissez pas des colonnes individuelles, par défaut **tous les** des colonnes de type numérique de l’entrée sont inclus, et le même processus de normalisation est appliqué à toutes les colonnes sélectionnées. 

    Cela peut entraîner des résultats inattendus si vous incluez des colonnes numériques qui ne doit pas être normalisés ! Vérifiez toujours les colonnes avec soin.

    Si aucune des colonnes numériques ne sont détectées, vérifiez les métadonnées de colonne pour vérifier que le type de données de la colonne est un type numérique pris en charge.

    > [!TIP]
    > Pour vous assurer que les colonnes d’un type spécifique sont fournies en tant qu’entrée, essayez d’utiliser le [Select Columns in Dataset](./select-columns-in-dataset.md) module avant **normaliser les données**.

4. **Utilisez 0 pour les colonnes constante lorsqu’elle est activée**:  Sélectionnez cette option lors de toute colonne numérique contient une valeur unique qui ne changent pas. Cela garantit que ces colonnes ne sont pas utilisées dans les opérations de normalisation.

5. À partir de la **méthode de Transformation** liste déroulante, sélectionnez une seule fonction mathématique à appliquer à toutes les colonnes sélectionnées. 
  
    - **Zscore**: Convertit toutes les valeurs en un z-score.
    
      Les valeurs dans la colonne sont transformées à l’aide de la formule suivante :  
  
      ![normalisation à l’aide de z&#45;scores](media/module/aml-normalization-z-score.png)
  
      L’écart moyen et standard sont calculés séparément pour chaque colonne. Écart est utilisé.
  
    - **MinMax**: La normalisation min-max remet linéairement à l’échelle toutes les caractéristiques de l’intervalle [0,1].
    
      La remise à l’intervalle [0,1] est effectuée en décalant les valeurs de chaque fonctionnalité afin que la valeur minimale est 0 et en divisant par la nouvelle valeur maximale (qui est la différence entre les valeurs maximales et minimales d’origine).
      
      Les valeurs dans la colonne sont transformées à l’aide de la formule suivante :  
  
      ![normalisation à l’aide de la min&#45;max fonction](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logistique**: Les valeurs dans la colonne sont transformées à l’aide de la formule suivante :

      ![formule pour normalisation par fonction logistique](media/module/aml-normalization-logistic.png "AML_normalization-logistique")  
  
    - **LogNormal**: Cette option convertit toutes les valeurs en une échelle lognormale.
  
      Les valeurs dans la colonne sont transformées à l’aide de la formule suivante :
  
      ![formule journal&#45;distribution normale](media/module/aml-normalization-lognormal.png "suivant une loi lognormale AML_normalization")
    
      Ici μg et σ sont les paramètres de la distribution, calculés de façon empirique à partir des données en tant qu’estimations de vraisemblance maximale, pour chaque colonne séparément.  
  
    - **TanH**: Toutes les valeurs sont converties en une tangente hyperbolique.
    
      Les valeurs dans la colonne sont transformées à l’aide de la formule suivante :
    
      ![normalisation à l’aide de la fonction tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Exécutez l’expérience, ou double-cliquez sur le **normaliser les données** module et sélectionnez **exécuter les éléments sélectionnés**. 

## <a name="results"></a>Résultats

Le **normaliser les données** module génère deux sorties :

- Pour afficher les valeurs transformées, cliquez sur le module, sélectionnez **transformé dataset**, puis cliquez sur **visualiser**.

    Par défaut, les valeurs sont transformés en place. Si vous souhaitez comparer les valeurs transformées avec les valeurs d’origine, utilisez le [Add Columns](./add-columns.md) module recombiner les jeux de données et d’afficher les colonnes côte à côte.

- Pour enregistrer la transformation afin que vous pouvez appliquer la même méthode de normalisation à un autre jeu de données similaire, cliquez sur le module, sélectionnez **fonction de Transformation**, puis cliquez sur **enregistrer en tant que transformation**.

    Vous pouvez ensuite charger les transformations enregistrées à partir de la **transforme** groupe du volet de navigation gauche et l’appliquer à un jeu de données avec le même schéma à l’aide de [. / Apply Transformation](apply-transformation.md).  


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 