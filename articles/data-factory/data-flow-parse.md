---
title: Transformation d’analyse des données dans le flux de données de mappage
description: Analyser des documents à colonnes incorporées
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: a81676b9985b134a8214f7f5f97d85d27259b71f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416512"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Transformation d’analyse dans un flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilisez la transformation Analyse pour analyser les colonnes de vos données qui sont sous forme de documents. Les types de documents incorporés actuellement pris en charge et qui peuvent être analysés sont JSON et Texte délimité.

## <a name="configuration"></a>Configuration

Dans le panneau de configuration de la transformation d’analyse, vous devez d’abord choisir le type de données contenu dans les colonnes que vous souhaitez analyser inlined. La transformation d’analyse contient également les paramètres de configuration suivants.

![Paramètres d’analyse](media/data-flow/data-flow-parse-1.png "Analyser")

### <a name="column"></a>Colonne

À l’instar des colonnes dérivées et des agrégats, vous pouvez modifier une colonne existante en la sélectionnant dans le sélecteur de liste déroulante. Vous pouvez également saisir le nom d’une nouvelle colonne ici. ADF stocke les données sources analysées dans cette colonne.

### <a name="expression"></a>Expression

Utilisez le générateur d’expressions pour définir la source de votre analyse. Cela peut être aussi simple que de sélectionner la colonne source avec les données autonomes que vous souhaitez analyser, ou vous pouvez créer des expressions complexes à analyser.

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
                format: 'json',
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
