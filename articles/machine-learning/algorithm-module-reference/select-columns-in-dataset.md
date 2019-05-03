---
title: 'Sélectionnez les colonnes dans le jeu de données : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser les sélectionner des colonnes dans le module de jeu de données du service Azure Machine Learning pour choisir un sous-ensemble de colonnes à utiliser dans les opérations en aval.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e7407f62bd3401411d56076b298bd8cd134ece62
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028094"
---
# <a name="select-columns-in-dataset-module"></a>Sélectionner des colonnes dans le module de jeu de données

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour choisir un sous-ensemble de colonnes à utiliser dans les opérations en aval. Le module ne supprime pas physiquement les colonnes de jeu de données source ; au lieu de cela, il crée un sous-ensemble de colonnes, à l’instar d’une base de données *vue* ou *projection*.

Ce module est utile lorsque vous avez besoin limiter les colonnes disponibles pour une opération en aval, ou si vous souhaitez réduire la taille du jeu de données en supprimant les colonnes inutiles.

Les colonnes dans le jeu de données sont générées dans le même ordre que les données d’origine, même si vous les spécifiez dans un ordre différent.

## <a name="how-to-use"></a>Utilisation

Ce module n’a aucun paramètre. Vous utilisez le sélecteur de colonne pour choisir les colonnes à inclure ou exclure.

### <a name="choose-columns-by-name"></a>Choisir les colonnes par nom

Il existe plusieurs options dans le module de choix de colonnes par nom : 

+ Filtrer et rechercher

    Cliquez sur le **par nom** option.

    Si vous avez connecté un jeu de données est déjà remplie, une liste des colonnes disponibles doit apparaître. Si aucune colonne n’apparaît pas, vous devrez peut-être exécuter les modules en amont pour afficher la liste des colonnes.

    Pour filtrer la liste, tapez dans la zone de recherche. Par exemple, si vous tapez la lettre `w` dans la zone de recherche, la liste est filtrée pour afficher les noms des colonnes qui contiennent la lettre `w`.

    Sélectionnez les colonnes et cliquez sur le bouton de flèche droite pour déplacer les colonnes sélectionnées à la liste dans le volet de droite.

    + Pour sélectionner une plage continue de noms de colonnes, appuyez sur **MAJ + clic**.
    + Pour ajouter des colonnes individuelles à la sélection, appuyez sur **Ctrl + clic**.

    Cliquez sur la coche pour enregistrer et fermer.

+ Utilisez des noms en combinaison avec d’autres règles

    Cliquez sur le **avec règles** option.
    
    Choisissez une règle, par exemple affichage des colonnes d’un type de données spécifique.

    Ensuite, cliquez sur les colonnes individuelles de ce type par nom, pour les ajouter à la liste de sélection.

+ Tapez ou collez une liste séparée par des virgules de noms de colonnes

    Si votre jeu de données est large, il peut être plus facile à utiliser des index ou généré des listes de noms, plutôt que des colonnes en sélectionnant individuellement. En supposant que vous avez préparé la liste à l’avance :

    1. Cliquez sur le **avec règles** option. 
    2. Sélectionnez **aucune colonne**, sélectionnez **Include**, puis cliquez dans la zone de texte avec le point d’exclamation rouge. 
    3. Collez ou tapez une liste séparée par des virgules de noms de colonnes validé précédemment. Vous ne pouvez pas enregistrer le module si une colonne a un nom non valide, veillez à vérifier les noms au préalable.
    
    Vous pouvez également utiliser cette méthode pour spécifier une liste de colonnes à l’aide de leurs valeurs d’index. 

### <a name="choose-by-type"></a>Choisissez par type

Si vous utilisez le **avec règles** option, vous pouvez appliquer plusieurs conditions sur les colonnes sélectionnées. Par exemple, vous devrez peut-être obtenir uniquement les colonnes de fonctionnalités d’un type de données numériques.

Le **commencer par** option détermine votre point de départ et qu’il est importante pour comprendre les résultats. 

+ Si vous sélectionnez le **toutes les colonnes** option, toutes les colonnes sont ajoutées à la liste. Ensuite, vous devez utiliser le **exclure** option *supprimer* colonnes qui répondent à certaines conditions. 

    Par exemple, vous pourrez commencer par toutes les colonnes et puis supprimer des colonnes par nom ou par type.

+ Si vous sélectionnez le **non colonnes** option, la liste des colonnes démarre est vide. Vous spécifiez ensuite les conditions à *ajouter* colonnes à la liste. 

    Si vous appliquez plusieurs règles, chaque condition est **additifs**. Par exemple, vous commencez par aucune colonne, puis ajoutez une règle pour obtenir toutes les colonnes numériques. Dans le jeu de données de prix Automobile, qui entraîne 16 colonnes. Ensuite, cliquez sur le **+** vous connecter pour ajouter une nouvelle condition, puis sélectionnez **inclure toutes les fonctionnalités**. Le jeu de données obtenu inclut toutes les colonnes numériques, ainsi que toutes les colonnes de fonctionnalité, y compris certaines colonnes de fonctionnalité de chaîne.

### <a name="choose-by-column-index"></a>Choisissez par index de colonne

L’index de colonne fait référence à l’ordre de la colonne dans le jeu de données d’origine.

+ Les colonnes sont numérotées séquentiellement en commençant à 1.  
+ Pour obtenir une plage de colonnes, utilisez un trait d’union. 
+ Spécifications de durée indéterminées comme `1-` ou `-3` ne sont pas autorisés.
+ Valeurs de l’index en double (ou noms de colonnes) ne sont pas autorisées et peuvent entraîner une erreur.

Par exemple, en supposant que votre jeu de données a au moins huit colonnes, vous a pu coller dans un des exemples suivants pour retourner plusieurs colonnes non contiguës : 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

le dernier exemple n’entraîne pas une erreur ; Toutefois, elle retourne une instance unique de la colonne `4`.



### <a name="change-order-of-columns"></a>Modifier l’ordre des colonnes

L’option **autorise pas les doublons et conserver l’ordre des colonnes dans la sélection** commence par une liste vide et ajoute les colonnes que vous spécifiez par nom ou par index. Contrairement à d’autres options, qui retournent toujours des colonnes dans « ordre naturel », cette option affiche les colonnes dans l’ordre que vous nommez ou les répertorier. 

Par exemple, dans un jeu de données avec les colonnes Col1, Col2, Col3 et Col4, vous pouvez inverser l’ordre des colonnes et omettre la colonne 2, en spécifiant une des listes suivantes :

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 