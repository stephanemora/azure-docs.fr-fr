---
title: Générateur d’expressions dans un flux de données de mappage
description: Générez des expressions à l’aide du générateur d’expressions dans des flux de données de mappage dans Azure Data Factory.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969349"
---
# <a name="building-expressions-in-mapping-data-flow"></a>Génération d’expressions dans un flux de données de mappage

Dans un flux de données de mappage, de nombreuses propriétés de transformation sont entrées en tant qu’expressions. Ces expressions sont composées de valeurs de colonne, de paramètres, de fonctions, d’opérateurs et de littéraux qui correspondent à un type de données Spark au moment de l’exécution.

## <a name="opening-the-expression-builder"></a>Ouverture du générateur d’expressions

L’interface de modification d’expression dans l’expérience utilisateur de la fabrique de données est connue sous le nom de **Générateur d’expressions**. Quand vous entrez dans votre logique d’expression, la fabrique de données utilise la complétion de code [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) pour la mise en surbrillance, la vérification syntaxique et l’autocomplétion.

![Générateur d’expressions](media/data-flow/xpb1.png "Générateur d’expressions")

Dans les transformations telles que la colonne dérivée et le filtre, où les expressions sont obligatoires, ouvrez le générateur d’expressions en cliquant sur la zone d’expression bleue.

![Générateur d’expressions](media/data-flow/expressionbox.png "Générateur d’expressions")

Lors du référencement de colonnes dans une correspondance ou un groupe par condition, une expression peut extraire des valeurs des colonnes. Pour créer une expression, sélectionnez l’option de « colonne calculée ».

![Générateur d’expressions](media/data-flow/computedcolumn.png "Générateur d’expressions")

Pour les cas où une expression ou une valeur littérale sont des entrées valides, l’ajout de contenu dynamique vous permet de générer une expression qui prend la valeur d’un littéral.

![Générateur d’expressions](media/data-flow/add-dynamic-content.png "Générateur d’expressions")

## <a name="expression-language-reference"></a>Informations de référence sur le langage des expressions

Les flux de données de mappage comportent des fonctions et opérateurs intégrés qui peuvent être utilisés dans des expressions. La liste des fonctions disponibles se trouve dans la page de référence du [langage d’expression des flux de données de mappage](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Noms de colonne avec des caractères spéciaux

Quand vous avez des noms de colonnes qui comportent des espaces ou des caractères spéciaux, placez-les entre accolades pour les référencer dans une expression.

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>Aperçu des résultats d’expressions

Si le [mode débogage](concepts-data-flow-debug-mode.md) est activé, vous pouvez utiliser le cluster Spark activé pour voir un aperçu du résultat de l’évaluation de votre expression. Pendant que vous générez votre logique, vous pouvez déboguer votre expression en temps réel. 

![Générateur d’expressions](media/data-flow/exp4b.png "Aperçu des données d’expression")

Cliquez sur le bouton Actualiser pour mettre à jour les résultats de votre expression par rapport à un exemple en direct de votre source.

![Générateur d’expressions](media/data-flow/exp5.png "Aperçu des données d’expression")

## <a name="string-interpolation"></a>Interpolation de chaîne

Utilisez des guillemets doubles pour entourer le texte de chaîne littérale avec les expressions. Vous pouvez inclure des fonctions, des colonnes et des paramètres d’expression. L’interpolation de chaîne s’avère utile pour éviter une utilisation intensive de la concaténation de chaînes lors de l’inclusion de paramètres dans les chaînes de requête. Pour utiliser la syntaxe d’expression, mettez-la entre accolades.

Voici quelques exemples d’interpolation de chaîne :

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>Commenter les expressions

Ajoutez des commentaires à vos expressions en utilisant une seule ligne et un syntaxe de commentaire multiligne :

![Commentaires](media/data-flow/comments.png "Commentaires")

Vous trouverez ci-dessous des exemples de commentaires valides :

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Si vous placez un commentaire en haut de votre expression, il apparaît dans la zone de texte de transformation afin de documenter vos expressions de transformation :

![Commentaires](media/data-flow/comments2.png "Commentaires")

## <a name="regular-expressions"></a>Expressions régulières

De nombreuses fonctions du langage d’expression utilisent la syntaxe des expressions régulières. Quand vous utilisez des fonctions d’expression régulière, le Générateur d’expressions tente d’interpréter une barre oblique inverse (\\) comme une séquence de caractères d’échappement. Quand vous utilisez des barres obliques inverses dans une expression régulière, placez l’expression régulière entière entre des écarts (\`) ou utilisez une barre oblique inverse double.

Exemple d’utilisation d’écarts

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

ou de barre oblique double

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Adressage d’index de tableau

Avec des fonctions d’expression qui retournent des tableaux, utilisez des crochets [] pour adresser des index spécifiques qui retournent un objet tableau. Le tableau est de base 1.

![Générateur d’expressions - tableau](media/data-flow/expb2.png "Aperçu des données d’expression")

## <a name="keyboard-shortcuts"></a>Raccourcis clavier

* ```Ctrl-K Ctrl-C```: Marquer un commentaire sur la ligne entière
* ```Ctrl-K Ctrl-U```: Supprimer les marques de commentaire
* ```F1```: Fournir à l’éditeur des commandes d’aide
* ```Alt-Down Arrow```: Descendre d’une ligne par rapport à la ligne actuelle
* ```Alt-Up Arrow```: Monter d’une ligne par rapport à la ligne actuelle
* ```Cntrl-Space```: Afficher l’aide contextuelle

## <a name="convert-to-dates-or-timestamps"></a>Convertir en dates ou en timestamps

Pour inclure des littéraux de chaîne dans votre sortie d’horodatage, vous devez wrapper votre conversion dans ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Pour convertir des millisecondes issues d’Epoch en date ou en horodatage, utilisez `toTimestamp(<number of milliseconds>)`. Si le temps s’écoule en secondes, multipliez par 1 000.

```toTimestamp(1574127407*1000l)```

Le « l » situé à la fin de l’expression ci-dessus signifie la conversion en type long comme la syntaxe en ligne.

## <a name="next-steps"></a>Étapes suivantes

[Commencer à créer des expressions de transformation de données](data-flow-expression-functions.md)
