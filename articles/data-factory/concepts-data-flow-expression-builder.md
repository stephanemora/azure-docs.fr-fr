---
title: Générateur d’expressions dans un flux de données de mappage
description: Générer des expressions à l’aide du Générateur d’expressions dans des flux de données de mappage dans Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: f522812f762b55ec61794101e6cd1ec15fb171ca
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212099"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Générer des expressions dans un flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dans un flux de données de mappage, de nombreuses propriétés de transformation sont entrées en tant qu’expressions. Ces expressions sont composées de valeurs de colonne, de paramètres, de fonctions, d’opérateurs et de littéraux qui correspondent à un type de données Spark au moment de l’exécution.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Générateur d’expressions ouvert

L’interface de modification d’expression dans l’expérience utilisateur Azure Data Factory est connue sous le nom de Générateur d’expressions. Quand vous entrez votre logique d’expression, Data Factory utilise la complétion de code [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) pour la mise en surbrillance, la vérification syntaxique et l’autocomplétion.

![Générateur d’expressions](media/data-flow/xpb1.png "Générateur d’expressions")

Dans les transformations telles que la colonne dérivée et le filtre, où les expressions sont obligatoires, ouvrez le Générateur d’expressions en sélectionnant la zone d’expression bleue.

![Zone d’expression bleue](media/data-flow/expressionbox.png "Générateur d’expressions")

Lorsque vous référencez des colonnes dans une correspondance ou un groupe par condition, une expression peut extraire des valeurs des colonnes. Pour créer une expression, sélectionnez **Colonne calculée**.

![Option de colonne calculée](media/data-flow/computedcolumn.png "Générateur d’expressions")

Pour les cas où une expression ou une valeur littérale sont des entrées valides, sélectionnez **Ajout de contenu dynamique** pour générer une expression qui renvoie une valeur littérale.

![Option d’ajout de contenu dynamique](media/data-flow/add-dynamic-content.png "Générateur d’expressions")

## <a name="expression-language-reference"></a>Informations de référence sur le langage des expressions

Les flux de données de mappage comportent des fonctions et opérateurs intégrés qui peuvent être utilisés dans des expressions. Pour obtenir la liste des fonctions disponibles, consultez [Fonctions d’expression dans le flux de données de mappage](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Noms de colonne avec des caractères spéciaux

Quand vous avez des noms de colonnes qui comportent des espaces ou des caractères spéciaux, placez-les entre accolades pour les référencer dans une expression.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Aperçu des résultats d’expressions

Si le [mode débogage](concepts-data-flow-debug-mode.md) est activé, vous pouvez utiliser le cluster Spark activé pour voir un aperçu du résultat de l’évaluation de votre expression. Pendant que vous générez votre logique, vous pouvez déboguer votre expression en temps réel. 

![Aperçu en cours](media/data-flow/exp4b.png "Aperçu des données d’expression")

Sélectionnez **Actualiser** pour mettre à jour les résultats de votre expression par rapport à un exemple en direct de votre source.

![Bouton Actualiser](media/data-flow/exp5.png "Aperçu des données d’expression")

## <a name="string-interpolation"></a>Interpolation de chaîne

Utilisez des guillemets pour entourer le texte de chaîne littérale avec les expressions. Vous pouvez inclure des fonctions, des colonnes et des paramètres d’expression. L’interpolation de chaîne s’avère utile pour éviter une utilisation intensive de la concaténation de chaînes lorsque des paramètres sont inclus dans les chaînes de requête. Pour utiliser la syntaxe d’expression, mettez-la entre accolades.

Voici quelques exemples d’interpolation de chaîne :

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Expressions de commentaire

Ajoutez des commentaires à vos expressions en utilisant une seule ligne et une syntaxe de commentaire multiligne.

Les exemples suivants sont des commentaires valides :

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```

Si vous placez un commentaire en haut de votre expression, il apparaît dans la zone de texte de transformation afin de documenter vos expressions de transformation.

![Commentaire dans la zone de texte de transformation](media/data-flow/comments2.png "Commentaires")

## <a name="regular-expressions"></a>Expressions régulières

De nombreuses fonctions du langage d’expression utilisent la syntaxe des expressions régulières. Quand vous utilisez des fonctions d’expression régulière, le Générateur d’expressions tente d’interpréter une barre oblique inverse (\\) comme une séquence de caractères d’échappement. Quand vous utilisez des barres obliques inverses dans une expression régulière, placez l’expression régulière entière entre des barres obliques (\`) ou utilisez une barre oblique inverse double.

Voici un exemple utilisant des barres obliques :

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Voici un exemple utilisant des barres obliques doubles :

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Adresser des index de tableau

Avec des fonctions d’expression qui retournent des tableaux, utilisez des crochets ([]) pour adresser des index spécifiques qui retournent des objets tableau. Le tableau est de base 1.

![Générateur d’expressions - tableau](media/data-flow/expb2.png "Aperçu des données d’expression")

## <a name="keyboard-shortcuts"></a>Raccourcis clavier

* Ctrl+K, Ctrl+C : marquer un commentaire sur la ligne entière.
* Ctrl+K, Ctrl+U : supprimer les marques de commentaire.
* F1 : fournir à l’éditeur des commandes d’aide.
* Alt + touche flèche bas : descendre d’une ligne par rapport à la ligne actuelle.
* Alt + touche flèche haut : monter d’une ligne par rapport à la ligne actuelle.
* Ctrl + Espace : afficher l’aide contextuelle.

## <a name="convert-to-dates-or-timestamps"></a>Convertir en dates ou en timestamps

Pour inclure des littéraux de chaîne dans votre sortie d’horodatage, wrappez votre conversion dans ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Pour convertir des millisecondes issues de l’époque en date ou en horodatage, utilisez `toTimestamp(<number of milliseconds>)`. Si le temps s’écoule en secondes, multipliez par 1 000.

```toTimestamp(1574127407*1000l)```

Le « l » situé à la fin de l’expression précédente signifie la conversion en type long comme la syntaxe en ligne.

## <a name="find-time-from-epoch-or-unix-time"></a>Connaître l’heure à l’aide d’Epoch ou de l’heure Unix

toLong( currentTimestamp() - toTimestamp('1970-01-01 00:00:00.000', 'yyyy-MM-dd HH:mm:ss.SSS') ) * 1000l

## <a name="next-steps"></a>Étapes suivantes

[Commencer à créer des expressions de transformation de données](data-flow-expression-functions.md)
