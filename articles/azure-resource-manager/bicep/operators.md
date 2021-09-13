---
title: Opérateurs Bicep
description: Décrit les opérateurs Bicep disponibles pour les déploiements Azure Resource Manager.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 80fc9e4e1285d86858a476feba30621a7afe1c79
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221052"
---
# <a name="bicep-operators"></a>Opérateurs Bicep

Cet article décrit les opérateurs Bicep. Ces opérateurs sont utilisés pour calculer et comparer des valeurs, ou évaluer des conditions. Il existe quatre types d’opérateurs Bicep :

- [accesseur](#accessor)
- [Opérateurs de comparaison](#comparison)
- [Opérateurs logiques](#logical)
- [numeric](#numeric)

## <a name="operator-precedence-and-associativity"></a>Priorité des opérateurs et associativité

Les opérateurs ci-dessous sont répertoriés par ordre de priorité décroissant (plus la position est élevée, plus la priorité est élevée). Les opérateurs de même niveau ont une priorité identique.

| Symbole | Type d’opération | Associativité |
|:-|:-|:-|
| `(` `)` `[` `]` `.` `::` | Parenthèses, indexeurs de tableaux, accesseurs de propriété et accesseur de ressource imbriquée  | De gauche à droite |
| `!` `-` | Unaire | De droite à gauche |
| `%` `*` `/` | Multiplicatif | De gauche à droite |
| `+` `-` | Additive | De gauche à droite |
| `<=` `<` `>` `>=` | Relationnel | De gauche à droite |
| `==` `!=` `=~` `!~` | Égalité | De gauche à droite |
| `&&` | ET logique | De gauche à droite |
| `||` | OU logique | De gauche à droite |
| `?` `:` | Expression conditionnelle (terniaire) | De droite à gauche
| `??` | Coalesce | De gauche à droite

La délimitation d’une expression entre `(` et `)` vous permet de substituer la priorité d’opérateur Bicep par défaut. Par exemple, l’expression x + y / z évalue d’abord la division, puis l’addition. Mais l’expression (x + y) / z évalue d’abord l’addition, puis la division.

## <a name="accessor"></a>Accesseur

Les opérateurs d’accesseur sont utilisés pour accéder aux ressources et aux propriétés imbriquées sur les objets.

| Opérateur | Nom | Description |
| ---- | ---- | ---- |
| `[]` | [Accesseur d’index](./operators-access.md#index-accessor) | Accéder à un élément d’un tableau ou d’une propriété sur un objet. |
| `.` | [Accesseur de fonction](./operators-access.md#function-accessor) | Appeler une fonction sur une ressource. |
| `::` | [Accesseur de ressource imbriquée](./operators-access.md#nested-resource-accessor) | Accédez à une ressource imbriquée à partir de l’extérieur de la ressource parente. |
| `.` | [Accesseur de propriété](./operators-access.md#property-accessor) | Propriétés d’accès d’un objet. |

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
