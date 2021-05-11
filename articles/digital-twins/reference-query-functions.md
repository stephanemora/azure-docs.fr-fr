---
title: Informations de référence sur le langage de requête Azure Digital Twins - Fonctions
titleSuffix: Azure Digital Twins
description: Documentation de référence sur les fonction du langage de requête Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 03/22/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 80b32907499d3c8588a7a565972f49923abbe5c7
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108296373"
---
# <a name="azure-digital-twins-query-language-reference-functions"></a>Informations de référence sur le langage de requête Azure Digital Twins - Fonctions

Ce document contient des informations de référence sur les **fonctions** du [langage de requête Azure Digital Twins](concepts-query-language.md).

## <a name="endswith"></a>ENDSWITH

Fonction de chaîne utilisée pour déterminer si une chaîne donnée se termine par une autre chaîne. 

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="EndsWithSyntax":::

### <a name="arguments"></a>Arguments

* `<string-to-check>`: chaîne permettant de vérifier la fin de
* `<ending-string>` : chaîne représentant la fin à vérifier pour

### <a name="returns"></a>Retours

Valeur booléenne indiquant si la première expression de chaîne se termine par la seconde.

### <a name="example"></a>Exemple

La requête suivante renvoie tous les jumeaux numériques dont les ID se terminent dans `-small`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="EndsWithExample":::

## <a name="is_defined"></a>IS_DEFINED

Fonction de vérification et de conversion de type pour vérifier si une propriété est définie.

Elle est uniquement prise en charge lorsque la valeur de la propriété correspond à un type primitif. Exemples de types primitifs : chaîne, booléen, numérique ou `null`. `DateTime`, les types d’objets et les tableaux ne sont pas pris en charge.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsDefinedSyntax":::

### <a name="arguments"></a>Arguments

`<property>`, propriété pour déterminer si elle est définie. La propriété doit être de type primitif.

### <a name="returns"></a>Retours

Valeur booléenne indiquant si une valeur a été attribuée à la propriété.

### <a name="example"></a>Exemple

La requête suivante renvoie tous les jumeaux numériques dotés d’une propriété *Emplacement*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsDefinedExample":::

## <a name="is_of_model"></a>IS_OF_MODEL

Fonction de vérification et de conversion de type pour déterminer si un jumeau relève d’un type de modèle spécifique. Comprend les modèles qui héritent du modèle spécifié.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsOfModelSyntax":::

### <a name="arguments"></a>Arguments

Requis :
* `<model-ID>`: ID de modèle à vérifier.

Facultatif :
* `<twin-collection>` : spécifier une collection de jumeaux à rechercher lorsqu’il en existe plusieurs (par exemple, lorsqu’un `JOIN` est utilisé).
* `exact`: requiert une correspondance exacte. Si ce paramètre n’est pas défini, le jeu de résultats inclut des jumeaux avec modèles héritant du modèle spécifié.

### <a name="returns"></a>Retours

Valeur booléenne indiquant si le jumeau spécifié correspond au type de modèle spécifié.

### <a name="example"></a>Exemple

La requête suivante renvoie des jumeaux issus de la collection DT correspondant exactement au type de modèle `dtmi:example:room;1`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsOfModelExample":::

## <a name="is_bool"></a>IS_BOOL

Fonction de vérification et de conversion de type pour déterminer si une expression a une valeur booléenne.

Cette fonction est souvent associée à d’autres prédicats si le programme qui traite les résultats de la requête requiert une valeur booléenne et que vous souhaitez exclure les cas où la propriété ne correspond pas à une valeur booléenne.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolSyntax":::

### <a name="arguments"></a>Arguments

`<expression>`, expression pour vérifier s’il s’agit d’une valeur booléenne.

### <a name="returns"></a>Retours

Valeur booléenne indiquant si l’expression spécifiée est du type booléen.

### <a name="example"></a>Exemple

La requête suivante sélectionne les jumeaux numériques dotés d’une propriété `HasTemperature` booléenne.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolExample":::

La requête suivante s’appuie sur l’exemple ci-dessus pour sélectionner les jumeaux numériques dotés d’une propriété `HasTemperature` booléenne, et la valeur de cette propriété n’est pas `false` .

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolNotFalseExample":::

## <a name="is_number"></a>IS_NUMBER

Fonction de vérification et de conversion de type pour déterminer si une expression a une valeur de nombre.

Cette fonction est souvent associée à d’autres prédicats si le programme qui traite les résultats de la requête requiert une valeur de nombre et que vous souhaitez exclure les cas où la propriété ne correspond pas à une valeur de nombre.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNumberSyntax":::

### <a name="arguments"></a>Arguments

