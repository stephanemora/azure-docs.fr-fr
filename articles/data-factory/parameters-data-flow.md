---
title: Paramétrage de flux de données de mappage
description: Découvrez comment paramétrer un flux de données de mappage à partir de pipelines Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c589cfeab7a812e09ce7f7620e93b72bd362859a
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746149"
---
# <a name="parameterizing-mapping-data-flows"></a>Paramétrage de flux de données de mappage

Les flux de données de mappage dans Azure Data Factory prennent en charge l’utilisation de paramètres. Vous pouvez définir des paramètres à l’intérieur de votre définition de flux de données, utilisable ensuite dans toutes vos expressions. Les valeurs de paramètre peuvent être définies par le pipeline d’appel par le biais de l’activité Exécuter une activité de flux de données. Vous disposez de trois options pour définir les valeurs dans les expressions d’activité de flux de données :

* Utiliser le langage d’expression de flux de contrôle de pipeline pour définir une valeur dynamique
* Utiliser le langage d’expression de flux de données pour définir une valeur dynamique
* Utiliser un langage d’expression pour définir une valeur littérale statique

Utilisez cette fonctionnalité pour rendre vos flux de données polyvalents, flexibles et réutilisables. Vous pouvez définir les paramètres de flux de données et les expressions à l’aide de ces paramètres.

> [!NOTE]
> Pour utiliser des expressions de flux de contrôle de pipeline, votre paramètre de flux de données doit être de type chaîne.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Créer des paramètres dans un flux de données de mappage

Pour ajouter des paramètres à votre flux de données, cliquez sur la partie vide du canevas de celui-ci afin d’accéder aux propriétés générales. Dans le volet des paramètres, vous voyez un onglet nommé **Paramètre**. Sélectionnez **Nouveau** pour générer un nouveau paramètre. Pour chaque paramètre, vous devez attribuer un nom, sélectionner un type et éventuellement définir une valeur par défaut.

![Créer des paramètres de flux de données](media/data-flow/create-params.png "Créer des paramètres de flux de données")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Utiliser des paramètres dans un flux de données de mappage 

Des paramètres peuvent être référencés dans toute expression de flux de données. Les paramètres commencent par $ et sont immuables. Vous trouverez la liste des paramètres disponibles dans le Générateur d’expressions, sous l’onglet **Paramètres**.

![Expression de paramètre de flux de données](media/data-flow/parameter-expression.png "Expression de paramètre de flux de données")

Vous pouvez ajouter rapidement des paramètres supplémentaires en sélectionnant **Nouveau paramètre** et en spécifiant le nom et le type.

![Expression de paramètre de flux de données](media/data-flow/new-parameter-expression.png "Expression de paramètre de flux de données")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Transmission d’un nom de colonne en tant que paramètre

Un modèle courant consiste à passer un nom de colonne en tant que valeur de paramètre. Pour référencer la colonne associée au paramètre, utilisez la fonction `byName()`. Veillez à effectuer un cast de la colonne vers son type approprié à l’aide d’une fonction de conversion telle que `toString()`.

Par exemple, si vous souhaitez mapper une colonne de chaîne basée sur un paramètre `columnName`, vous pouvez ajouter une transformation de colonne dérivée égale à `toString(byName($columnName))`.

![Transmission d’un nom de colonne en tant que paramètre](media/data-flow/parameterize-column-name.png "Transmission d’un nom de colonne en tant que paramètre")

## <a name="assign-parameter-values-from-a-pipeline"></a>Assigner des valeurs de paramètre à partir d’un pipeline

Une fois que vous avez créé votre flux de données avec des paramètres, vous pouvez l’exécuter à partir d’un pipeline avec l’activité Exécuter un flux de données. Après avoir ajouté l’activité à votre canevas de pipeline, les paramètres de flux de données disponibles vous sont présentés dans l’onglet **Paramètres** de l’activité.

![Définition d’un paramètre de flux de données](media/data-flow/parameter-assign.png "Définition d’un paramètre de flux de données")

Si votre type de données de paramètre est une chaîne de caractères, lorsque vous cliquez sur la zone de texte pour définir les valeurs des paramètres, vous pouvez choisir d’entrer un pipeline ou une expression de flux de données. Si vous choisissez l’expression de pipeline, le panneau d’expression de pipeline vous est présenté. Assurez-vous d’inclure les fonctions de pipeline dans la syntaxe d’interpolation de chaîne en utilisant `'@{<expression>}'`, par exemple :

```'@{pipeline().RunId}'```

Si votre paramètre n’est pas de type chaîne, le Générateur d’expressions Data Flow vous est toujours présenté. Ici, vous pouvez saisir n’importe quelle expression ou valeur littérale qui correspond au type de données du paramètre. Vous trouverez ci-dessous des exemples d’expression Data Flow et une chaîne littérale du Générateur d’expressions :

* ```toInteger(Role)```
* ```'this is my static literal string'```

Chaque flux de données de mappage peut comporter n’importe quelle combinaison de paramètres d’expression de pipeline et de flux de données. 

![Exemple de paramètres de flux de données](media/data-flow/parameter-example.png "Exemple de paramètres de flux de données")



## <a name="next-steps"></a>Étapes suivantes
* [Exécuter une activité de flux de données](control-flow-execute-data-flow-activity.md)
* [Expressions de flux de contrôle](control-flow-expression-language-functions.md)
