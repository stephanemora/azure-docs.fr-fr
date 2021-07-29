---
title: Opérateurs Bicep
description: Décrit les opérateurs Bicep disponibles pour les déploiements Azure Resource Manager.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 154a42d1bcdc78eee63241286e8f65ab7777bc6b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026451"
---
# <a name="bicep-operators"></a>Opérateurs Bicep

Cet article décrit les opérateurs Bicep disponibles lorsque vous créez un modèle Bicep et utilisez Azure Resource Manager pour déployer des ressources. Ces opérateurs sont utilisés pour calculer et comparer des valeurs, ou évaluer des conditions. Il existe trois types d’opérateurs Bicep :

- [Opérateurs de comparaison](#comparison)
- [Opérateurs logiques](#logical)
- [numeric](#numeric)

La délimitation d’une expression entre `(` et `)` vous permet de substituer la priorité d’opérateur Bicep par défaut. Par exemple, l’expression x + y / z évalue d’abord la division, puis l’addition. Mais l’expression (x + y) / z évalue d’abord l’addition, puis la division.

Pour plus d’informations sur l’accès à une ressource par le biais de l'opérateur `::`, consultez [Définir le nom et le type des ressources enfants dans Bicep](child-resource-name-type.md).

## <a name="comparison"></a>Comparaison

Les opérateurs de comparaison comparent les valeurs et renvoient `true` ou `false`.

| Opérateur | Nom | Description |
| ---- | ---- | ---- |
| `>=` | [Supérieur ou égal à](./operators-comparison.md#greater-than-or-equal-) | Détermine si la première valeur est supérieure ou égale à la seconde. |
| `>`  | [Supérieur à](./operators-comparison.md#greater-than-) | Détermine si la première valeur est supérieure à la seconde. |
| `<=` | [Inférieur ou égal à](./operators-comparison.md#less-than-or-equal-) | Détermine si la première valeur est inférieure ou égale à la seconde. |
| `<`  | [Inférieur à](./operators-comparison.md#less-than-) | Détermine si la première valeur est inférieure à la seconde. |
| `==` | [Égal à](./operators-comparison.md#equals-) | Détermine si deux valeurs sont égales. |
| `!=` | [Différent de](./operators-comparison.md#not-equal-) | Détermine si deux valeurs sont différentes. |
| `=~` | [Égal à - insensible à la casse](./operators-comparison.md#equal-case-insensitive-) | Ignore la casse pour déterminer si deux valeurs sont égales. |
| `!~` | [Différent de - insensible à la casse](./operators-comparison.md#not-equal-case-insensitive-) | Ignore la casse pour déterminer si deux valeurs sont différentes. |

## <a name="logical"></a>Logical

Les opérateurs logiques évaluent des valeurs booléennes, renvoient des valeurs non nulles ou évaluent une expression conditionnelle.

| Opérateur | Nom | Description |
| ---- | ---- | ---- |
| `&&` | [And](./operators-logical.md#and-) | Renvoie `true` si toutes les valeurs sont vraies. |
| `||`| [Ou](./operators-logical.md#or-) | Renvoie `true` si une des deux valeurs est vraie. |
| `!` | [Not](./operators-logical.md#not-) | Inverse une valeur booléenne. |
| `??` | [Coalesce](./operators-logical.md#coalesce-) | Renvoie la première valeur non nulle. |
| `?` `:` | [Expression conditionnelle](./operators-logical.md#conditional-expression--) | Évalue une condition pour déterminer si elle est vraie ou fausse et renvoie une valeur. |

## <a name="numeric"></a>Numérique

Les opérateurs numériques utilisent des entiers pour effectuer des calculs et retourner des valeurs entières.

| Opérateur | Nom | Description |
| ---- | ---- | ---- |
| `*` | [Multiplier](./operators-numeric.md#multiply-) | Multiplie deux entiers. |
| `/` | [Divide](./operators-numeric.md#divide-) | Divise un entier par un entier. |
| `%` | [Modulo](./operators-numeric.md#modulo-) | Divise un entier par un entier et retourne le reste. |
| `+` | [Ajouter](./operators-numeric.md#add-) | Ajoute deux entiers. |
| `-` | [Soustraire](./operators-numeric.md#subtract--) | Soustrait un entier d’un entier. |
| `-` | [Moins](./operators-numeric.md#minus--) | Multiplie un entier par `-1`. |

> [!NOTE]
> Soustraire et Moins utilisent le même opérateur. La fonctionnalité est différente, car Soustraire utilise deux opérandes et Moins en utilise un seul.

## <a name="next-steps"></a>Étapes suivantes

- Pour créer un fichier Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Pour plus d’informations sur la résolution des erreurs de type Bicep, consultez [Fonction any pour Bicep](./bicep-functions-any.md).
- Pour comparer la syntaxe de Bicep et de JSON, consultez [Comparaison de JSON et de Bicep pour les modèles](./compare-template-syntax.md).
- Pour obtenir des exemples de fonctions Bicep, consultez [Fonctions Bicep](./bicep-functions.md).
