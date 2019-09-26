---
title: Modèles de colonne Data Flow de mappage Azure Data Factory
description: Créer des modèles de transformation de données généralisés à l’aide de modèles de colonnes Azure Data Factory dans des flux de données de mappage
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 4df831c1329ab13f19e6ecf979e404d4a90e5f72
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219768"
---
# <a name="mapping-data-flows-column-patterns"></a>Modèles de colonne de flux de données de mappage

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Plusieurs transformations de flux de données Azure Data Factory supportent le concept de « Modèles de colonne » afin que vous puissiez créer des colonnes modèles basées sur des modèles et non pas sur des noms de colonne codés en dur. Vous pouvez utiliser cette fonctionnalité dans le Générateur d’expressions pour définir des modèles afin de faire correspondre les colonnes dans le cadre de la transformation au lieu d’exiger des noms de champs spécifiques exacts. Les modèles sont utiles si les champs source entrants sont souvent modifiés, notamment dans le cas de modifications de colonnes dans les fichiers texte ou les bases de données NoSQL. Cette condition est parfois désignée sous le nom de « dérive de schéma ».

Cette gestion de « schéma flexible » se trouve actuellement dans la colonne dérivée et les transformations d’agrégation, ainsi que les transformations de sélection et de récepteur comme « mappage basé sur des règles ».

![modèles de colonne](media/data-flow/columnpattern2.png "Modèles de colonne")

## <a name="column-patterns"></a>Modèles de colonne
Les modèles de colonne sont utiles pour traiter à la fois les scénarios de dérive de schéma et les scénarios généraux. Cela est pratique lorsque vous n’êtes pas en mesure de connaître précisément le nom de chaque colonne. Vous pouvez faire correspondre un modèle avec le nom de colonne et le type de données de la colonne et construire une expression pour la transformation qui exécutera cette opération sur n’importe quel champ du flux de données qui correspond à vos modèles `name` & `type`.

Lorsque vous ajoutez une expression à une transformation qui accepte des modèles, choisissez « Ajouter un modèle de colonne ». Modèles de colonne permet de faire correspondre les modèles des colonnes de dérive de schéma.

Lors de la construction de modèles de colonnes de modèle, utilisez `$$` dans l’expression pour représenter une référence à chaque champ correspondant du flux de données entrant.

Si vous choisissez d’utiliser l’une des fonctions d’expression régulière du Générateur d’expressions, vous pouvez ensuite utiliser $1, $2, $3... pour référencer les sous-modèles correspondant à votre expression régulière.

L’utilisation de SUM avec une série de champs entrants constitue un exemple de scénario de modèle de colonne. Les calculs agrégés des calculs de SUM résident dans la transformation agrégée. Vous pouvez ensuite utiliser SUM sur chaque correspondance des types de champs qui correspondent à « integer », puis utiliser $$ pour référencer chaque correspondance dans votre expression.

## <a name="match-columns"></a>Colonnes de correspondance
![types de modèles de colonnes](media/data-flow/pattern2.png "Types de modèles")

Pour créer des modèles basés sur des colonnes, vous pouvez faire correspondre le nom de colonne, le type, le flux ou la position et utiliser n’importe quelle combinaison de ceux-ci avec les fonctions d’expression et les expressions régulières.

![position de colonne](media/data-flow/position.png "Position de colonne")

## <a name="rule-based-mapping"></a>Mappage basé sur des règles
Lorsque vous mappez des colonnes dans les transformations Source et Sélection, vous avez la possibilité de choisir « Mappage fixe » ou « Mappage basé sur les règles ». Lorsque vous connaissez le schéma de vos données et que vous vous attendez à des colonnes spécifiques du jeu de données source qui correspondent toujours à des noms statiques spécifiques, vous pouvez utiliser le mappage fixe. Toutefois, lorsque vous travaillez avec des schémas flexibles, utilisez le mappage basé sur les règles. Vous serez en mesure de créer une correspondance de modèle à l’aide des règles décrites ci-dessus.

![mappage basé sur des règles](media/data-flow/rule2.png "Mappage basé sur des règles")

Construisez vos règles à l’aide du générateur d’expressions. Vos expressions retournent une valeur booléenne pour faire correspondre les colonnes (true) ou les exclure (false).

## <a name="pattern-matching-special-columns"></a>Critères spéciaux pour les colonnes spéciales

* `$$` désigne le nom de chaque correspondance au moment de la conception en mode débogage et au moment de l’exécution
* `name` représente le nom de chaque colonne entrante
* `type` représente le type de données de chaque colonne entrante
* `stream` représente le nom associé à chaque flux ou transformation dans votre flux
* `position` est la position ordinale des colonnes dans votre flux de données

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le flux de données de mappage ADF [langage d’expression](https://aka.ms/dataflowexpressions) pour les transformations de données
* Utiliser des modèles de colonne dans la [Transformation du récepteur](data-flow-sink.md) et [Transformation de sélection](data-flow-select.md) avec un mappage basé sur des règles
