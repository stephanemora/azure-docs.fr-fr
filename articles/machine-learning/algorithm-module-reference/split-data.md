---
title: 'Fractionner les données : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module fractionner les données dans le service Azure Machine Learning pour diviser un jeu de données en deux ensembles distincts.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7395280ed9a2e9dcb94a081f0b3bf10a28da719
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029399"
---
# <a name="split-data-module"></a>Module fractionner les données

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour diviser un jeu de données en deux jeux distincts.

Ce module est particulièrement utile lorsque vous avez besoin de séparer les données dans l’apprentissage et jeux de test. Vous pouvez personnaliser la façon dont les données sont également divisées. Certaines options de prise en charge la randomisation du format de données ; d’autres sont adaptées pour un type de données ou un type de modèle.

## <a name="how-to-configure"></a>Comment configurer

> [!TIP]
> Vous devez effectuer les opérations suivantes avant de choisir le mode de fractionnement, de lire toutes les options pour déterminer le type de fractionnement.
> Si vous modifiez le mode de fractionnement, toutes les autres options pu être réinitialisées.

1. Ajouter le **fractionner les données** module à votre expérience dans l’interface. Vous trouverez ce module sous **Transformation des données**, dans le **Sample and Split** catégorie.

2. **Mode de fractionnement**: Choisissez un des modes suivants, selon le type de données que vous avez et la façon dont vous souhaitez diviser. Chaque mode de fractionnement a différentes options. Cliquez sur les rubriques suivantes pour obtenir des instructions détaillées et des exemples. 

    - **Fractionner les lignes**: Utilisez cette option si vous souhaitez simplement diviser les données en deux parties. Vous pouvez spécifier le pourcentage de données à placer dans chaque division, mais par défaut, les données sont divisées 50 / 50.

        Vous pouvez également rendre aléatoire la sélection de lignes dans chaque groupe et utiliser l’échantillonnage stratifié. Dans l’échantillonnage stratifié, vous devez sélectionner une seule colonne de données pour laquelle vous souhaitez que les valeurs sont répartis équitablement sur les jeux de données de deux résultats.  

    - **Fractionnement d’Expression régulière** choisissez cette option lorsque vous souhaitez diviser votre jeu de données en testant une colonne unique pour une valeur.

        Par exemple, si vous analysez les sentiments, vous pouvez vérifier la présence d’un nom de produit spécifique dans un champ de texte et puis diviser le jeu de données en lignes avec le nom du produit cible et celles sans.

    - **Fractionnement de l’Expression relative**:  Utilisez cette option lorsque vous souhaitez appliquer une condition à une colonne numérique. Le nombre peut être un champ de date/heure, un âge contenant de colonne ou montants en dollars ou même un pourcentage. Par exemple, vous souhaiterez peut-être diviser votre jeu de données selon le coût des éléments, groupes de personnes par tranches d’âge ou de données séparées par une date de calendrier.

### <a name="split-rows"></a>Fractionner les lignes
1.  Ajouter le [fractionner les données](./split-data.md) module à votre expérience dans l’interface et connectez le jeu de données que vous souhaitez fractionner.
  
2.  Pour **mode de fractionnement**, choisissez **fractionner les lignes**. 

3.  **Fraction de lignes dans le premier jeu de données de sortie**. Utilisez cette option pour déterminer combien de lignes sont placées dans la première sortie (gauche). Toutes les autres lignes passera dans la deuxième sortie (droite).

    Le rapport représente le pourcentage de lignes envoyées au premier jeu de données de sortie, vous devez taper un nombre décimal entre 0 et 1.
     
     Par exemple, si vous tapez 0,75 comme valeur, le jeu de données est fractionné à l’aide d’un taux de 75 : 25, avec 75 % de lignes envoyées au premier jeu de données de sortie et 25 % au deuxième jeu de données de sortie.
  
4. Sélectionnez le **fractionnement aléatoire** option si vous souhaitez rendre aléatoire de sélection de données dans les deux groupes. Il s’agit de l’option par défaut lors de la création de jeux de données apprentissage et de test.

5.  **Valeur de départ aléatoire**: Tapez une valeur entière non négative pour initialiser la séquence pseudo-aléatoire d’instances à utiliser. Cette valeur de départ par défaut est utilisée dans tous les modules qui génèrent des nombres aléatoires. 

     En spécifiant une valeur de départ rend les résultats généralement reproductibles. Si vous devez répéter les résultats d’une opération de fractionnement, vous devez spécifier une valeur initiale pour le Générateur de nombres aléatoires. Sinon, la valeur initiale aléatoire est définie par défaut sur 0, ce qui signifie que la valeur de départ initiale est obtenue à partir de l’horloge système. Par conséquent, la distribution des données peut être légèrement différente chaque fois que vous effectuez un fractionnement. 

6. **Fractionnement stratifié**: Définissez cette option sur **True** afin de garantir que les jeux de données de sortie de deux contiennent un échantillon représentatif des valeurs dans le *colonne de couches* ou *colonne clé de stratification*. 

    Avec un échantillonnage stratifié, les données sont divisées telles que chaque jeu de données de sortie obtient à peu près le même pourcentage de chaque valeur cible. Par exemple, vous pourrez vous assurer que votre apprentissage et jeux de test sont à peu près équilibrées en ce qui concerne le résultat ou eu égard ot une autre colonne, telles que le sexe.

7. Exécutez l’expérience.


## <a name="regular-expression-split"></a>Fractionnement de l’expression régulière

1.  Ajouter le [fractionner les données](./split-data.md) module à votre expérience et vous connecter en tant qu’entrée pour le jeu de données que vous souhaitez fractionner.  
  
2.  Pour **mode de fractionnement**, sélectionnez **fractionnement de l’expression régulière**.

3. Dans le **expression régulière** , tapez une expression régulière valide. 
  
   L’expression régulière doit respecter la syntaxe d’expression régulière Python.


4. Exécutez l’expérience.

    En fonction de l’expression régulière que vous fournissez, le jeu de données est divisé en deux ensembles de lignes : lignes avec des valeurs qui correspondent à l’expression et toutes les lignes restantes. 

## <a name="relative-expression-split"></a>Fractionnement de l’expression relative.

1. Ajouter le [fractionner les données](./split-data.md) module à votre expérience et vous connecter en tant qu’entrée pour le jeu de données que vous souhaitez fractionner.
  
2. Pour **mode de fractionnement**, sélectionnez **fractionnement de l’expression relative**.
  
3. Dans le **expression relationnelle** zone de texte, tapez une expression qui effectue une opération de comparaison, sur une seule colonne :


 - Colonne numérique :
    - La colonne contient des nombres de n’importe quel type de données numériques, y compris les types de données de date/heure.

    - L’expression peut faire référence à un nom de colonne.

    - Utilisez le caractère esperluette (&) pour l’opération AND et utilisez la barre verticale (|) pour l’opération.

    - Les opérateurs suivants sont pris en charge : `<`, `>`, `<=`, `>=`, `==`, `!=`

    - Vous ne pouvez pas regrouper les opérations à l’aide de `(` et `)`.

 - Colonne de chaîne : 
    - Les opérateurs suivants sont pris en charge : `==`, `!=`



4. Exécutez l’expérience.

    L’expression divise le jeu de données en deux ensembles de lignes : lignes avec des valeurs qui remplissent la condition et toutes les lignes restantes.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 