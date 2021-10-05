---
title: Transformation de clé de substitution dans le flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment utiliser la transformation de clé de substitution du flux de données de mappage pour générer des valeurs de clés séquentielles dans Azure Data Factory et Synapse Analytics.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: d125923d676b0adfd280a1882a39cacb74749a82
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059812"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformation de clé de substitution dans le flux de données de mappage 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Utilisez la transformation de clé de substitution pour ajouter une valeur de clé incrémentielle à chaque ligne de données. Cette fonctionnalité est utile pour concevoir des tables de dimension dans un modèle de données analytiques suivant un schéma en étoile. Dans ce type de schéma, chacun des membres des tables de dimension a besoin d’une clé unique non commerciale.

## <a name="configuration"></a>Configuration

:::image type="content" source="media/data-flow/surrogate.png" alt-text="Transformation de clé de substitution":::

**Colonne clé :** nom de la colonne clé de substitution générée.

**Valeur de début :** la valeur de clé la plus faible qui sera générée.

## <a name="increment-keys-from-existing-sources"></a>Incrémenter les clés de sources existantes

Pour démarrer votre séquence à partir d’une valeur qui existe dans une source, nous vous recommandons d’utiliser un récepteur de cache pour enregistrer cette valeur et d’utiliser une transformation de colonne dérivée pour ajouter les deux valeurs ensemble. Utilisez une recherche mise en cache pour obtenir la sortie et l’ajouter à la clé générée. Pour plus d’informations, apprenez-en plus sur les [récepteurs de cache](data-flow-sink.md#cache-sink) et les [recherches mises en cache](concepts-data-flow-expression-builder.md#cached-lookup).

:::image type="content" source="media/data-flow/cached-lookup-example.png" alt-text="Recherche de clé de substitution":::

### <a name="increment-from-existing-maximum-value"></a>Incrémentation à partir d’une valeur maximale existante

La technique à appliquer pour amorcer la valeur de clé avec la valeur maximale précédente dépend de l’emplacement des données sources.

#### <a name="database-sources"></a>Sources de base de données

Utilisez une option de requête SQL pour sélectionner MAX() à partir de votre source. Par exemple, `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

:::image type="content" source="media/data-flow/surrogate-key-max-database.png" alt-text="Requête de clé de substitution":::

#### <a name="file-sources"></a>Sources de fichiers

Si votre valeur maximale précédente se trouve dans un fichier, utilisez la fonction `max()` dans la transformation d’agrégation pour la récupérer :

:::image type="content" source="media/data-flow/surrogate-key-max-file.png" alt-text="Fichier de clé de substitution":::

Dans les deux cas, vous devrez écrire dans un récepteur de cache et rechercher la valeur. 


## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Exemple

:::image type="content" source="media/data-flow/surrogate.png" alt-text="Transformation de clé de substitution":::

Le flux de données correspondant à la configuration de clé de substitution ci-dessus est présenté dans l’extrait de code suivant.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Étapes suivantes

Ces exemples utilisent les transformations [Joindre](data-flow-join.md) et [Colonne dérivée](data-flow-derived-column.md).
