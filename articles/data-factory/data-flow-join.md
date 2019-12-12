---
title: Transformation de jointure dans le flux de données de mappage
description: Combiner les données de deux sources de données à l’aide de la transformation de jointure dans le flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/17/2019
ms.openlocfilehash: 09d2c1d063c542583dc11fab0805a9392661426f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930345"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Transformation de jointure dans le flux de données de mappage

Utilisez la transformation de jointure pour combiner des données de deux sources ou flux dans un flux de données de mappage. Le flux de sortie inclut toutes les colonnes des deux sources correspondantes en fonction d’une condition de jointure. 

## <a name="join-types"></a>Types de jointure

Le mappage de flux de données prend actuellement en charge cinq types de jointures différents.

### <a name="inner-join"></a>Jointure interne (Inner)

La jointure interne génère uniquement les lignes possédant des valeurs correspondantes dans les deux tables.

### <a name="left-outer"></a>Externe gauche

La jointure externe gauche renvoie toutes les lignes du flux de données gauche et les enregistrements correspondants du flux de droite. Si une ligne du flux de gauche n’a pas de correspondance, les colonnes de sortie du flux de droite ont pour valeur NULL. La sortie contient les lignes renvoyées par la jointure interne ainsi que les lignes sans correspondance du flux de gauche.

### <a name="right-outer"></a>Externe droite

La jointure externe droite retourne toutes les lignes du flux de données de droite et les enregistrements correspondants du flux de données de gauche. Si une ligne du flux de droite n’a pas de correspondance, les colonnes de sortie du flux de gauche ont pour valeur NULL. La sortie contient les lignes renvoyées par la jointure interne ainsi que les lignes sans correspondance du flux de droite.

### <a name="full-outer"></a>Externe entière

La jointure externe complète génère toutes les colonnes et les lignes des deux côtés avec des valeurs NULL pour les colonnes sans correspondance.

### <a name="cross-join"></a>Jointure croisée

La jointure croisée génère le produit croisé des deux flux en fonction d’une condition. Si vous utilisez une condition qui n’est pas une égalité, spécifiez une expression personnalisée comme condition de jointure croisée. Le flux de sortie correspond à toutes les lignes qui répondent à la condition de jointure. Pour créer un produit cartésien qui génère chaque combinaison de ligne, spécifiez `true()` comme condition de jointure.

## <a name="configuration"></a>Configuration

1. Dans la liste déroulante **Flux de droite**, choisissez le flux de données que vous joignez.
1. Sélectionner votre **Type de jointure**
1. Choisissez les colonnes clés pour lesquelles vous souhaitez faire correspondre la condition de jointure. Par défaut, le flux de données recherche l’équivalence entre une colonne d’un flux et une colonne de l’autre flux. Pour effectuer une comparaison à l’aide d’une valeur calculée, pointez sur la liste déroulante de la colonne, puis sélectionnez **Colonne calculée**.

![Transformation de jointure (Join)](media/data-flow/join.png "Join")

## <a name="optimizing-join-performance"></a>Optimisation des performances de jointure

Contrairement à la jointure de fusion dans les outils tels que SSIS, la transformation de jointure n’est pas une opération de jointure de fusion obligatoire. Les clés de jointure ne nécessitent pas de tri. L’opération de jointure se produit en fonction de l’opération de jointure optimale dans Spark : soit la jointure de diffusion, soit la jointure côté mappage.

![Optimisation de la transformation de jointure (Join)](media/data-flow/joinoptimize.png "Optimisation de la jointure")

Si l’un des flux de données, voire les deux, s’intègrent à la mémoire du nœud worker, optimisez vos performances en activant **Diffuser** sous l’onglet Optimiser. Vous pouvez également repartitionner vos données sur l’opération de jointure afin qu’elles s’adaptent mieux à la mémoire par Worker.

## <a name="self-join"></a>Jointure réflexive

Pour joindre automatiquement un flux de données à lui-même, associez un alias à un flux existant à l’aide d’une transformation Select. Créez une branche en cliquant sur l’icône plus (+) en regard d’une transformation et en sélectionnant **Nouvelle branche**. Ajoutez une transformation SELECT pour créer un alias du flux d’origine. Ajoutez une transformation de jointure et choisissez le flux d’origine comme **flux de gauche** et la transformation SELECT comme **flux de droite**.

![Jointure réflexive](media/data-flow/selfjoin.png "Jointure réflexive")

## <a name="testing-join-conditions"></a>Test des conditions de jointure

Lorsque vous testez les transformations de jointure avec l’aperçu des données en mode débogage, utilisez un jeu de données connues peu volumineux. Lors de l’échantillonnage de lignes à partir d’un jeu de données volumineux, vous ne pouvez pas prédire quelles lignes et quelles clés seront lues dans le cadre du test. Le résultat n’est pas déterministe, ce qui signifie que vos conditions de jointure peuvent ne renvoyer aucun résultat.

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Exemple de jointure interne

L’exemple ci-dessous illustre une transformation de jointure nommée `JoinMatchedData`, qui utilise le flux de gauche `TripData` et le flux de droite `TripFare`.  La condition de jointure est l’expression `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` qui retourne true si les colonnes `hack_license`, `medallion`, `vendor_id` et `pickup_datetime` de chaque flux correspondent. Le `joinType` est `'inner'`. Nous activons la diffusion uniquement dans le flux de gauche afin que `broadcast` ait pour valeur `'left'`.

Dans l’expérience utilisateur Data Factory, cette transformation se présente comme dans l’image ci-dessous :

![Exemple de jointure](media/data-flow/join-script1.png "Exemple de jointure")

Le script de flux de données pour cette transformation se trouve dans l’extrait de code ci-dessous :

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="cross-join-example"></a>Exemple de jointure croisée

L’exemple ci-dessous illustre une transformation de jointure nommée `CartesianProduct`, qui utilise le flux de gauche `TripData` et le flux de droite `TripFare`. Cette transformation prend deux flux et retourne le produit cartésien de leurs lignes. La condition de jointure est `true()`, car elle génère un produit cartésien complet. Le `joinType` est `cross`. Nous activons la diffusion uniquement dans le flux de gauche afin que `broadcast` ait pour valeur `'left'`.

Dans l’expérience utilisateur Data Factory, cette transformation se présente comme dans l’image ci-dessous :

![Exemple de jointure](media/data-flow/join-script2.png "Exemple de jointure")

Le script de flux de données pour cette transformation se trouve dans l’extrait de code ci-dessous :

```
TripData, TripFare
    join(
        true(),
        joinType:'cross',
        broadcast: 'left'
    )~> CartesianProduct
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir joint les données, créez une [colonne dérivée](data-flow-derived-column.md) et [réceptionnez](data-flow-sink.md) vos données dans un magasin de données de destination.
