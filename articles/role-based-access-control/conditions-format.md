---
title: Format et syntaxe des conditions d’attribution de rôle Azure – Azure RBAC
description: Obtenir une vue d’ensemble du format et de la syntaxe des conditions d’attribution de rôle Azure pour le contrôle d’accès en fonction des attributs Azure (Azure ABAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: b271da3cf8e591df8557133abcff248a737645e5
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489657"
---
# <a name="azure-role-assignment-condition-format-and-syntax-preview"></a>Format et syntaxe des conditions d’attribution de rôle Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Une condition est une vérification supplémentaire que vous pouvez éventuellement ajouter à votre attribution de rôle pour fournir un contrôle d’accès plus précis. Par exemple, vous pouvez ajouter une condition qui oblige un objet à porter une étiquette spécifique pour être lu. Cet article décrit le format et la syntaxe des conditions d’attribution de rôle.

## <a name="condition-format"></a>Format de condition

Pour mieux comprendre les conditions d’attribution de rôle, il est utile d’examiner le format.

### <a name="simple-condition"></a>Condition simple

La condition la plus simple se compose d’une action ciblée et d’une expression. Une action est une opération qu’un utilisateur peut effectuer sur un type de ressource. Une expression est une instruction dont la valeur est vraie ou fausse, ce qui détermine si l’action est autorisée à être exécutée.

L’exemple suivant illustre le format d’une condition simple.

![Format d’une condition simple avec une seule action et une seule expression.](./media/conditions-format/format-simple.png)

La condition suivante a une action « Lire un blob ». L’expression vérifie si le nom du conteneur est blobs-example-container.

![Exemple de condition simple avec une action de lecture de blobs et une expression de nom de conteneur.](./media/conditions-format/format-simple-example.png)

![Diagramme montrant l’accès en lecture aux blobs avec un nom de conteneur particulier.](./media/conditions-format/format-simple-example-diagram.png)

### <a name="how-a-condition-is-evaluated"></a>Évaluation d’une condition

Si un utilisateur tente d’effectuer une action dans l’attribution de rôle qui n’est pas `<action>`, `!(ActionMatches)` prend la valeur true, et la condition globale prend la valeur true pour permettre l’exécution de l’action.

Si un utilisateur tente d’effectuer `<action>` dans l’attribution de rôle, `!(ActionMatches)` prend la valeur false, et l’expression est donc évaluée. Si l’expression prend la valeur true, la condition globale prend la valeur true pour permettre l’exécution de `<action>`. Dans le cas contraire, `<action>` n’est pas autorisé à être exécutée.

Le pseudo-code suivant montre une autre façon de lire cette condition.

```
if a user tries to perform an action in the role assignment that does not match <action>
{
    Allow action to be performed
}
else
{
    if <attribute> <operator> <value> is true
    {
        Allow <action> to be performed
    }
    else
    {
        Do not allow <action> to be performed
    }
}
```

### <a name="suboperations"></a>Sous-opérations

Certaines actions comportent des sous-opérations. Par exemple, l’action de données « Lire un blob » a la sous-opération « Lire du contenu à partir d’un blob avec des conditions de balise ». Les conditions avec sous-opérations ont le format suivant.

![Format d’une action avec une sous-opération.](./media/conditions-format/format-suboperation.png)

### <a name="multiple-actions"></a>Actions multiples

Une condition peut inclure plusieurs actions que vous souhaitez autoriser si la condition est vraie. Si vous sélectionnez plusieurs actions pour une même condition, il peut y avoir moins d’attributs à choisir pour votre condition, car les attributs doivent être disponibles pour toutes les actions sélectionnées.

![Format pour les actions multiples à autoriser si la condition est vraie.](./media/conditions-format/format-multiple-actions.png)

### <a name="multiple-expressions"></a>Expressions multiples

Une condition peut inclure plusieurs expressions. Selon l’opérateur, les attributs peuvent être vérifiés par rapport à plusieurs valeurs.

![Format pour les expressions multiples utilisant des opérateurs booléens et des valeurs multiples.](./media/conditions-format/format-multiple-expressions.png)

### <a name="multiple-conditions"></a>Plusieurs conditions

Vous pouvez également combiner des conditions pour cibler plusieurs actions.

![Format pour les conditions multiples utilisant un opérateur booléen.](./media/conditions-format/format-multiple-conditions.png)

## <a name="condition-syntax"></a>Syntaxe de condition

L’exemple suivant illustre la syntaxe d’une condition d’attribution de rôle.

```
(
    (
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        ...
    )
    OR
    (
        <attribute> <operator> {<value, <value>, ...}
        AND | OR
        <attribute> <operator> {<value>, <value>, ...}
        AND | OR
        ...
    )
)
AND
(
    (
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        ...
    )
    OR
    (
        <attribute> <operator> {<value, <value>, ...}
        AND | OR
        <attribute> <operator> {<value>, <value>, ...}
        AND | OR
        ...
    )
)
AND
...
```

## <a name="actions"></a>Actions

Actuellement, il est possible d’ajouter des conditions à des attributions de rôles intégrés ou personnalisés qui ont des actions de données blob de stockage. Sont inclus les rôles intégrés suivants :

- [Contributeur aux données Blob du stockage](built-in-roles.md#storage-blob-data-contributor)
- [Propriétaire des données Blob du stockage](built-in-roles.md#storage-blob-data-owner)
- [Lecteur des données blob du stockage](built-in-roles.md#storage-blob-data-reader)

Pour obtenir la liste des actions de blob de stockage que vous pouvez utiliser dans les conditions, consultez [Actions et attributs pour les conditions d’attribution de rôle Azure dans Stockage Azure (préversion)](../storage/common/storage-auth-abac-attributes.md).

## <a name="attributes"></a>Attributs

En fonction des actions sélectionnées, l’attribut peut être trouvé à différents emplacements. Si vous sélectionnez plusieurs actions pour une même condition, il peut y avoir moins d’attributs à choisir pour votre condition, car les attributs doivent être disponibles pour toutes les actions sélectionnées. Pour spécifier un attribut, vous devez inclure la source en tant que préfixe.

> [!div class="mx-tableFixed"]
> | Source de l’attribut | Description | Code |
> | --- | --- | --- |
> | Ressource | Indique que l’attribut est sur la ressource, comme un nom de conteneur. | `@Resource` |
> | Requête | Indique que l’attribut fait partie de la demande d’action, comme la définition de la balise d’index de blob. | `@Request` |

Pour obtenir la liste des attributs de blob de stockage que vous pouvez utiliser dans les conditions, consultez [Actions et attributs pour les conditions d’attribution de rôle Azure dans Stockage Azure (préversion)](../storage/common/storage-auth-abac-attributes.md).

## <a name="operators"></a>Opérateurs

Le tableau suivant répertorie les opérateurs disponibles pour construire des conditions.

| Category | Opérateur | Description |
| --- | --- | --- |
| Comparaison logique |`AND`<br/>`&&` | Opérateur AND. |
|  | `OR`<br/>`||` | Opérateur OR. |
|  | `NOT`<br/>`!` | Opérateur NOT ou de négation. |
| Comparaison de chaînes | `StringEquals`<br/>`StringEqualsIgnoreCase` | Correspondance du respect de la casse (ou du non-respect de la casse). Les valeurs doivent correspondre exactement à la chaîne. |
|  | `StringNotEquals`<br/>`StringNotEqualsIgnoreCase` | Opérateur de négation de `StringEquals` (ou `StringEqualsIgnoreCase`) |
|  | `StringStartsWith`<br/>`StringStartsWithIgnoreCase` | Correspondance du respect de la casse (ou du non-respect de la casse). Les valeurs commencent par la chaîne. |
|  | `StringNotStartsWith`<br/>`StringNotStartsWithIgnoreCase` | Opérateur de négation de `StringStartsWith` (ou `StringStartsWithIgnoreCase`) |
|  | `StringLike`<br/>`StringLikeIgnoreCase` | Correspondance du respect de la casse (ou du non-respect de la casse). Les valeurs peuvent inclure un caractère générique de correspondance à plusieurs caractères (`*`) ou un caractère générique de correspondance à un seul caractère (`?`) n’importe où dans la chaîne. Le cas échéant, ces caractères peuvent être placés dans une séquence d’échappement en ajoutant une barre oblique inverse : `\*` et `\?`. |
|  | `StringNotLike`<br/>`StringNotLikeIgnoreCase` | Opérateur de négation de `StringLike` (ou `StringLikeIgnoreCase`) |
| Comparaison numérique | `NumericEquals`<br/>`NumericNotEquals`<br/>`NumericLessThan`<br/>`NumericLessThanEquals`<br/>`NumericGreaterThan`<br/>`NumericGreaterThanEquals` | Actuellement, seuls les entiers sont pris en charge. |
| Fonctions de niveau supérieur | `ActionMatches` | Vérifie si la valeur d’Action[ID] correspond au modèle d’action spécifié. Cet opérateur équivaut à la logique de correspondance d’action que le Kit de développement logiciel (SDK) utilise pour comparer une action à un modèle d’action dans une autorisation. |
| Comparaison entre produits | `ForAnyOfAnyValues:StringEquals`<br/>`ForAnyOfAnyValues:StringEqualsIgnoreCase`<br/>`ForAnyOfAnyValues:StringNotEquals`<br/>`ForAnyOfAnyValues:StringNotEqualsIgnoreCase`<br/>`ForAnyOfAnyValues:StringLike`<br/>`ForAnyOfAnyValues:StringLikeIgnoreCase`<br/>`ForAnyOfAnyValues:StringNotLike`<br/>`ForAnyOfAnyValues:StringNotLikeIgnoreCase`<br/>`ForAnyOfAnyValues:NumericEquals`<br/>`ForAnyOfAnyValues:NumericNotEquals`<br/>`ForAnyOfAnyValues:NumericGreaterThan`<br/>`ForAnyOfAnyValues:NumericGreaterThanEquals`<br/>`ForAnyOfAnyValues:NumericLessThan`<br/>`ForAnyOfAnyValues:NumericLessThanEquals` | Si au moins une valeur du côté gauche satisfait la comparaison avec au moins une valeur du côté droit, alors l’expression prend la valeur true. Est au format : `ForAnyOfAnyValues:<BooleanFunction>`. Prend en charge plusieurs chaînes et nombres. |
|  | `ForAllOfAnyValues:StringEquals`<br/>`ForAllOfAnyValues:StringEqualsIgnoreCase`<br/>`ForAllOfAnyValues:StringNotEquals`<br/>`ForAllOfAnyValues:StringNotEqualsIgnoreCase`<br/>`ForAllOfAnyValues:StringLike`<br/>`ForAllOfAnyValues:StringLikeIgnoreCase`<br/>`ForAllOfAnyValues:StringNotLike`<br/>`ForAllOfAnyValues:StringNotLikeIgnoreCase`<br/>`ForAllOfAnyValues:NumericEquals`<br/>`ForAllOfAnyValues:NumericNotEquals`<br/>`ForAllOfAnyValues:NumericGreaterThan`<br/>`ForAllOfAnyValues:NumericGreaterThanEquals`<br/>`ForAllOfAnyValues:NumericLessThan`<br/>`ForAllOfAnyValues:NumericLessThanEquals` | Si chaque valeur du côté gauche satisfait à la comparaison avec au moins une valeur du côté droit, alors l’expression prend la valeur true. Est au format : `ForAllOfAnyValues:<BooleanFunction>`. Prend en charge plusieurs chaînes et nombres. |
|  | `ForAnyOfAllValues:StringEquals`<br/>`ForAnyOfAllValues:StringEqualsIgnoreCase`<br/>`ForAnyOfAllValues:StringNotEquals`<br/>`ForAnyOfAllValues:StringNotEqualsIgnoreCase`<br/>`ForAnyOfAllValues:StringLike`<br/>`ForAnyOfAllValues:StringLikeIgnoreCase`<br/>`ForAnyOfAllValues:StringNotLike`<br/>`ForAnyOfAllValues:StringNotLikeIgnoreCase`<br/>`ForAnyOfAllValues:NumericEquals`<br/>`ForAnyOfAllValues:NumericNotEquals`<br/>`ForAnyOfAllValues:NumericGreaterThan`<br/>`ForAnyOfAllValues:NumericGreaterThanEquals`<br/>`ForAnyOfAllValues:NumericLessThan`<br/>`ForAnyOfAllValues:NumericLessThanEquals` | Si au moins une valeur du côté gauche satisfait à la comparaison avec chaque valeur du côté droit, alors l’expression prend la valeur true. Est au format : `ForAnyOfAllValues:<BooleanFunction>`. Prend en charge plusieurs chaînes et nombres. |
|  | `ForAllOfAllValues:StringEquals`<br/>`ForAllOfAllValues:StringEqualsIgnoreCase`<br/>`ForAllOfAllValues:StringNotEquals`<br/>`ForAllOfAllValues:StringNotEqualsIgnoreCase`<br/>`ForAllOfAllValues:StringLike`<br/>`ForAllOfAllValues:StringLikeIgnoreCase`<br/>`ForAllOfAllValues:StringNotLike`<br/>`ForAllOfAllValues:StringNotLikeIgnoreCase`<br/>`ForAllOfAllValues:NumericEquals`<br/>`ForAllOfAllValues:NumericNotEquals`<br/>`ForAllOfAllValues:NumericGreaterThan`<br/>`ForAllOfAllValues:NumericGreaterThanEquals`<br/>`ForAllOfAllValues:NumericLessThan`<br/>`ForAllOfAllValues:NumericLessThanEquals` | Si chaque valeur du côté gauche satisfait à la comparaison avec chaque valeur du côté droit, alors l’expression prend la valeur true. Est au format : `ForAllOfAllValues:<BooleanFunction>`. Prend en charge plusieurs chaînes et nombres. |

## <a name="operator-examples"></a>Exemples d’opérateurs

> [!div class="mx-tableFixed"]
> | Exemple | Résultats |
> | --- | --- |
> | `ActionMatches{'Microsoft.Authorization/roleAssignments/*'}` | Si l’action en cours de vérification équivaut à « Microsoft.Authorization/roleAssignments/write », alors true |
> | `ActionMatches{'Microsoft.Authorization/roleDefinitions/*'}` | Si l’action en cours de vérification équivaut à « Microsoft.Authorization/roleAssignments/write », alors false |
> | `Resource[name1] StringLike 'a*c?'` | Si Resource[name1] équivaut à « abcd », alors true |
> | `Resource[name1] StringLike 'A*C?'` | Si Resource[name1] équivaut à « abcd », alors false |
> | `Resource[name1] StringLike 'a*c'` | Si Resource[name1] équivaut à « abcd », alors false |
> | `{'red', 'blue'} ForAnyOfAnyValues:StringEquals {'blue', 'green'}` | true |
> | `{'red', 'blue'} ForAnyOfAnyValues:StringEquals {'orange', 'green'}` | false |
> | `{'red', 'blue'} ForAllOfAnyValues:StringEquals {'orange', 'red', 'blue'}` | true |
> | `{'red', 'blue'} ForAllOfAnyValues:StringEquals {'red', 'green'}` | false |
> | `{10, 20} ForAnyOfAllValues:NumericLessThan {15, 18}` | true |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {5, 15, 18}` | false |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {25, 30}` | true |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {15, 25, 30}` | false |

## <a name="special-characters"></a>Caractères spéciaux

| Caractère | Description |
| --- | --- |
| `*` | Un astérisque (*) représente une correspondance de caractère générique à plusieurs caractères qui peut être utilisée avec les opérateurs `Like`. Le cas échéant, vous pouvez placer un astérisque dans une séquence d’échappement en ajoutant une barre oblique inverse : `\*`. |
| `?` | Un point d’interrogation (?) représente une correspondance de caractère générique à un seul caractère qui peut être utilisée avec les opérateurs `Like`. Le cas échéant, vous pouvez placer un point d’interrogation dans une séquence d’échappement en ajoutant une barre oblique inverse : `\?`. |
| `$` | Un signe de dollar ($) est utilisé pour aider à délimiter les clés de balises. Dans Azure PowerShell, si une chaîne placée entre guillemets doubles (") comprend un signe de dollar, vous devez la faire précéder d’un accent grave (\`). Par exemple : ``tags:Project<`$key_case_sensitive`$>``. | 

## <a name="grouping-and-precedence"></a>Regroupement et priorité

Vous utilisez des parenthèses `()` pour regrouper des expressions et définir la priorité dans une condition. Si vous avez au moins trois expressions pour une action ciblée, vous devez ajouter des parenthèses pour définir l’ordre dans lequel les expressions sont évaluées. Les expressions entre parenthèses ont une priorité plus élevée. Prenons l’exemple de l’expression suivante :

```
a AND b OR c
```

Vous devez ajouter des parenthèses de l’une des manières suivantes :

```
(a AND b) OR c
```

```
a AND (b OR c)
```

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de conditions d’attribution de rôle Azure (préversion)](../storage/common/storage-auth-abac-examples.md)
- [Actions et attributs pour les conditions d’attribution de rôle Azure dans Stockage Azure (préversion)](../storage/common/storage-auth-abac-attributes.md)
