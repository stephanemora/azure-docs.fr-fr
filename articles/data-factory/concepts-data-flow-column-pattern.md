---
title: Modèles de colonne dans le flux de données de mappage Azure Data Factory
description: Créer des modèles de transformation de données généralisés avec des modèles de colonne dans les flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 5e1c0eb1c4f1fc74e56db8f83f1bab6406ec5306
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415549"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Utilisation de modèles de colonne dans les flux de données de mappage

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

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

Lorsque vous mappez des colonnes dans les transformations de source et de sélection, vous pouvez ajouter au choix un mappage fixe ou des mappages basés sur les règles. Correspondance basée sur les valeurs `name`, `type`, `stream` et `position` de colonnes. Vous pouvez réaliser n’importe quelle combinaison de mappage fixe et basé sur des règles. Par défaut, toutes les projections contenant plus de 50 colonnes ont comme valeur par défaut un mappage basé sur des règles qui correspond à chaque colonne et génère le nom entré. 

Pour ajouter un mappage basé sur les règles, cliquez sur **Ajouter un mappage** et sélectionnez **Mappage basé sur les règles**.

![Mappage basé sur les règles](media/data-flow/rule2.png "Mappage basé sur des règles")

Chaque mappage basé sur des règles nécessite deux entrées : la condition à rechercher et le nom de chaque colonne mappée. Les deux valeurs sont entrées par l’intermédiaire du [générateur d’expressions](concepts-data-flow-expression-builder.md). Dans la zone d’expression de gauche, entrez votre condition de correspondance booléenne. Dans la zone d’expression de droite, indiquez ce à quoi la colonne correspondante sera mappée.

![Mappage basé sur les règles](media/data-flow/rule-based-mapping.png "Mappage basé sur des règles")

Utilisez la syntaxe `$$` pour référencer le nom d’entrée d’une colonne correspondante. Prenons l’image ci-dessus comme exemple et imaginons qu’un utilisateur souhaite faire correspondre toutes les colonnes de chaîne dont les noms ont moins de six caractères. Si une colonne entrante a été nommée `test`, l’expression `$$ + '_short'` renomme la colonne en `test_short`. Si c’est le seul mappage qui existe, toutes les colonnes qui ne répondent pas à la condition sont supprimées des données générées.

Les modèles correspondent à la fois aux colonnes dérivées et définies. Pour voir les colonnes définies qui sont mappées par une règle, cliquez sur l’icône de lunettes à côté de la règle. Vérifiez votre sortie à l’aide de l’aperçu des données.

### <a name="regex-mapping"></a>Mappage Regex

Si vous cliquez sur l’icône du chevron pointant vers le bas, vous pouvez spécifier une condition de mappage regex. Une condition de mappage regex correspond à tous les noms de colonne qui correspondent à la condition regex spécifiée. Elle peut être utilisée en association avec les mappages standard basés sur des règles.

![Mappage basé sur les règles](media/data-flow/regex-matching.png "Mappage basé sur des règles")

L’exemple ci-dessus correspond au modèle regex `(r)` ou à tout nom de colonne qui contient un r minuscule. À l’instar du mappage standard basé sur des règles, toutes les colonnes correspondantes sont modifiées par la condition à droite à l’aide de la syntaxe `$$`.

### <a name="rule-based-hierarchies"></a>Hiérarchies basées sur des règles

Si votre projection définie a une hiérarchie, vous pouvez utiliser le mappage basé sur des règles pour mapper les sous-colonnes de hiérarchies. Spécifiez une condition de correspondance et la colonne complexe dont vous souhaitez mapper les sous-colonnes. Chaque sous-colonne correspondante est générée à l’aide de la règle « Nommer » spécifiée à droite.

![Mappage basé sur les règles](media/data-flow/rule-based-hierarchy.png "Mappage basé sur des règles")

L’exemple ci-dessus correspond à toutes les sous-colonnes de la colonne complexe `a`. `a` contient deux sous-colonnes `b` et `c`. Le schéma de sortie inclut deux colonnes `b` et `c`, car la condition « Nommer » est `$$`.

## <a name="pattern-matching-expression-values"></a>Valeurs des expressions de critères spéciaux

* `$$` correspond au nom ou à la valeur de chaque correspondance au moment de l’exécution
* `name` représente le nom de chaque colonne entrante
* `type` représente le type de données de chaque colonne entrante
* `stream` représente le nom associé à chaque flux ou transformation dans votre flux
* `position` est la position ordinale des colonnes dans votre flux de données

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [langage d’expression](data-flow-expression-functions.md) du flux de données de mappage pour les transformations de données
* Utiliser des modèles de colonne dans la [transformation de récepteur](data-flow-sink.md) et la [transformation de sélection](data-flow-select.md) avec un mappage basé sur les règles
