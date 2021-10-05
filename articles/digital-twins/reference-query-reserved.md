---
title: Informations de référence sur le langage de requête Azure Digital Twins – Mots clés réservés
titleSuffix: Azure Digital Twins
description: Documentation de référence sur les mots clés réservés du langage de requête Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 6ffba46cacf452a4769709b115177bab41d5eca7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838432"
---
# <a name="azure-digital-twins-query-language-reference-reserved-keywords"></a>Informations de référence sur le langage de requête Azure Digital Twins : mots clés réservés

Ce document contient la liste des **mots clés réservés** dans le [langage de requête Azure Digital Twins](concepts-query-language.md). Ces mots ne peuvent être utilisés comme identificateurs dans des requêtes que s’ils sont [placés dans une séquence d’échappement entre crochets doubles](#escaping-reserved-keywords-in-queries). 

## <a name="list-of-reserved-keywords"></a>Liste de mots clés réservés

Les mots clés réservés dans le langage de requête Azure Digital Twins sont les suivants :

* ALL 
* AND
* AS
* ASC
* AVG
* BY
* COUNT
* DESC
* DEVICES_JOBS
* DEVICES_MODULES
* APPAREILS
* ENDS_WITH
* FALSE
* FROM
* GROUP
* IN
* IS_BOOL
* IS_DEFINED
* IS_NULL
* IS_NUMBER
* IS_OBJECT
* IS_PRIMITIVE
* IS_STRING
* MAX
* MIN
* NOT
* NOT_IN
* NULL
* OR
* ORDER
* SELECT
* STARTS_WITH
* SUM
* Haut de la page
* TRUE
* WHERE
* IS_OF_MODEL

## <a name="escaping-reserved-keywords-in-queries"></a>Échappement des mots clés réservés dans les requêtes

Pour utiliser un mot clé réservé comme identificateur dans une requête, placez-le dans une séquence d’échappement en l’entourant de crochets doubles comme suit : `[[<keyword>]]`

Par exemple, considérez un ensemble de jumeaux numériques avec une propriété appelée `GROUP`, qui est un mot clé réservé. Pour filtrer sur cette valeur de propriété, le nom de la propriété doit être placé dans une séquence d’échappement à l’endroit où il est utilisé dans la requête, comme ci-dessous :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ReservedKeywordExample":::