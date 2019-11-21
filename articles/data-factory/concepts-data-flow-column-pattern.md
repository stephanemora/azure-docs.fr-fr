---
title: Modèles de colonne dans le flux de données de mappage Azure Data Factory
description: Créer des modèles de transformation de données généralisés avec des modèles de colonne dans les flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789943"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Utilisation de modèles de colonne dans les flux de données de mappage

Plusieurs transformations de flux de données de mappage vous permettent de référencer des colonnes de modèles, en fonction des modèles plutôt que des noms de colonnes codés en dur. Cette correspondance est connue sous le nom de *modèles de colonne*. Vous pouvez définir des modèles pour faire correspondre des colonnes en fonction du nom, du type de données, du flux ou de la position au lieu de demander des noms de champ précis. Il existe deux scénarios dans lesquels les modèles de colonne sont utiles :

* Si les champs source entrants sont souvent modifiés, comme dans le cas de modifications de colonnes dans les fichiers texte ou les bases de données NoSQL. Ce scénario est appelé [dérive de schéma](concepts-data-flow-schema-drift.md).
* Si vous souhaitez effectuer une opération commune sur un grand groupe de colonnes. Par exemple, si vous voulez caster en double chaque colonne contenant « total » dans son nom de colonne.

Les modèles de colonne sont actuellement disponibles dans les transformations de colonne dérivée, d’agrégation, de sélection et de récepteur.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Modèles de colonne dans une colonne dérivée et une agrégation

Pour ajouter un modèle de colonne dans une colonne dérivée, ou l’onglet Agrégats d’une transformation d’agrégation, cliquez sur l’icône représentant un signe plus, à droite d’une colonne existante. Sélectionnez **Ajouter un modèle de colonne**. 

![modèles de colonne](media/data-flow/columnpattern.png "Modèles de colonne")

Utilisez le [Générateur d’expressions](concepts-data-flow-expression-builder.md) pour entrer la condition de correspondance. Créez une expression booléenne qui correspond aux colonnes basées sur le `name`, `type`, `stream` et `position` de la colonne. Le modèle affecte toute colonne, dérivée ou définie, où la condition retourne la valeur true.

Les deux zones d’expression sous la condition de correspondance indiquent les nouveaux noms et les valeurs des colonnes affectées. Utilisez `$$` pour référencer la valeur existante du champ correspondant. La zone d’expression de gauche définit le nom, et la zone d’expression de droite précise la valeur.

![modèles de colonne](media/data-flow/columnpattern2.png "Modèles de colonne")

Le modèle de colonne ci-dessus associe chaque colonne de type double et crée une colonne d’agrégation par correspondance. Le nom de la nouvelle colonne correspond au nom de la colonne correspondante, concaténée avec « _total ». La valeur de la nouvelle colonne est la somme agrégée et arrondie de la valeur double existante.

Pour vérifier que votre condition de correspondance est correcte, vous pouvez valider le schéma de sortie des colonnes définies dans l’onglet **Inspecter**, ou obtenir une capture instantanée des données dans l’onglet **Aperçu des données**. 

![modèles de colonne](media/data-flow/columnpattern3.png "Modèles de colonne")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mappage basé sur les règles dans la sélection et le récepteur

Lorsque vous mappez des colonnes dans les transformations de source et de sélection, vous pouvez ajouter au choix un mappage fixe ou des mappages basés sur les règles. Si vous connaissez le schéma de vos données et que vous vous attendez à ce que des colonnes spécifiques du jeu de données source correspondent toujours à des noms statiques spécifiques, utilisez le mappage fixe. Si vous utilisez des schémas flexibles, utilisez le mappage basé sur les règles pour créer une correspondance de modèle en fonction de `name`, `type`, `stream` et `position` des colonnes. Vous pouvez réaliser n’importe quelle combinaison de mappage fixe et basé sur des règles. 

Pour ajouter un mappage basé sur les règles, cliquez sur **Ajouter un mappage** et sélectionnez **Mappage basé sur les règles**.

![Mappage basé sur les règles](media/data-flow/rule2.png "Mappage basé sur des règles")

Dans la zone d’expression de gauche, entrez votre condition de correspondance booléenne. Dans la zone d’expression de droite, indiquez ce à quoi la colonne correspondante sera mappée. Utilisez `$$` pour référencer le nom existant du champ correspondant.

Si vous cliquez sur l’icône du chevron pointant vers le bas, vous pouvez spécifier une condition de mappage regex.

Cliquez sur l’icône des lunettes, à côté d’un mappage basé sur les règles, pour voir les colonnes définies qui sont mises en correspondance et ce à quoi elles sont mappées.

![Mappage basé sur les règles](media/data-flow/rule1.png "Mappage basé sur des règles")

Dans l’exemple ci-dessus, deux mappages basés sur les règles sont créés. La première règle prend toutes les colonnes qui ne sont pas nommées « Movie » et les mappe à leurs valeurs existantes. La deuxième utilise regex pour faire correspondre toutes les colonnes qui commencent par « Movie » et les mappe à la colonne « movieId ».

Si votre règle génère plusieurs mappages identiques, activez **Ignorer les entrées en double** ou **Ignorer les sorties en double** pour éviter les doublons.

## <a name="pattern-matching-expression-values"></a>Valeurs des expressions de critères spéciaux

* `$$` correspond au nom ou à la valeur de chaque correspondance au moment de l’exécution
* `name` représente le nom de chaque colonne entrante
* `type` représente le type de données de chaque colonne entrante
* `stream` représente le nom associé à chaque flux ou transformation dans votre flux
* `position` est la position ordinale des colonnes dans votre flux de données

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [langage d’expression](data-flow-expression-functions.md) du flux de données de mappage pour les transformations de données
* Utiliser des modèles de colonne dans la [transformation de récepteur](data-flow-sink.md) et la [transformation de sélection](data-flow-select.md) avec un mappage basé sur les règles
