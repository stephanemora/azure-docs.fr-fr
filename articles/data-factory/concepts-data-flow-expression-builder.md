---
title: Générateur d’expressions de la fonctionnalité de mappage de flux de données d’Azure Data Factory
description: Générateur d’expressions pour la fonctionnalité de mappage de flux de données d’Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: dab0a6a5eee8893f28a221b44d57afe255841fa0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329743"
---
# <a name="mapping-data-flow-expression-builder"></a>Générateur d’expressions de la fonctionnalité de mappage de Data Flow

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La fonctionnalité de mappage de flux de données d’Azure Data Factory propose des zones d’expression dans lesquelles vous pouvez entrer des expressions pour la transformation de données. Vous pouvez utiliser des colonnes, champs, variables, paramètres et fonctions extraits de votre flux de données dans ces zones. Pour générer l’expression, utilisez le Générateur d’expressions que vous pouvez démarrer en cliquant dans la zone de texte d’expression à l’intérieur de la transformation. Lors de la sélection de colonnes pour la transformation, vous verrez parfois des options « Colonne calculée ». En cliquant sur celles-ci, vous verrez le Générateur d’expressions démarrer.

![Générateur d’expressions](media/data-flow/expression.png "Générateur d’expressions")

Par défaut, l’option d’éditeur de texte est définie pour l’outil Générateur d’expressions. La fonctionnalité de saisie semi-automatique lit dans le modèle objet Data Flow d’Azure Data Factory, avec vérification et mise en surbrillance de la syntaxe.

![Saisie semi-automatique du Générateur d’expressions](media/data-flow/expb1.png "Saisie semi-automatique du Générateur d’expressions")

## <a name="currently-working-on-field"></a>Champ Currently Working On

![Générateur d’expressions](media/data-flow/exp3.png "Currently Working On")

En haut à gauche de l’interface utilisateur du Générateur d’expressions figure un champ nommé « Currently Working On » (Travail en cours sur) contenant le nom du champ sur lequel vous travaillez actuellement. L’expression que vous créez dans l’interface utilisateur s’appliquera uniquement à ce champ. Si vous souhaitez transformer un autre champ, enregistrez votre travail, puis utilisez cette liste déroulante pour sélectionner un autre champ et générer une expression pour celui-ci.

## <a name="data-preview-in-debug-mode"></a>Aperçu des données en mode débogage

![Générateur d’expressions](media/data-flow/exp4b.png "Aperçu des données d’expression")

Lorsque vous travaillez sur vos expressions, vous pouvez passer en mode de débogage à partir de l’aire de conception Data Flow d’Azure Data Factory, ce qui a pour effet d’activer l’aperçu en cours des résultats de données produits par l’expression que vous générez. Le débogage dynamique en temps réel est activé pour vos expressions.

![Mode de débogage](media/data-flow/debugbutton.png "Bouton Déboguer")


![Générateur d’expressions](media/data-flow/exp5.png "Aperçu des données d’une expression")

## <a name="comments"></a>Commentaires

Ajoutez des commentaires à vos expressions en utilisant une seule ligne et un syntaxe de commentaire multiligne :

![Commentaires](media/data-flow/comments.png "Commentaires")

## <a name="regular-expressions"></a>Expressions régulières

Le langage d’expressions de flux de données d’Azure Data Factory ([documentation de référence complète ici](http://aka.ms/dataflowexpressions)) active des fonctions qui incluent une syntaxe d’expression régulière. Lorsque vous utilisez des fonctions d’expression régulière, le Générateur d’expressions tente d’interpréter une barre oblique inverse (\) comme une séquence de caractères d’échappement. Lorsque vous utilisez des barres obliques inverses dans une expression régulière, placez l’expression régulière entière entre des écarts ` ` ou utilisez une barre oblique inverse double.

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
