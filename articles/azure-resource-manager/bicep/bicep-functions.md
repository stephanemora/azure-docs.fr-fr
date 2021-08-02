---
title: Fonctions Bicep
description: Décrit les fonctions à utiliser dans un fichier Bicep pour récupérer des valeurs, utiliser des chaînes et des valeurs numériques, ainsi que récupérer des informations relatives au déploiement.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: ac8d9660fa198db3da9c5fd3d9770157efd818d6
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025899"
---
# <a name="bicep-functions"></a>Fonctions Bicep

Cet article décrit toutes les fonctions que vous pouvez utiliser dans un fichier Bicep. Pour obtenir une description des sections d’un fichier Bicep, consultez [Comprendre la structure et la syntaxe des fichiers Bicep](./file.md).

La plupart des fonctions opèrent de la même façon quand elles sont déployées sur un groupe de ressources, un abonnement, un groupe d’administration ou un locataire. Certaines fonctions ne peuvent pas être utilisées dans toutes les étendues. Elles sont indiquées dans les listes ci-dessous.

## <a name="any-function"></a>Toutes les fonctions

La [fonction any](./bicep-functions-any.md) est disponible dans bicep pour aider à résoudre les problèmes concernant les avertissements de type de données.

## <a name="array-functions"></a>Fonctions Tableau

Les fonctions suivantes sont disponibles pour l’utilisation de tableaux.

* [array](./bicep-functions-array.md#array)
* [concat](./bicep-functions-array.md#concat)
* [contains](./bicep-functions-array.md#contains)
* [empty](./bicep-functions-array.md#empty)
* [first](./bicep-functions-array.md#first)
* [intersection](./bicep-functions-array.md#intersection)
* [last](./bicep-functions-array.md#last)
* [length](./bicep-functions-array.md#length)
* [min](./bicep-functions-array.md#min)
* [max](./bicep-functions-array.md#max)
* [range](./bicep-functions-array.md#range)
* [skip](./bicep-functions-array.md#skip)
* [take](./bicep-functions-array.md#take)
* [union](./bicep-functions-array.md#union)

## <a name="date-functions"></a>Fonctions de date

Les fonctions suivantes sont disponibles pour l’utilisation de dates.

* [dateTimeAdd](./bicep-functions-date.md#datetimeadd)
* [utcNow](./bicep-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Fonctions de valeur de déploiement

Les fonctions suivantes sont disponibles pour l’obtention de valeurs associées au déploiement :

* [deployment](./bicep-functions-deployment.md#deployment)
* [environment](./bicep-functions-deployment.md#environment)

## <a name="logical-functions"></a>Fonctions logiques

La fonction suivante est disponible pour l’utilisation de conditions logiques :

* [bool](./bicep-functions-logical.md#bool)

## <a name="numeric-functions"></a>Fonctions Numériques

Les fonctions suivantes sont disponibles pour l’utilisation d’entiers :

* [int](./bicep-functions-numeric.md#int)
* [min](./bicep-functions-numeric.md#min)
* [max](./bicep-functions-numeric.md#max)

## <a name="object-functions"></a>Fonctions d’objet

Les fonctions suivantes sont disponibles pour l’utilisation d’objets.

* [contains](./bicep-functions-object.md#contains)
* [empty](./bicep-functions-object.md#empty)
* [intersection](./bicep-functions-object.md#intersection)
* [json](./bicep-functions-object.md#json)
* [length](./bicep-functions-object.md#length)
* [union](./bicep-functions-object.md#union)

## <a name="resource-functions"></a>Fonctions de ressource

Les fonctions suivantes sont disponibles pour l’obtention de valeurs de l’étendue :

* [extensionResourceId](./bicep-functions-resource.md#extensionresourceid)
* [listAccountSas](./bicep-functions-resource.md#list)
* [listKeys](./bicep-functions-resource.md#listkeys)
* [listSecrets](./bicep-functions-resource.md#list)
* [list*](./bicep-functions-resource.md#list)
* [pickZones](./bicep-functions-resource.md#pickzones)
* [reference](./bicep-functions-resource.md#reference)
* [resourceId](./bicep-functions-resource.md#resourceid) - peut être utilisée dans n’importe quelle étendue, mais les paramètres valides changent en fonction de l’étendue.
* [subscriptionResourceId](./bicep-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](./bicep-functions-resource.md#tenantresourceid)

## <a name="scope-functions"></a>Fonctions de limitation

Les fonctions suivantes sont disponibles pour l’obtention de valeurs d’étendue.

* [managementGroup](./bicep-functions-scope.md#managementgroup)
* [resourceGroup](./bicep-functions-scope.md#resourcegroup) - peut être utilisée uniquement dans les déploiements sur un groupe de ressources.
* [subscription](./bicep-functions-scope.md#subscription) - peut être utilisée uniquement dans les déploiements sur un groupe de ressources ou un abonnement.
* [client](./bicep-functions-scope.md#tenant)

## <a name="string-functions"></a>Fonctions de chaînes

Bicep fournit les fonctions suivantes pour travailler avec des chaînes :

* [base64](./bicep-functions-string.md#base64)
* [base64ToJson](./bicep-functions-string.md#base64tojson)
* [base64ToString](./bicep-functions-string.md#base64tostring)
* [concat](./bicep-functions-string.md#concat)
* [contains](./bicep-functions-string.md#contains)
* [dataUri](./bicep-functions-string.md#datauri)
* [dataUriToString](./bicep-functions-string.md#datauritostring)
* [empty](./bicep-functions-string.md#empty)
* [endsWith](./bicep-functions-string.md#endswith)
* [first](./bicep-functions-string.md#first)
* [format](./bicep-functions-string.md#format)
* [guid](./bicep-functions-string.md#guid)
* [indexOf](./bicep-functions-string.md#indexof)
* [last](./bicep-functions-string.md#last)
* [lastIndexOf](./bicep-functions-string.md#lastindexof)
* [length](./bicep-functions-string.md#length)
* [newGuid](./bicep-functions-string.md#newguid)
* [padLeft](./bicep-functions-string.md#padleft)
* [replace](./bicep-functions-string.md#replace)
* [skip](./bicep-functions-string.md#skip)
* [split](./bicep-functions-string.md#split)
* [startsWith](./bicep-functions-string.md#startswith)
* [string](./bicep-functions-string.md#string)
* [substring](./bicep-functions-string.md#substring)
* [take](./bicep-functions-string.md#take)
* [toLower](./bicep-functions-string.md#tolower)
* [toUpper](./bicep-functions-string.md#toupper)
* [trim](./bicep-functions-string.md#trim)
* [uniqueString](./bicep-functions-string.md#uniquestring)
* [uri](./bicep-functions-string.md#uri)
* [uriComponent](./bicep-functions-string.md#uricomponent)
* [uriComponentToString](./bicep-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un fichier Bicep, consultez [Comprendre la structure et la syntaxe des fichiers Bicep](./file.md).
* Pour itérer un nombre de fois spécifié lors de la création d’un type de ressource, consultez [Déployer plusieurs instances de ressources dans Bicep](./loop-resources.md).
* Pour découvrir comment déployer le fichier Bicep que vous avez créé, consultez [Déployer des ressources avec Bicep et Azure PowerShell](./deploy-powershell.md).
