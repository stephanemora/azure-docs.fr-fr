---
title: 'Supprimer les lignes en double : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module de supprimer les lignes en double dans le service Azure Machine Learning pour supprimer les doublons potentiels d’un jeu de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: dce90d911085c1f7330a2e0952bb9576c1d765fa
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029294"
---
# <a name="remove-duplicate-rows-module"></a>Supprimer le module de lignes en double

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Ce module permet de supprimer les doublons potentiels à partir d’un jeu de données.

Par exemple, supposons que vos données se présente comme suit et représente plusieurs enregistrements pour les patients. 

| PatientID | Initials| Sexe|Age|Admis|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Fév|
|4| F.M.| M| 23| Fév|
| | F.M.| M| 23| |
|5.| F.A.M.| M| 53| |
|6.| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Clairement, cet exemple comporte plusieurs colonnes avec des données potentiellement en double. Si elles sont en fait les doublons dépend de votre connaissance des données. 

+ Par exemple, vous connaissez peut-être que plusieurs patients ont le même nom. Vous n’aurait pas éliminer les doublons à l’aide des colonnes Nom, uniquement le **ID** colonne. De cette façon, seules les lignes avec des valeurs ID dupliquées sont filtrées, indépendamment de si les patients ont le même nom ou non.

+ Vous pouvez également décider autoriser les doublons dans le champ ID et utiliser une autre combinaison de fichiers pour rechercher des enregistrements uniques, telles que le prénom, nom, âge et sexe.  

Pour définir les critères déterminant si une ligne est en double ou non, vous spécifiez une seule colonne ou un ensemble de colonnes à utiliser comme **clés**. Deux lignes sont considérées comme des doublons uniquement lorsque les valeurs dans **tous les** colonnes clés sont égales. Si une ligne a une valeur manquante pour **clés**, ils ne seront plus utilisés des lignes en double. Par exemple, si l’âge et sexe sont définies comme clés d’au-dessus de table, la ligne 6 et 7 ne sont pas des doublons de lignes donnés sont-ils valeur manquante dans l’âge.

Lorsque vous exécutez le module, il crée un jeu de données finale et retourne un ensemble de lignes qui n’ont aucuns doublons dans le jeu de colonnes que vous avez spécifié.

> [!IMPORTANT]
> Le jeu de données source n’est pas modifié ; Ce module crée un nouveau jeu de données qui est filtrée pour exclure les doublons, selon les critères que vous spécifiez.

## <a name="how-to-use-remove-duplicate-rows"></a>Comment utiliser Remove Duplicate Rows

1. Ajouter le module à votre expérience. Vous pouvez trouver la **Remove Duplicate Rows** module sous **Transformation des données**, **Manipulation**.  

2. Connectez le jeu de données que vous souhaitez vérifier pour les lignes en double.

3. Dans le **propriétés** volet, sous **expression de filtre de sélection de colonne de clé**, cliquez sur **lancer le sélecteur de colonne**, pour sélectionner des colonnes pour identifier les doublons.

    Dans ce contexte, **clé** ne signifie pas un identificateur unique. Toutes les colonnes que vous sélectionnez à l’aide du sélecteur de colonne sont désignés comme **colonnes clés**. Toutes les colonnes non sélectionnées sont considérés comme des colonnes non-clés. La combinaison de colonnes que vous sélectionnez en tant que clés détermine l’unicité des enregistrements. (La considérer comme une instruction SQL qui utilise plusieurs jointures égalités.)

    Exemples :

    + « Je veux vous assurer que les ID sont uniques : » Choisissez uniquement la colonne ID.
    + « Je souhaite s’assurer que la combinaison du prénom, nom et ID est unique » : Sélectionnez les trois colonnes.

4. Utilisez le **conserver de première ligne dupliquée** case à cocher pour indiquer les lignes à retourner lorsque ceux-ci se trouvent :

    + Si sélectionné, la première ligne est retournée et d’autres ignorées. 
    + Si vous décochez cette option, la dernière ligne en double est conservée dans les résultats, et d’autres sont ignorées. 

5. Exécutez l’expérience.

6. Pour passer en revue les résultats, cliquez sur le module, sélectionnez **jeu de données de résultats**, puis cliquez sur **visualiser**. 

> [!TIP]
> Si les résultats sont difficiles à comprendre, ou si vous souhaitez ignorer certaines colonnes dans, vous pouvez supprimer des colonnes à l’aide de la [Select Columns in Dataset](./select-columns-in-dataset.md) module.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 