`<expression>`, expression pour vérifier s’il s’agit d’un nombre.

### <a name="returns"></a>Retours

Valeur booléenne indiquant si l’expression spécifiée est du type nombre.

### <a name="example"></a>Exemple

La requête suivante sélectionne les jumeaux numériques dotés d’une propriété `Capacity` numérique et sa valeur n’est pas égale à 0.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNumberExample":::

## <a name="is_string"></a>IS_STRING

Fonction de vérification et de conversion de type pour déterminer si une expression a une valeur de chaîne. 

Cette fonction est souvent associée à d’autres prédicats si le programme qui traite les résultats de la requête requiert une valeur de chaîne et que vous souhaitez exclure les cas où la propriété ne correspond pas à une valeur de chaîne.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsStringSyntax":::

### <a name="arguments"></a>Arguments

`<expression>`, expression pour vérifier s’il s’agit d’une chaîne.

### <a name="returns"></a>Retours

Valeur booléenne indiquant si l’expression spécifiée est du type chaîne.

### <a name="example"></a>Exemple

La requête suivante sélectionne les jumeaux numériques dotés d’une propriété `Status` de propriété de chaîne et sa valeur n’est pas égale à `Completed`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsStringExample":::

## <a name="is_null"></a>IS_NULL

Fonction de vérification et de conversion de type pour déterminer si la valeur d’une expression est `null`.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNullSyntax":::

### <a name="arguments"></a>Arguments

`<expression>`, expression pour vérifier si elle est null.

### <a name="returns"></a>Retours

Valeur booléenne indiquant si l’expression spécifiée est de type `null`.

### <a name="example"></a>Exemple

La requête suivante renvoie des jumeaux qui n’ont pas de valeur null pour la température. Pour plus d’informations sur l'`NOT` opérateur utilisé dans cette requête, consultez [Informations de référence sur le langage de requête Azure Digital Twins : opérateurs](reference-query-operators.md#logical-operators).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNullExample":::

## <a name="is_primitive"></a>IS_PRIMITIVE

Fonction de vérification et de vérification de type pour déterminer si la valeur d’une expression est de type primitif (chaîne, booléenne, numérique ou `null` ).

Cette fonction est souvent associée à d’autres prédicats si le programme qui traite les résultats de la requête requiert une valeur de type primitif et que vous souhaitez exclure les cas où la propriété ne correspond pas à une valeur de type primitif.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsPrimitiveSyntax":::

### <a name="arguments"></a>Arguments

`<expression>`, expression pour vérifier si elle est de type primitif.

### <a name="returns"></a>Retours

Valeur booléenne indiquant si l’expression spécifiée est de type primitif (chaîne, booléenne, numérique ou `null`).

### <a name="example"></a>Exemple

La requête suivante renvoie la propriété `area` de la fabrique avec l’ID « ABC » uniquement si la propriété `area` est de type primitif. Pour plus d’informations sur la projection de certaines colonnes du résultat de la requête (par exemple, cette requête avec `area` ), consultez [Informations de référence sur le langage de requête Azure Digital Twins : clause SELECT](reference-query-clause-select.md#select-columns-with-projections).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsPrimitiveExample":::

## <a name="is_object"></a>IS_OBJECT

Fonction de vérification et de conversion de type pour déterminer si la valeur d’une expression correspond à un type d’objet JSON.

Cette fonction est souvent associée à d’autres prédicats si le programme qui traite les résultats de la requête requiert un objet JSON et que vous souhaitez exclure les cas où la valeur ne correspond pas à un objet JSON.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsObjectSyntax":::

### <a name="arguments"></a>Arguments

`<expression>`, expression pour vérifier s’il s’agit d’un type d’objet.

### <a name="returns"></a>Retours

Valeur booléenne indiquant si l’expression spécifiée est du type objet JSON.

### <a name="example"></a>Exemple

La requête suivante sélectionne tous les jumeaux numériques présentant un objet appelé `MapObject` et non dotés d’une propriété enfant `TemperatureReading`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsObjectExample":::

## <a name="startswith"></a>STARTSWITH

Fonction de chaîne utilisée pour déterminer si une chaîne donnée commence par une autre chaîne. 

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="StartsWithSyntax":::

### <a name="arguments"></a>Arguments

* `<string-to-check>` : chaîne permettant de vérifier le début de
* `<beginning-string>` : chaîne représentant le début à vérifier pour

### <a name="returns"></a>Retours

Valeur booléenne indiquant si la première expression de chaîne commence par la seconde

### <a name="example"></a>Exemple

La requête suivante renvoie tous les jumeaux numériques dont les ID commencent par `area1-`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="StartsWithExample":::
