---
title: Types de données dans Bicep
description: Décrit les types de données disponibles dans Bicep
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 31f2c6e979acb3b0b622bc63ffb8a2845179491d
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025886"
---
# <a name="data-types-in-bicep"></a>Types de données dans Bicep

Cet article décrit les types de données pris en charge par [Bicep](./overview.md).

## <a name="supported-types"></a>Types pris en charge

Dans un fichier Bicep, vous pouvez utiliser les types de données suivants :

* tableau
* bool
* int
* object
* secureObject : indiqué par un modificateur dans Bicep
* secureString : indiqué par un modificateur dans Bicep
* string

## <a name="arrays"></a>Tableaux

Les tableaux commencent par un crochet ouvrant (`[`) et se terminent par un crochet fermant (`]`). Dans Bicep, un tableau doit être déclaré dans plusieurs lignes. N’utilisez pas de virgules entre les valeurs.

Dans un tableau, chaque élément est représenté par le [type any](bicep-functions-any.md). Vous pouvez avoir un tableau où tous les éléments sont du même type de données ou un tableau qui contient des types de données différents.

Dans Bicep, les tableaux sont de base 0. Dans l’exemple suivant, l’expression `exampleArray[0]` prend la valeur 1 et l’expression `exampleArray[2]` prend la valeur 3. L’index de l’indexeur peut lui-même être une autre expression. L’expression `exampleArray[index]` prend la valeur 2. Les indexeurs d’entier sont autorisés uniquement sur une expression de type tableau.

```bicep
var index = 1

var exampleArray = [
  1
  2
  3
]
```

Les indexeurs basés sur des chaînes sont autorisés dans Bicep.

```bicep
param environment string = 'prod'

var environmentSettings = {
  dev: {
    name: 'dev'
  }
  prod: {
    name: 'prod'
  }
}
```

L’expression environmentSettings['dev'] prend la valeur de l’objet suivant :

```bicep
{
  name: 'dev'
}
```

L’exemple suivant montre un tableau avec différents types.

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

## <a name="booleans"></a>valeurs booléennes

Quand vous spécifiez des valeurs booléennes, utilisez `true` ou `false`. N’entourez pas la valeur de guillemets.

```bicep
param exampleBool bool = true
```

## <a name="integers"></a>Entiers

Quand vous spécifiez des valeurs entières, n’utilisez pas de guillemets.

```bicep
param exampleInt int = 1
```

Pour les entiers passés comme paramètres inclus, la plage de valeurs peut être limitée par le SDK ou l’outil en ligne de commande que vous utilisez pour le déploiement. Par exemple, si vous utilisez PowerShell pour déployer un fichier Bicep, les types d’entiers peuvent être compris entre -2147483648 et 2147483647. Pour éviter cette limite, spécifiez des valeurs entières élevées dans un [fichier de paramètres](parameter-files.md). Les types de ressources appliquent leurs propres limites aux propriétés d’entiers.

Les formats à virgule flottante, décimal et binaire ne sont pas pris en charge actuellement.

## <a name="objects"></a>Objets

Les objets commencent par une accolade ouvrante (`{`) et se terminent par une accolade fermante (`}`). Dans Bicep, un objet doit être déclaré dans plusieurs lignes. Chaque propriété d’un objet se compose d’une clé et d’une valeur. La clé et la valeur sont séparées par un signe deux-points (`:`). Un objet accepte toute propriété de n’importe quel type.

Dans Bicep, la clé n’est pas entourée de guillemets. N’utilisez pas de virgules entre les propriétés.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

Les accesseurs de propriété sont utilisés pour accéder aux propriétés d’un objet. Ils sont construits à l’aide de l’opérateur `.`. Par exemple :

