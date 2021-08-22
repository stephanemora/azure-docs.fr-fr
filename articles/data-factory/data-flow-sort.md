---
title: Transformation de tri dans le flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: Transformation du tri des données de mappage d’Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 04/14/2020
ms.openlocfilehash: 88253393820892f20544f5cbf6a83b21e24e8ec8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642065"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Transformation de tri dans le flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La transformation de tri permet de trier les lignes entrantes sur le flux de données actuel. Il est possible de sélectionner les colonnes une par une et de les trier par ordre croissant ou décroissant.

> [!NOTE]
> Les flux de données de mappage s’exécutent sur des clusters Spark qui distribuent les données entre plusieurs nœuds et partitions. Si vous choisissez de repartitionner vos données dans une transformation ultérieure, vous risquez de perdre le tri en raison de ce remaniement. La meilleure façon de conserver l’ordre de tri dans votre flux de données consiste à définir une partition unique sous l’onglet Optimiser de la transformation et à conserver la transformation de tri aussi proche que possible du récepteur.

## <a name="configuration"></a>Configuration

![Paramètres de tri](media/data-flow/sort.png "Trier")

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

![Paramètres de tri](media/data-flow/sort.png "Trier")

Le flux de données correspondant à la configuration de tri ci-dessus est présenté dans l’extrait de code suivant.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Étapes suivantes

Après le tri, vous souhaiterez peut-être utiliser la [transformation d’agrégation](data-flow-aggregate.md)
