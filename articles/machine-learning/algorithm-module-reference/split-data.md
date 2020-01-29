---
title: 'Fractionner les données : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Fractionner les données dans Azure Machine Learning pour diviser un jeu de données en deux jeux distincts.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b707971f0b150bce63b4dd0ffa4e55dc3b3d6bac
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313955"
---
# <a name="split-data-module"></a>Module Fractionner les données

Cet article décrit un module dans le concepteur Azure Machine Learning.

Utilisez ce module pour diviser un jeu de données en deux jeux distincts.

Ce module est particulièrement utile quand vous devez séparer des données en jeux de formation et de test. Vous pouvez également personnaliser la façon dont les données sont divisées. Certaines options prennent en charge la randomisation des données, d'autres sont adaptées à un certain type de données ou type de modèle.

## <a name="how-to-configure"></a>Comment configurer

> [!TIP]
> Avant de choisir le mode de fractionnement, prenez connaissance de toutes les options afin de déterminer le type de fractionnement adapté à vos besoins.
> Si vous modifiez le mode de fractionnement, toutes les autres options peuvent être réinitialisées.

1. Ajoutez le module **Fractionner les données** à votre pipeline dans le concepteur. Ce module est disponible sous **Transformation des données**, dans la catégorie **Échantillonner et fractionner**.

2. **Mode de fractionnement** : Choisissez l'un des modes suivants, selon le type de données dont vous disposez et la manière dont vous souhaitez les diviser. Chaque mode de fractionnement s'accompagne de différentes options. Cliquez sur les rubriques suivantes pour obtenir des instructions détaillées et des exemples. 

    - **Fractionner les lignes** : Utilisez cette option pour diviser simplement les données en deux parties. Vous pouvez spécifier le pourcentage de données de chaque fractionnement, mais par défaut, les données sont divisées à 50/50.

        Vous pouvez également rendre aléatoire la sélection des lignes de chaque groupe et utiliser l’échantillonnage stratifié. Dans l’échantillonnage stratifié, vous devez sélectionner une seule colonne de données pour laquelle vous souhaitez que les valeurs soient équitablement réparties entre les deux jeux de données de résultats.  

    - **Fractionnement Expression régulière** Choisissez cette option si vous souhaitez diviser votre jeu de données en testant une seule colonne pour une valeur.

        Par exemple, si vous analysez des sentiments, vous pouvez vérifier la présence d’un nom de produit spécifique dans un champ de texte, puis diviser le jeu de données en lignes avec et sans le nom du produit cible.

    - **Fractionnement Expression relative** :  Utilisez cette option si vous souhaitez appliquer une condition à une colonne numérique. Le nombre peut correspondre à un champ de date/heure, une colonne contenant un âge ou des montants en dollars, voire un pourcentage. Par exemple, vous souhaiterez peut-être diviser votre jeu de données en fonction du prix des éléments, grouper des personnes par tranches d'âge ou séparer les données par date.

### <a name="split-rows"></a>Fractionner les lignes

1.  Ajoutez le module [Fractionner les données](./split-data.md) à votre pipeline dans le concepteur et connectez le jeu de données que vous souhaitez fractionner.
  
2.  Pour **Mode de fractionnement**, sélectionnez **Lignes fractionnées**. 

3.  **Fraction de lignes dans le premier jeu de données de sortie**. Utilisez cette option pour déterminer le nombre de lignes de la première sortie (à gauche). Toutes les autres lignes rejoindront la deuxième sortie (à droite).

    Le rapport représente le pourcentage de lignes envoyées au premier jeu de données de sortie, et vous devez entrer un nombre décimal compris entre 0 et 1.
     
     Par exemple, si vous entrez 0,75 en tant que valeur, le jeu de données est fractionné selon un taux de 75:25, 75 % des lignes rejoignent le premier jeu de données de sortie et 25 %, le second.
  
4. Sélectionnez l'option **Fractionnement aléatoire** si vous souhaitez rendre aléatoire la sélection de données dans les deux groupes. Cette option est recommandée lors de la création de jeux de données de formation et de test.

5.  **Valeur de départ aléatoire** : Entrez une valeur entière positive pour initialiser la séquence pseudoaléatoire d’instances à utiliser. Cette valeur de départ par défaut est utilisée dans tous les modules qui génèrent des nombres aléatoires. 

     Spécifier une valeur de départ rend les résultats généralement reproductibles. Pour répéter les résultats d’une opération de fractionnement, vous devez spécifier une valeur de départ pour le générateur de nombres aléatoires. Sinon, la valeur de départ aléatoire est définie sur 0 par défaut, ce qui signifie qu’elle est obtenue à partir de l’horloge système. Dès lors, la répartition des données peut légèrement varier chaque fois que vous effectuez un fractionnement. 

6. **Fractionnement stratifié** : Définissez cette option sur **True** pour vous assurer que les deux jeux de données de sortie contiennent un échantillon représentatif des valeurs dans la *colonne de strate* ou la *colonne clé de stratification*. 

    Avec un échantillonnage stratifié, les données sont divisées de manière à ce que chaque jeu de données de sortie obtienne approximativement le même pourcentage de chaque valeur cible. Par exemple, vous souhaiterez peut-être que vos jeux de formation et de test soient globalement équilibrés en termes de résultat ou autres, comme le sexe.

7. Exécuter le pipeline.


## <a name="regular-expression-split"></a>Fractionnement d’expression régulière

1.  Ajoutez le module [Fractionner les données](./split-data.md) à votre pipeline et connectez-le en tant qu’entrée au jeu de données que vous souhaitez fractionner.  
  
2.  Pour **Mode de fractionnement**, sélectionnez **Fractionnement Expression régulière**.

3. Dans la zone **Expression régulière**, entrez une expression régulière valide. 
  
   L’expression régulière doit respecter la syntaxe d’expression régulière Python.


4. Exécuter le pipeline.

    En fonction de l’expression régulière entrée, le jeu de données est divisé en deux ensembles de lignes : les lignes avec des valeurs correspondant à l’expression et les autres. 

## <a name="relative-expression-split"></a>Fractionnement Expression relative

1. Ajoutez le module [Fractionner les données](./split-data.md) à votre pipeline et connectez-le en tant qu’entrée au jeu de données que vous souhaitez fractionner.
  
2. Pour **Mode de fractionnement**, sélectionnez **Fractionnement Expression relative**.
  
3. Dans la zone de texte **Expression relationnelle**, entrez une expression permettant une opération de comparaison, sur une même colonne :


 - Colonne numérique :
    - La colonne contient des nombres de n’importe quel type de données numérique, y compris les types de données date/heure.

    - L’expression peut faire référence à un nom de colonne.

    - Utilisez une esperluette (&) pour l’opération AND et une barre verticale (|) pour l’opération OR.

    - Les opérateurs suivants sont pris en charge : `<`, `>`, `<=`, `>=`, `==`, `!=`

    - Vous ne pouvez pas regrouper les opérations en utilisant `(` et `)`.

 - Colonne de chaîne : 
    - Les opérateurs suivants sont pris en charge : `==`, `!=`



4. Exécuter le pipeline.

    L’expression divise le jeu de données en deux ensembles de lignes : les lignes avec des valeurs correspondant à la condition, et les autres.

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 