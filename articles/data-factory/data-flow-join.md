---
title: Transformation de jointure (Join) de Data Flow d’Azure Data Factory
description: Transformation de jointure (Join) de Data Flow d’Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 48cf9d58c8acd85e545a5bcb5104d7069670e349
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029326"
---
# <a name="mapping-data-flow-join-transformation"></a>Mappage de la transformation de jointure (Join) de Data Flow



Utilisez la transformation de jointure (Join) pour combiner les données de deux tables dans votre Data Flow. Cliquez sur la transformation qui sera la relation de gauche et ajoutez une transformation de jointure (Join) à partir de la boîte à outils. Dans la transformation de jointure (Join), vous allez sélectionner un autre flux de données à partir de votre flux de données pour qu’il devienne la relation de droite.

![Transformation de jointure](media/data-flow/join.png "Join")

## <a name="join-types"></a>Types de jointure

La sélection du type de jointure est obligatoire pour la transformation de jointure (Join).

### <a name="inner-join"></a>Jointure interne (Inner)

La jointure interne (Inner) passe uniquement dans les lignes qui correspondent aux conditions de colonne des deux tables.

### <a name="left-outer"></a>Externe gauche

Toutes les lignes du flux de gauche ne respectant pas la condition de jointure sont transmises, et les colonnes de sortie de l’autre table sont définies sur NULL en plus de toutes les lignes retournées par la jointure interne.

### <a name="right-outer"></a>Externe droite

Toutes les lignes du flux de droite ne respectant pas la condition de jointure sont transmises, et les colonnes de sortie qui correspondent à l’autre table sont définies sur NULL en plus de toutes les lignes retournées par la jointure interne.

### <a name="full-outer"></a>Externe entière

L’opération Externe entière génère toutes les colonnes et lignes des deux côtés avec des valeurs NULL pour les colonnes qui sont absentes dans l’autre table.

### <a name="cross-join"></a>Jointure croisée

Indiquez le produit croisé des deux flux avec une expression. Vous pouvez utiliser cette opération pour créer des conditions de jointure personnalisées.

## <a name="specify-join-conditions"></a>Spécifier les conditions de jointure

La condition Jointure gauche provient du flux de données connecté à gauche de la jointure. La condition Jointure droite correspond au deuxième flux de données connecté à votre Jointure en bas, correspondant à un connecteur direct vers un autre flux ou à une référence à un autre flux.

Vous devez entrer au moins 1 (1..n) conditions de jointure. Ils peut s’agit de champs qui sont référencés directement, sélectionnés à partir du menu déroulant, ou d’expressions.

## <a name="join-performance-optimizations"></a>Optimisation des performances de jointure

Contrairement à la jointure de fusion (Merge) dans les outils tels que SSIS, la jointure dans ADF Data Flow n’est pas une opération de jointure de fusion obligatoire. Par conséquent, les clés de jointure n’ont pas besoin d’être triées en premier lieu. L’opération de jointure se produira en fonction de l’opération de jointure optimale dans Spark : Jointure de diffusion / côté mappage :

![Optimisation de la transformation de jointure](media/data-flow/joinoptimize.png "Optimisation de la jointure")

Si votre jeu de données peut tenir dans la mémoire de nœud Worker, nous pouvons optimiser vos performances de jointure. Vous pouvez également spécifier le partitionnement de vos données dans l’opération de jointure (Join) pour créer des jeux de données qui s’adaptent mieux à la mémoire par worker.

## <a name="self-join"></a>Jointure réflexive

Vous pouvez obtenir des conditions de jointure réflexive dans ADF Data Flow à l’aide de la transformation de sélection (Select) pour appliquer un alias à un flux existant. Tout d’abord, créez une « nouvelle branche » à partir d’un flux, puis ajoutez une instruction Select pour appliquer un alias à l’intégralité du flux d’origine.

![Jointure réflexive](media/data-flow/selfjoin.png "Jointure réflexive")

Dans le diagramme ci-dessus, la transformation de sélection (Select) se trouve en haut. Cette opération se contente d’applique l’alias « OrigSourceBatting » au flux d’origine. Dans la transformation de jointure (Join) mise en évidence en dessous, vous pouvez voir que nous utilisons ce flux d’alias de sélection (Select) en tant que jointure de droite, ce qui nous permet de faire référence à la même clé du côté gauche et du côté droit de la jointure interne (Inner).

## <a name="composite-and-custom-keys"></a>Clés composites et clés personnalisées

Vous pouvez créer des clés composites et des clés personnalisées à la volée dans la transformation de jointure. Ajoutez des lignes pour les colonnes de jointure supplémentaires avec le signe plus (+) en regard de chaque ligne de relation. Ou bien calculez une nouvelle valeur de clé dans le Générateur d’expressions pour une valeur de jointure à la volée.

## <a name="next-steps"></a>Étapes suivantes

Après avoir joint les données, vous pouvez [créer de nouvelles colonnes](data-flow-derived-column.md) et [réceptionner vos données dans un magasin de données de destination](data-flow-sink.md).
