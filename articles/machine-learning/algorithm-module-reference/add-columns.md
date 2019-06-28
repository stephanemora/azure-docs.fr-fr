---
title: 'Ajouter des colonnes : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Ajouter des colonnes dans Azure Machine Learning service pour concaténer deux jeux de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029354"
---
# <a name="add-columns-module"></a>Module Ajouter des colonnes

Cet article décrit un module de l’interface visuelle (préversion) pour le service Azure Machine Learning.

Utilisez ce module pour concaténer 2 jeux de données. Vous combinez toutes les colonnes à partir de 2 jeux de données que vous spécifiez en tant qu’entrées pour créer un jeu de données unique. Si vous devez concaténer plus de 2 jeux de données, utilisez plusieurs instances du module **Ajouter des colonnes**.



## <a name="how-to-configure-add-columns"></a>Comment configurer Ajouter des colonnes
1. Ajoutez le module **Ajouter des colonnes** à votre expérience.

2. Connectez les 2 jeux de données à concaténer. Si vous souhaitez combiner plus de 2 jeux de données, vous pouvez chaîner plusieurs combinaisons du module **Ajouter des colonnes**.

    - Vous pouvez combiner deux colonnes qui ont un nombre différent de lignes. Le jeu de données de sortie est complété avec les valeurs manquantes pour chaque ligne de la plus petite colonne source.

    - Vous ne pouvez pas choisir des colonnes individuelles à ajouter. Toutes les colonnes de chaque jeu de données sont concaténées lorsque vous utilisez le module **Ajouter des colonnes**. Par conséquent, si vous souhaitez ajouter uniquement un sous-ensemble de colonnes, utilisez Sélectionner des colonnes dans le jeu de données pour créer un jeu de données contenant les colonnes de votre choix.

3. Exécutez l’expérience.

### <a name="results"></a>Résultats
Après l’exécution de l’expérience :

- Pour afficher les premières lignes du nouveau jeu de données, cliquez sur la sortie du module **Ajouter des colonnes**, puis sélectionnez Visualiser.

Le nombre de colonnes du nouveau jeu de données est égal à la somme des colonnes des 2 jeux de données d’entrée.

Si 2 colonnes portent le même nom dans les jeux de données d’entrée, le module ajoute un suffixe numérique au nom de ces colonnes. Par exemple, s’il existe deux instances d’une colonne nommée RésultatCible, la colonne de gauche est renommée RésultatCible_1, tandis que la colonne de droite est renommée RésultatCible_2.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [modules disponibles](module-reference.md) pour Azure Machine Learning service. 