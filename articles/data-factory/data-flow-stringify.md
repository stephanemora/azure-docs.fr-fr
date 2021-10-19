---
title: Stringifier la transformation des données dans le flux de données de mappage
description: Stringifier des types de données complexes
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: e2d0a5993dddccc65109eb623d8f04f11715ac47
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660036"
---
# <a name="stringify-transformation-in-mapping-data-flow"></a>Stringifier la transformation dans un flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilisez la transformation Stringifier pour convertir des types de données complexes en chaînes. Cela peut être très utile lorsque vous devez stocker ou envoyer des données de colonne sous la forme d’une entité chaîne unique qui peut émaner en tant que de type structure, de mappage ou de tableau.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWMTs9]

## <a name="configuration"></a>Configuration

Dans le panneau de configuration de la transformation Stringifier, vous devez commencer par choisir le type des données contenues dans les colonnes que vous souhaitez analyser. La transformation d’analyse contient également les paramètres de configuration suivants.

:::image type="content" source="media/data-flow/stringify.png" alt-text="Paramètres de Stringifier":::

### <a name="column"></a>Colonne

À l’instar des colonnes dérivées et des agrégats, vous pouvez modifier une colonne existante en la sélectionnant dans le sélecteur de liste déroulante. Vous pouvez également saisir le nom d’une nouvelle colonne ici. ADF stocke les données sources de la transformation Stringifier dans cette colonne. Dans la plupart des cas, vous pouvez définir une nouvelle colonne qui stringifie le type de champ complexe entrant.

### <a name="expression"></a>Expression

Utilisez le générateur d’expressions pour définir le champ complexe source à stringifier. Cela peut être aussi simple que de sélectionner la colonne source contenant les données autonomes que vous souhaitez stringifier. Vous pouvez également créer des expressions complexes à analyser.

:::image type="content" source="media/data-flow/stringify-2.png" alt-text="Expressions de transformation Stringifier":::

#### <a name="example-expression"></a>Exemple d’expression

Dans cet exemple, ```body.properties.periods``` est un tableau à l’intérieur d’une structure retournée à partir d’une source REST.

```
body.properties.periods
```

## <a name="data-flow-script"></a>Script de flux de données

```
stringify(mydata = body.properties.periods ? string,
    format: 'json') ~> Stringify1
```

## <a name="next-steps"></a>Étapes suivantes

* Utilisez la [transformation d’aplatissement](data-flow-flatten.md) pour convertir des lignes en colonnes.
* Utilisez la [transformation d’analyse](data-flow-parse.md) pour convertir des types incorporés complexes en colonnes distinctes.
* Utilisez la [transformation de colonne dérivée](data-flow-derived-column.md) pour convertir des colonnes en lignes.
