---
title: Transformation d’analyse des données dans le flux de données de mappage
description: Analyser des documents à colonnes incorporées
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/10/2021
ms.openlocfilehash: 7a01d2d17a4c98656588530f5b288c6a69b8a206
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109734159"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Transformation d’analyse dans un flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilisez la transformation Analyse pour analyser les colonnes de vos données qui sont sous forme de documents. Les types de documents incorporés actuellement pris en charge et qui peuvent être analysés sont JSON, XML et Texte délimité.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Configuration

Dans le panneau de configuration de la transformation d’analyse, vous devez d’abord choisir le type de données contenu dans les colonnes que vous souhaitez analyser inlined. La transformation d’analyse contient également les paramètres de configuration suivants.

![Paramètres d’analyse](media/data-flow/data-flow-parse-1.png "Analyser")

### <a name="column"></a>Colonne

À l’instar des colonnes dérivées et des agrégats, vous pouvez modifier une colonne existante en la sélectionnant dans le sélecteur de liste déroulante. Vous pouvez également saisir le nom d’une nouvelle colonne ici. ADF stocke les données sources analysées dans cette colonne. Dans la plupart des cas, vous pouvez définir une nouvelle colonne qui analyse le champ du document incorporé entrant.

### <a name="expression"></a>Expression

Utilisez le générateur d’expressions pour définir la source de votre analyse. Cela peut être aussi simple que de sélectionner la colonne source avec les données autonomes que vous souhaitez analyser, ou vous pouvez créer des expressions complexes à analyser.

#### <a name="example-expressions"></a>Exemples d’expressions

* Données de la chaîne source : ```chrome|steel|plastic```
  * Expression : ```(desc1 as string, desc2 as string, desc3 as string)```

* Données JSON sources : ```{"ts":1409318650332,"userId":"309","sessionId":1879,"page":"NextSong","auth":"Logged In","method":"PUT","status":200,"level":"free","itemInSession":2,"registration":1384448}```
  * Expression : ```(level as string, registration as long)```

* Données XML sources : ```<Customers><Customer>122</Customer><CompanyName>Great Lakes Food Market</CompanyName></Customers>```
  * Expression : ```(Customers as (Customer as integer, CompanyName as string))```

### <a name="output-column-type"></a>Type de colonne de sortie

Ici, vous allez configurer le schéma de sortie cible à partir de l’analyse qui sera écrite dans une seule colonne.

![Exemple d’analyse](media/data-flow/data-flow-parse-2.png "Exemple d’analyse")

Dans cet exemple, nous avons défini l’analyse du champ entrant « jsonString », qui est du texte brut, mais formaté selon une structure JSON. Nous allons stocker les résultats analysés au format JSON dans une nouvelle colonne appelée « json » suivant ce schéma :

```(trade as boolean, customers as string[])```

Pour vérifier que votre sortie est correctement mappée, reportez-vous à l’onglet Inspection et à l’aperçu des données.

## <a name="examples"></a>Exemples

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

### <a name="examples"></a>Exemples

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json|XML|delimited',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Étapes suivantes

* Utilisez la [transformation d’aplatissement](data-flow-flatten.md) pour convertir des lignes en colonnes.
* Utilisez la [transformation de colonne dérivée](data-flow-derived-column.md) pour convertir des colonnes en lignes.
