---
title: Transformation de tri dans le flux de données de mappage
description: Apprenez-en davantage sur la transformation de tri des données de mappage dans les pipelines Azure Data Factory et Synapse Analytics.
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 8781836be58403dfef130f55fa698b281360e641
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059879"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Transformation de tri dans le flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

La transformation de tri permet de trier les lignes entrantes sur le flux de données actuel. Il est possible de sélectionner les colonnes une par une et de les trier par ordre croissant ou décroissant.

> [!NOTE]
> Les flux de données de mappage s’exécutent sur des clusters Spark qui distribuent les données entre plusieurs nœuds et partitions. Si vous choisissez de repartitionner vos données dans une transformation ultérieure, vous risquez de perdre le tri en raison de ce remaniement. La meilleure façon de conserver l’ordre de tri dans votre flux de données consiste à définir une partition unique sous l’onglet Optimiser de la transformation et à conserver la transformation de tri aussi proche que possible du récepteur.

## <a name="configuration"></a>Configuration

:::image type="content" source="media/data-flow/sort.png" alt-text="Paramètres de tri":::

**Insensible à la casse :** si vous souhaitez ou non ignorer la casse lors du tri de champs de chaîne ou de texte.

**Trier uniquement dans les partitions :** chaque flux de données est divisé en partitions lors de son exécution sur Spark. Ce paramètre a pour effet de ne trier que les données des partitions entrantes, et non l’intégralité du flux de données. 

**Conditions de tri :** choisissez les colonnes sur lesquelles porte le tri et l’ordre dans lequel il se produit. L’ordre détermine la priorité de tri. Indiquez si les valeurs Null s’affichent ou non au début et à la fin du flux de données.

### <a name="computed-columns"></a>Colonnes calculées

Pour modifier ou extraire une valeur de colonne avant d’appliquer le tri, placez le curseur sur la colonne et sélectionnez « colonne calculée ». Le générateur d’expressions qui s’ouvre permet de créer une expression pour l’opération de tri au lieu d’utiliser une valeur de colonne.

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exemple

:::image type="content" source="media/data-flow/sort.png" alt-text="Paramètres de tri":::

Le flux de données correspondant à la configuration de tri ci-dessus est présenté dans l’extrait de code suivant.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Étapes suivantes

Après le tri, vous souhaiterez peut-être utiliser la [transformation d’agrégation](data-flow-aggregate.md)
