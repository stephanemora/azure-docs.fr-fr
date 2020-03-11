---
title: Transformation d’aplatissement de flux de données de mappage
description: Transformation d’aplatissement de flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 415a093fd8a8fbe27e1d240b061548e18f2ca6b6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164727"
---
# <a name="azure-data-factory-flatten-transformation"></a>Transformation d’aplatissement Azure Data Factory

La transformation d’aplatissement peut être utilisée pour convertir les valeurs de tableau d’une structure hiérarchique en nouvelles lignes, ce qui revient essentiellement à dénormaliser vos données.

![Transformation toolbox](media/data-flow/flatten5.png "Boîte à outils de transformation")

![Flatten transformation 1](media/data-flow/flatten7.png "Transformation d’aplatissement 1")

## <a name="unroll-by"></a>Dérouler par

Tout d’abord, choisissez la colonne de tableau que vous souhaitez dérouler et convertir.

![Flatten transformation settings](media/data-flow/flatten1.png "Paramètres de transformation d’aplatissement")

## <a name="unroll-root"></a>Racine du déroulement

Par défaut, ADF aplatit la structure au niveau du tableau de déroulement que vous avez choisi ci-dessus. Ou bien, vous pouvez choisir une autre partie de la hiérarchie à dérouler. L’option « Racine du déroulement » est un paramètre facultatif.

## <a name="input-columns"></a>Colonnes d'entrée

Enfin, choisissez la projection de votre nouvelle structure en fonction des champs entrants, ainsi que de la colonne normalisée que vous avez déroulée.

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Exemple

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>Étapes suivantes

* Utilisez la [transformation Tableau croisé dynamique](data-flow-pivot.md) pour convertir des lignes en colonnes.
* Utilisez la [transformation de suppression de tableau croisé dynamique](data-flow-unpivot.md) pour convertir des colonnes en lignes.
