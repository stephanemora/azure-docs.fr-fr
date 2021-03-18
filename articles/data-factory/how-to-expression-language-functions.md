---
title: Guide pratique pour utiliser des paramètres et des expressions dans Azure Data Factory
description: Cet article sur les procédures fournit des informations sur les expressions et fonctions que vous pouvez utiliser pour la création d’entités de fabrique de données.
author: ssabat
ms.author: susabat
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: 8f22645eafa0969eac3d6c4c0645909f8c650cad
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199814"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Guide pratique pour utiliser des paramètres, des expressions et des fonctions dans Azure Data Factory

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [Version actuelle](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dans ce document, nous allons essentiellement nous concentrer sur les concepts fondamentaux avec divers exemples pour explorer la possibilité de créer des pipelines de données paramétrisables dans Azure Data Factory. La paramétrisation et les expressions dynamiques sont des ajouts notables pour ADF, car ils permettent d’économiser beaucoup de temps et de tenir compte d’une solution ETL (extraction, transformation, chargement) ou ELT (extraction, chargement, transformation) bien plus souple, ce qui réduit considérablement le coût de maintenance des solutions et accélère l’implémentation de nouvelles fonctionnalités dans les pipelines existants. Ces gains sont liés au fait que la paramétrisation réduit la quantité de codage en dur et augmente le nombre d’objets et de processus réutilisables dans une solution.

## <a name="azure-data-factory-ui-and-parameters"></a>Interface utilisateur et paramètres Azure Data Factory

Si vous débutez avec l’utilisation des paramètres Azure Data Factory dans l’interface utilisateur ADF, consultez [Interface utilisateur Data Factory pour les services liés avec des paramètres](https://docs.microsoft.com/azure/data-factory/parameterize-linked-services#data-factory-ui) et [Interface utilisateur Data Factory pour le pipeline piloté par les métadonnées avec des paramètres](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization#data-factory-ui) afin d’obtenir une explication visuelle.

## <a name="parameter-and-expression-concepts"></a>Concepts des paramètres et des expressions 

Vous pouvez utiliser des paramètres pour transmettre des valeurs externes dans des pipelines, des jeux de données, des services liés et des flux de données. Une fois que le paramètre a été transmis à la ressource, il ne peut pas être modifié. En paramétrisant des ressources, vous pouvez les réutiliser avec des valeurs différentes chaque fois. Les paramètres peuvent être utilisés individuellement ou dans le cadre d’expressions. Les valeurs JSON indiquées dans la définition peuvent être littérales. Il peut également s’agir d’expressions évaluées lors de l’exécution du runtime.

Par exemple :  
  
```json
"name": "value"
```

 or  
  
```json
"name": "@pipeline().parameters.password"
```

Les expressions peuvent apparaître n’importe où dans une valeur de chaîne JSON, et entraînent toujours une autre valeur JSON. Ici, *password* est un paramètre de pipeline dans l’expression. Si une valeur JSON est une expression, le corps de celle-ci est extrait en supprimant l’arobase (\@). Si une chaîne littérale devant commencer par \@ est nécessaire, elle doit être placée dans une séquence d’échappement en utilisant \@\@. Les exemples suivants montrent comment les expressions sont évaluées.  
  
|Valeur JSON|Résultats|  
|----------------|------------|  
|« parameters »|Les caractères « parameters » sont retournés.|  
|« parameters[1] »|Les caractères « parameters[1] » sont retournés.|  
|"\@\@"|Une chaîne de 1 caractère contenant « \@ » est retournée.|  
|" \@"|Une chaîne de 2 caractères contenant « \@ » est retournée.|  
  
 Des expressions peuvent également apparaître dans des chaînes, qui utilisent une fonctionnalité appelée *interpolation de chaîne* où les expressions sont encapsulées dans `@{ ... }`. Par exemple : `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 En utilisant une interpolation de chaîne, le résultat est toujours une chaîne. Supposons que j’ai défini `myNumber` comme `42` et `myString` comme `foo` :  
  
|Valeur JSON|Résultats|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Retourne `foo` en tant que chaîne.|  
|"\@{pipeline().parameters.myString}"| Retourne `foo` en tant que chaîne.|  
|"\@pipeline().parameters.myNumber"| Retourne `42` en tant que *nombre*.|  
|"\@{pipeline().parameters.myNumber}"| Retourne `42` en tant que *chaîne*.|  
|"Answer is: @{pipeline().parameters.myNumber}"| Retourne la chaîne `Answer is: 42`.|  
|"\@concat(’Answer is: ’, string(pipeline().parameters.myNumber))"| Retourne la chaîne `Answer is: 42`.|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| Retourne la chaîne `Answer is: @{pipeline().parameters.myNumber}`.|  

## <a name="examples-of-using-parameters-in-expressions"></a>Exemples d’utilisation de paramètres dans les expressions 

### <a name="complex-expression-example"></a>Exemple d’expression complexe
L’exemple ci-dessous montre un exemple complexe qui fait référence à un sous-champ profond d’une sortie d’activité. Pour faire référence à un paramètre de pipeline qui prend la valeur d’un sous-champ, utilisez la syntaxe [] au lieu de l’opérateur point(.) (comme dans le cas de subfield1 et subfield2)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Éditeur de contenu dynamique

L’éditeur de contenu dynamique échappe automatiquement des caractères de votre contenu quand vous avez fini de l’éditer. Par exemple, le contenu suivant dans l’éditeur de contenu est une interpolation de chaîne avec deux fonctions d’expression. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

L’éditeur de contenu dynamique convertit le contenu ci-dessus en expression `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"`. Le résultat de cette expression est une chaîne de format JSON illustrée ci-dessous.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>Jeu de données avec des paramètres

Dans l’exemple suivant, BlobDataset prend un paramètre nommé **path**. Sa valeur est utilisée pour donner une valeur à la propriété **folderPath** à l’aide de l’expression : `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with--parameters"></a>Pipeline avec des paramètres

Dans l’exemple suivant, le pipeline prend les paramètres **inputPath** et **outputPath**. Le **path** du jeu de données blob paramétrable est défini par les valeurs de ces paramètres. Voici la syntaxe utilisée : `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```

  
## <a name="calling-functions-within-expressions"></a>Appel de fonctions dans des expressions 

Vous pouvez appeler des fonctions dans des expressions. Les sections suivantes fournissent des informations sur les fonctions qui peut être utilisées dans une expression.  

### <a name="string-functions"></a>Fonctions de chaîne  

Pour travailler avec des chaînes, vous pouvez utiliser ces fonctions de chaîne, ainsi que certaines [fonctions de collection](#collection-functions).
Les fonctions de chaîne sont uniquement utilisables sur des chaînes.

| Fonction de chaîne | Tâche |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Combine au moins deux chaînes et retourne la chaîne combinée. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Vérifier si une chaîne se termine par la sous-chaîne spécifiée. |
| [guid](control-flow-expression-language-functions.md#guid) | Générer un identificateur global unique (GUID) sous forme de chaîne. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Renvoyer la position de départ d’une sous-chaîne. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Retourne la position de départ de la dernière occurrence d’une sous-chaîne. |
| [replace](control-flow-expression-language-functions.md#replace) | Remplacer une sous-chaîne par la chaîne spécifiée et renvoyer la chaîne mise à jour. |
| [split](control-flow-expression-language-functions.md#split) | Retourne un tableau qui contient des sous-chaînes, séparées par des virgules, extraites à partir d’une chaîne plus grande sur la base d’un caractère délimiteur spécifié dans la chaîne d’origine. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Vérifie si une chaîne commence par une sous-chaîne spécifique. |
| [substring](control-flow-expression-language-functions.md#substring) | Renvoyer les caractères d’une chaîne, en commençant à partir de la position spécifiée. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Retourne une chaîne en minuscules. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Retourne une chaîne en majuscules. |
| [trim](control-flow-expression-language-functions.md#trim) | Supprime les espaces blancs de début et de fin d’une chaîne et retourne la chaîne mise à jour. |

### <a name="collection-functions"></a>Fonctions de collection

Pour travailler avec des collections, généralement des tableaux, des chaînes et parfois, des dictionnaires, vous pouvez utiliser ces fonctions de collection.

| Fonction de collection | Tâche |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | Vérifie si une collection contient un élément spécifique. |
| [empty](control-flow-expression-language-functions.md#empty) | Vérifie si une collection est vide. |
| [first](control-flow-expression-language-functions.md#first) | Renvoyer le premier élément d’une collection. |
| [intersection](control-flow-expression-language-functions.md#intersection) | Retourne une collection qui contient *uniquement* les éléments communs aux collections spécifiées. |
| [join](control-flow-expression-language-functions.md#join) | Renvoyer une chaîne qui contient *tous* les éléments d’un tableau, séparés par le caractère spécifié. |
| [last](control-flow-expression-language-functions.md#last) | Retourne le dernier élément d’une collection. |
| [length](control-flow-expression-language-functions.md#length) | Renvoyer le nombre d’éléments d’une chaîne ou d’un tableau. |
| [skip](control-flow-expression-language-functions.md#skip) | Supprime des éléments du début d’une collection et retourne *tous les autres* éléments. |
| [take](control-flow-expression-language-functions.md#take) | Retourne des éléments du début d’une collection. |
| [union](control-flow-expression-language-functions.md#union) | Retourne une collection qui contient *tous* les éléments des collections spécifiées. | 

### <a name="logical-functions"></a>Fonctions logiques  

Ces fonctions sont utiles à l’intérieur de conditions, et permettent d’évaluer tout type de logique.  
  
| Fonction de comparaison logique | Tâche |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Vérifie si toutes les expressions sont vraies. |
| [equals](control-flow-expression-language-functions.md#equals) | Vérifier si les deux valeurs sont équivalentes. |
| [greater](control-flow-expression-language-functions.md#greater) | Vérifie si la première valeur est supérieure à la seconde. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Vérifie si la première valeur est supérieure ou égale à la seconde. |
| [if](control-flow-expression-language-functions.md#if) | Vérifie si une expression est vraie ou fausse. En fonction du résultat, retourne une valeur spécifiée. |
| [less](control-flow-expression-language-functions.md#less) | Vérifie si la première valeur est inférieure à la seconde. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Vérifie si la première valeur est inférieure ou égale à la seconde. |
| [not](control-flow-expression-language-functions.md#not) | Vérifie si une expression est fausse. |
| [or](control-flow-expression-language-functions.md#or) | Vérifie si au moins une expression est vraie. |
  
### <a name="conversion-functions"></a>Fonctions de conversion  

 Ces fonctions permettent de convertir chacun des types natifs du langage :  
-   string
-   entier
-   float
-   boolean
-   tableaux
-   dictionnaires

| Fonction de conversion | Tâche |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Retourne un tableau à partir d’une entrée spécifique unique. Pour des entrées multiples, consultez [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Retourne la version encodée en Base64 d’une chaîne. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Retourne la version binaire d’une chaîne encodée en Base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Renvoyer la version de type chaîne d’une chaîne encodée en base 64. |
| [binary](control-flow-expression-language-functions.md#binary) | Renvoyer la version binaire d’une valeur d’entrée. |
| [bool](control-flow-expression-language-functions.md#bool) | Renvoyer la version booléenne d’une valeur d’entrée. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Retourne la première valeur autre que Null d’un ou plusieurs paramètres. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Retourne un tableau à partir de plusieurs entrées. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Renvoyer l’URI de données d’une valeur d’entrée. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Renvoyer la version binaire d’un URI de données. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Renvoyer la version de type chaîne d’un URI de données. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Renvoyer la version de type chaîne d’une chaîne encodée en base 64. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Renvoyer la version binaire d’un URI de données. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Retourne une chaîne qui remplace les caractères d’échappement par des versions décodées. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Renvoyer une chaîne qui remplace les caractères non sécurisés pour les URL par des caractères d'échappement. |
| [float](control-flow-expression-language-functions.md#float) | Renvoyer un nombre à virgule flottante pour une valeur d’entrée. |
| [int](control-flow-expression-language-functions.md#int) | Retourne la version de type entier d’une chaîne. |
| [json](control-flow-expression-language-functions.md#json) | Retourne la valeur ou l’objet de type JavaScript Object Notation (JSON) d’une chaîne ou d’un élément XML. |
| [string](control-flow-expression-language-functions.md#string) | Renvoyer la version de type chaîne d’une valeur d’entrée. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Renvoyer la version encodée dans un URI d’une valeur d’entrée en remplaçant les caractères non sécurisés pour les URL par des caractères d’échappement. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Renvoyer la version binaire d’une chaîne encodée dans un URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Renvoyer la version de type chaîne d’une chaîne encodée dans un URI. |
| [xml](control-flow-expression-language-functions.md#xml) | Renvoyer la version de type entier d’une chaîne. |
| [xpath](control-flow-expression-language-functions.md#xpath) | Vérifie si le code XML contient des valeurs ou des nœuds qui correspondent à une expression de langage XPath et retourne les valeurs ou les nœuds correspondants. |

### <a name="math-functions"></a>Fonctions mathématiques  
 Ces fonctions peuvent être utilisées pour les deux types de nombre : **entiers** et **flottants**.  

| Fonction mathématique | Tâche |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Retourne le résultat de l’addition de deux nombres. |
| [div](control-flow-expression-language-functions.md#div) | Renvoyer le résultat de la division de deux nombres. |
| [max](control-flow-expression-language-functions.md#max) | Renvoyer la valeur la plus élevée d’un ensemble de nombres ou d’un tableau. |
| [min](control-flow-expression-language-functions.md#min) | Retourne la plus petite valeur d’un ensemble de nombres ou d’un tableau. |
| [mod](control-flow-expression-language-functions.md#mod) | Retourne le reste de la division de deux nombres. |
| [mul](control-flow-expression-language-functions.md#mul) | Retourne le produit de la multiplication de deux nombres. |
| [rand](control-flow-expression-language-functions.md#rand) | Renvoyer un entier aléatoire à partir d’une plage spécifique. |
| [range](control-flow-expression-language-functions.md#range) | Retourne un tableau d’entiers qui commence par un entier spécifique. |
| [sub](control-flow-expression-language-functions.md#sub) | Retourne le résultat de la soustraction du second nombre du premier. |
  
### <a name="date-functions"></a>Fonctions de date  

| Fonction de date ou heure | Tâche |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Ajoute un nombre de jours à un horodatage. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Ajoute un nombre d’heures à un horodatage. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Ajoute un nombre de minutes à un horodatage. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Ajoute un nombre de secondes à un horodatage. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Ajoute un nombre d’unités de temps à un horodatage. Voir aussi [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Convertit un horodatage du temps universel coordonné (UTC) au fuseau horaire cible. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Convertit un horodatage du fuseau horaire source au fuseau horaire cible. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Convertit un horodatage du fuseau horaire source en temps universel coordonné (UTC). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Renvoyer le jour du composant mois d’un timestamp. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Renvoyer le jour du composant semaine d’un timestamp. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Renvoyer le jour du composant année d’un timestamp. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Retourner l’horodatage sous forme de chaîne dans un format facultatif. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Retourne l’horodatage actuel plus les unités de temps spécifiées. Voir aussi [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Retourne l’horodatage actuel moins les unités de temps spécifiées. Voir aussi [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Retourne le début du jour d’un horodatage. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Retourne le début de l’heure d’un horodatage. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Retourne le début du mois pour un horodatage. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Soustrait un nombre d’unités de temps d’un horodatage. Voir aussi [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [ticks](control-flow-expression-language-functions.md#ticks) | Retourne la valeur de la propriété `ticks` pour un horodatage spécifique. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Renvoyer le timestamp actuel sous forme de chaîne. |

## <a name="detailed-examples-for-practice"></a>Exemples détaillés pour vous entraîner

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Pipeline de copie Azure Data Factory détaillé avec paramètres 

Ce [tutoriel sur la transmission de paramètres de pipeline de copie Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) vous montre comment transmettre des paramètres entre un pipeline et une activité ainsi qu’entre les activités.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Pipeline de flux de données de mappage détaillé avec paramètres 

Pour obtenir un exemple complet d’utilisation des paramètres dans le flux de données, suivez [Flux de données de mappage avec paramètres](https://docs.microsoft.com/azure/data-factory/parameters-data-flow).

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Pipeline piloté par les métadonnées détaillé avec paramètres

Pour en savoir plus sur l’utilisation des paramètres afin de concevoir des pipelines pilotés par les métadonnées, suivez [Pipeline piloté par les métadonnées avec paramètres](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization). Il s’agit d’un cas d’usage courant pour les paramètres.


## <a name="next-steps"></a>Étapes suivantes
Pour la liste des variables système que vous pouvez utiliser dans des expressions, voir [variables système](control-flow-system-variables.md).
