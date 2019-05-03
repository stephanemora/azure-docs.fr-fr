---
title: 'Ajouter des colonnes : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module d’ajouter des colonnes dans le service Azure Machine Learning pour concaténer deux jeux de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029354"
---
# <a name="add-columns-module"></a>Ajouter un module de colonnes

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour concaténer deux jeux de données. Vous combinez toutes les colonnes à partir de deux jeux de données que vous spécifiez en tant qu’entrées pour créer un dataset unique. Si vous devez concaténer plus de deux jeux de données, utilisez plusieurs instances de **Add Columns**.



## <a name="how-to-configure-add-columns"></a>Comment configurer ajouter des colonnes
1. Ajouter le **Add Columns** module à votre expérience.

2. Connectez les deux jeux de données à concaténer. Si vous souhaitez combiner de plus de deux jeux de données, vous pouvez chaîner plusieurs combinaisons de **Add Columns**.

    - Il est possible de combiner les deux colonnes qui ont un nombre différent de lignes. Le jeu de données de sortie est complétée avec des valeurs manquantes pour chaque ligne dans la colonne source plus petits.

    - Vous ne pouvez pas choisir des colonnes individuelles à ajouter. Toutes les colonnes de chaque jeu de données sont concaténées lorsque vous utilisez **Add Columns**. Par conséquent, si vous souhaitez ajouter uniquement un sous-ensemble de colonnes, permet de sélectionner des colonnes dans le jeu de données pour créer un jeu de données avec les colonnes que vous souhaitez.

3. Exécutez l’expérience.

### <a name="results"></a>Résultats
Après l’exécution de l’expérience :

- Pour afficher les premières lignes du nouveau jeu de données, cliquez sur la sortie de **Add Columns** , puis sélectionnez visualiser.

Le nombre de colonnes dans le nouveau jeu de données est égal à la somme des colonnes de deux jeux de données d’entrée.

S’il existe deux colonnes portant le même nom dans les jeux de données d’entrée, un suffixe numérique est ajouté au nom de la colonne. Par exemple, s’il existe deux instances d’une colonne nommée TargetOutcome, la colonne de gauche est renommée TargetOutcome_1 et la colonne de droite serait renommée TargetOutcome_2.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 