```bicep
var x = {
  y: {
    z: 'Hello`
    a: true
  }
  q: 42
}
```

À partir de la déclaration précédente, l’expression x.y.z correspond à la chaîne littérale « Hello ». De même, l’expression x.q correspond au littéral entier 42.

Les accesseurs de propriété peuvent être utilisés avec n’importe quel objet, y compris les paramètres et les variables des types d’objets et des littéraux d’objet. L’utilisation d’un accesseur de propriété sur une expression de type hors objet est une erreur.

## <a name="strings"></a>Chaînes

Dans Bicep, les chaînes sont marquées avec des guillemets simples et doivent être déclarées sur une seule ligne. Tous les caractères Unicode avec des points de code entre *0* et *10FFFF* sont autorisés.

```bicep
param exampleString string = 'test value'
```

Le tableau suivant liste l’ensemble de caractères réservés qui doivent être placés dans une séquence d’échappement avec une barre oblique inverse (`\`) :

| Séquence d'échappement | Valeur représentée | Notes |
|:-|:-|:-|
| \\ | \ ||
| \' | ' ||
| \n | saut de ligne (LF) ||
| \r | retour chariot (CR) ||
| \t | caractère de tabulation ||
| \u{x} | Point de code Unicode *x* | *x* représente une valeur de point de code hexadécimale entre *0* et *10FFFF* (les deux inclus). Les zéros non significatifs sont autorisés. Les points de code au-dessus de *FFFF* sont émis comme paire de substitution.
| \$ | $ | Doit être placé dans une séquence d’échappement uniquement s’il est suivi de *{* . |

```bicep
// evaluates to "what's up?"
var myVar = 'what\'s up?'
```

Toutes les chaînes dans Bicep prennent en charge l’interpolation. Pour injecter une expression, placez-la entre *${* et *}`. Les expressions référencées ne peuvent pas s’étendre sur plusieurs lignes.

```bicep
var storageName = 'storage${uniqueString(resourceGroup().id)}
```

## <a name="multi-line-strings"></a>Chaînes à lignes multiples

Dans Bicep, les chaînes multilignes sont définies entre 3 guillemets simples (`'''`) suivis éventuellement d’une nouvelle ligne (séquence d’ouverture) et 3 guillemets simples (`'''`) (séquence de fermeture). Les caractères entrés entre les séquences d’ouverture et de fermeture sont lus textuellement, et aucun échappement n’est nécessaire ni possible.

> [!NOTE]
> Étant donné que l’analyseur Bicep lit tous les caractères tels quels, selon les fins de ligne de votre fichier Bicep, les nouvelles lignes peuvent être interprétées comme `\r\n` ou `\n`.
> Actuellement, l’interpolation n’est pas prise en charge dans les chaînes multilignes.
> Les chaînes multilignes contenant `'''` ne sont pas prises en charge.

```bicep
// evaluates to "hello!"
var myVar = '''hello!'''

// evaluates to "hello!" because the first newline is skipped
var myVar2 = '''
hello!'''

// evaluates to "hello!\n" because the final newline is included
var myVar3 = '''
hello!
'''

// evaluates to "  this\n    is\n      indented\n"
var myVar4 = '''
  this
    is
      indented
'''

// evaluates to "comments // are included\n/* because everything is read as-is */\n"
var myVar5 = '''
comments // are included
/* because everything is read as-is */
'''

// evaluates to "interpolation\nis ${blocked}"
// note ${blocked} is part of the string, and is not evaluated as an expression
myVar6 = '''interpolation
is ${blocked}'''
```

## <a name="secure-strings-and-objects"></a>Sécuriser les chaînes et objets

La chaîne sécurisée utilise le même format que la chaîne, et l’objet sécurisé utilise le même format que l’objet. Avec Bicep, vous ajoutez le modificateur `@secure()` à une chaîne ou un objet.

Lorsque vous définissez un paramètre sur une chaîne sécurisée ou un objet sécurisé, la valeur du paramètre n’est pas enregistrée dans l’historique de déploiement et n’est pas consignée. Toutefois, si vous définissez cette valeur sécurisée sur une propriété qui n’attend pas une valeur sécurisée, la valeur n’est pas protégée. Par exemple, si vous définissez une chaîne sécurisée sur une balise, cette valeur est stockée sous forme de texte brut. Utilisez des chaînes sécurisées pour les mots de passe et les secrets.

L’exemple suivant présente deux paramètres sécurisés :

```bicep
@secure()
param password string

@secure()
param configValues object
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la structure et la syntaxe de Bicep, consultez [Structure des fichiers Bicep](./file.md).
