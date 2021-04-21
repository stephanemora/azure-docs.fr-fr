---
title: Opérateurs Bicep
description: Décrit les opérateurs Bicep disponibles pour les déploiements Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 4bf1005a11b1dcfea9f4b28d6bd3fa7c33e3278f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211244"
---
# <a name="bicep-operators"></a>Opérateurs Bicep

Cet article décrit les opérateurs Bicep disponibles lorsque vous créez un modèle Bicep et utilisez Azure Resource Manager pour déployer des ressources. Ces opérateurs sont utilisés pour calculer et comparer des valeurs, ou évaluer des conditions. Il existe trois types d'opérateurs Bicep : les opérateurs de [comparaison](#comparison), les opérateurs [logiques](#logical) et les opérateurs [numériques](#numeric).

## <a name="comparison"></a>Comparaison

Les opérateurs de comparaison comparent les valeurs et renvoient `true` ou `false`.

| Opérateur | Nom | Description |
| ---- | ---- | ---- |
| `>=` | [Supérieur ou égal à](bicep-operators-comparison.md#greater-than-or-equal-) | Détermine si la première valeur est supérieure ou égale à la seconde. |
| `>`  | [Supérieur à](bicep-operators-comparison.md#greater-than-) | Détermine si la première valeur est supérieure à la seconde. |
| `<=` | [Inférieur ou égal à](bicep-operators-comparison.md#less-than-or-equal-) | Détermine si la première valeur est inférieure ou égale à la seconde. |
| `<`  | [Inférieur à](bicep-operators-comparison.md#less-than-) | Détermine si la première valeur est inférieure à la seconde. |
| `==` | [Égal à](bicep-operators-comparison.md#equals-) | Détermine si deux valeurs sont égales. |
| `!=` | [Différent de](bicep-operators-comparison.md#not-equal-) | Détermine si deux valeurs sont différentes. |
| `=~` | [Égal à - insensible à la casse](bicep-operators-comparison.md#equal-case-insensitive-) | Ignore la casse pour déterminer si deux valeurs sont égales. |
| `!~` | [Différent de - insensible à la casse](bicep-operators-comparison.md#not-equal-case-insensitive-) | Ignore la casse pour déterminer si deux valeurs sont différentes. |

## <a name="logical"></a>Logical

Les opérateurs logiques évaluent des valeurs booléennes, renvoient des valeurs non nulles ou évaluent une expression conditionnelle.

| Opérateur | Nom | Description |
| ---- | ---- | ---- |
| `&&` | [And](bicep-operators-logical.md#and-) | Renvoie `true` si toutes les valeurs sont vraies. |
| `||`| [Ou](bicep-operators-logical.md#or-) | Renvoie `true` si une des deux valeurs est vraie. |
| `!` | [Not](bicep-operators-logical.md#not-) | Inverse une valeur booléenne. |
| `??` | [Coalesce](bicep-operators-logical.md#coalesce-) | Renvoie la première valeur non nulle. |
| `?` `:` | [Expression conditionnelle](bicep-operators-logical.md#conditional-expression--) | Évalue une condition pour déterminer si elle est vraie ou fausse et renvoie une valeur. |

## <a name="numeric"></a>Numérique

Les opérateurs numériques utilisent des entiers pour effectuer des calculs et renvoyer des valeurs entières.

| Opérateur | Nom | Description |
| ---- | ---- | ---- |
| `*` | [Multiplier](bicep-operators-numeric.md#multiply-) | Multiplie deux entiers. |
| `/` | [Divide](bicep-operators-numeric.md#divide-) | Divise un entier par un entier. |
| `%` | [Modulo](bicep-operators-numeric.md#modulo-) | Divise un entier par un autre entier et renvoie le reste. |
| `+` | [Ajouter](bicep-operators-numeric.md#add-) | Additionne deux entiers. |
| `-` | [Soustraire](bicep-operators-numeric.md#subtract--) | Soustrait un entier d'un autre entier. |
| `-` | [Moins](bicep-operators-numeric.md#minus--) | Multiplie un entier par `-1`. |

> [!NOTE]
> Soustraire et Moins utilisent le même opérateur. La fonctionnalité est différente, car Soustraire utilise deux opérandes alors que Moins n'en utilise qu'un seul.

## <a name="next-steps"></a>Étapes suivantes

- Pour créer un fichier Bicep, consultez [Tutoriel : Créer et déployer votre premier fichier Bicep Azure Resource Manager](bicep-tutorial-create-first-bicep.md).
- Pour plus d'informations sur la résolution des erreurs de type Bicep, consultez [Fonction any pour Bicep](template-functions-any.md).
- Pour comparer la syntaxe de Bicep et de JSON, consultez [Comparaison de JSON et de Bicep pour les modèles](compare-template-syntax.md).
- Pour obtenir des exemples de fonctions de modèle Bicep et ARM, consultez [Fonctions de modèle ARM](template-functions.md).
