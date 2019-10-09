---
title: Générateur d’expressions de la fonctionnalité de mappage de flux de données d’Azure Data Factory
description: Générateur d’expressions pour la fonctionnalité de mappage de flux de données d’Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 67a6de6d85a58f48af4761e0b5d5b0a1a4d74b1a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703408"
---
# <a name="mapping-data-flow-expression-builder"></a>Générateur d’expressions de la fonctionnalité de mappage de Data Flow

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La fonctionnalité de mappage de flux de données d’Azure Data Factory propose des zones d’expression dans lesquelles vous pouvez entrer des expressions pour la transformation de données. Vous pouvez utiliser des colonnes, champs, variables, paramètres et fonctions extraits de votre flux de données dans ces zones. Pour générer l’expression, utilisez le Générateur d’expressions que vous pouvez démarrer en cliquant dans la zone de texte d’expression à l’intérieur de la transformation. Lors de la sélection de colonnes pour la transformation, vous verrez parfois des options « Colonne calculée ». En cliquant sur celles-ci, vous verrez le Générateur d’expressions démarrer.

![Générateur d’expressions](media/data-flow/xpb1.png "Générateur d’expressions")

Par défaut, l’option d’éditeur de texte est définie pour l’outil Générateur d’expressions. La fonctionnalité de saisie semi-automatique lit dans le modèle objet Data Flow d’Azure Data Factory, avec vérification et mise en surbrillance de la syntaxe.

![Saisie semi-automatique du Générateur d’expressions](media/data-flow/expb1.png "Saisie semi-automatique du Générateur d’expressions")

## <a name="build-schemas-in-output-schema-pane"></a>Générer des schémas dans le volet Schéma de sortie

![Ajouter une colonne complexe](media/data-flow/complexcolumn.png "Ajouter des colonnes")

Dans le volet Schéma de sortie de gauche, les colonnes que vous modifiez et ajoutez à votre schéma s'affichent. Vous pouvez y créer des structures de données simples et complexes de manière interactive. Ajoutez des champs supplémentaires à l’aide de « Ajouter une colonne » et générez des hiérarchies de build à l’aide de « Ajouter une sous-colonne ».

![Ajouter une sous-colonne](media/data-flow/addsubcolumn.png "Ajouter une sous-colonne")

## <a name="data-preview-in-debug-mode"></a>Aperçu des données en mode de débogage

![Générateur d’expressions](media/data-flow/exp4b.png "Aperçu des données d’une expression")

Lorsque vous travaillez sur vos expressions de flux de données, passez en mode de débogage à partir de l’aire de conception Data Flow d’Azure Data Factory, ce qui a pour effet d’activer l’aperçu en cours des résultats de données produits par l’expression que vous générez. Le débogage dynamique en temps réel est activé pour vos expressions.

![Mode de débogage](media/data-flow/debugbutton.png "Bouton Déboguer")

Cliquez sur le bouton Actualiser pour mettre à jour les résultats de votre expression par rapport à un exemple en direct de votre source en temps réel.

![Générateur d’expressions](media/data-flow/exp5.png "Aperçu des données d’une expression")

## <a name="comments"></a>Commentaires

Ajoutez des commentaires à vos expressions en utilisant une seule ligne et un syntaxe de commentaire multiligne :

![Commentaires](media/data-flow/comments.png "Commentaires")

## <a name="regular-expressions"></a>Expressions régulières

Le langage d’expressions de flux de données d’Azure Data Factory ([documentation de référence complète ici](https://aka.ms/dataflowexpressions)) active des fonctions qui incluent une syntaxe d’expression régulière. Quand vous utilisez des fonctions d’expression régulière, le Générateur d’expressions tente d’interpréter une barre oblique inverse (\\) comme une séquence de caractères d’échappement. Quand vous utilisez des barres obliques inverses dans une expression régulière, placez l’expression régulière entière entre des écarts (\`) ou utilisez une barre oblique inverse double.

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

![Tableau du générateur d’expression](media/data-flow/expb2.png "Aperçu de données d’expression")

## <a name="handling-names-with-special-characters"></a>Gestion des noms avec des caractères spéciaux

Quand vous avez des noms de colonnes qui comportent des espaces ou des caractères spéciaux, placez ces noms entre accolades.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Étapes suivantes

[Commencer à créer des expressions de transformation de données](data-flow-expression-functions.md)
