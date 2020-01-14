---
title: Transformation du récepteur dans le flux de données de mappage
description: Apprenez à configurer une transformation de récepteur dans le flux de données de mappage.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 1c65a456270cdca345504c07b927a7ef7e1f725b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440261"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformation du récepteur dans le flux de données de mappage

Après avoir transformé vos données, vous pouvez les réceptionner dans un jeu de données de destination. Chaque flux de données requiert la transformation d'au moins un récepteur. Vous pouvez cependant écrire dans autant de récepteurs que nécessaire pour terminer votre flux de transformations. Pour écrire dans des récepteurs supplémentaires, créez de nouveaux flux via de nouvelles branches et des fractionnements conditionnels.

Un jeu de données Data Factory unique est associé à chaque transformation de récepteur. Le jeu de données définit la forme et l'emplacement des données sur lesquelles vous souhaitez écrire.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Connecteurs récepteurs pris en charge dans le flux de données de mappage

Actuellement, les jeux de données suivants peuvent être utilisés dans une transformation de récepteur :
    
* [Stockage Blob Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure Cosmos DB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Les paramètres spécifiques à ces connecteurs se trouvent dans l'onglet **Paramètres**. Vous trouverez des informations sur ces paramètres dans la documentation relative aux connecteurs. 

Azure Data Factory a accès à plus de [90 connecteurs natifs](connector-overview.md). Pour écrire des données sur ces autres sources à partir de votre flux de données, utilisez l'outil Copier l'activité afin de charger ces données à partir de l'une des zones de transit prises en charge une fois votre flux de données terminé.

## <a name="sink-settings"></a>Paramètres de récepteur

Après avoir ajouté un récepteur, configurez-le via l'onglet **Récepteur**. Sous cet onglet, vous pouvez sélectionner ou créer le jeu de données dans lequel votre récepteur écrira 

![Paramètres du récepteur](media/data-flow/sink-settings.png "Paramètres du récepteur")

**Dérive de schéma :** La [dérive de schéma](concepts-data-flow-schema-drift.md) est la capacité de la fabrique de données à gérer nativement des schémas flexibles dans vos flux de données sans avoir besoin de définir explicitement des changements de colonnes. Activez **Autoriser la dérive de schéma** pour écrire des colonnes supplémentaires, en plus de ce qui est défini dans le schéma de données du récepteur.

**Valider le schéma :** Lorsque la case Valider le schéma est cochée, le flux de données échoue si une colonne du schéma défini du jeu de données est introuvable.

## <a name="field-mapping"></a>Mappages de champs

Comme pour une transformation de sélection (Select), l'onglet **Mappage** du récepteur vous permet de choisir les colonnes entrantes dans lesquelles l'écriture interviendra. Par défaut, toutes les colonnes d'entrée, y compris les colonnes dérivées, sont mappées. C'est ce qu'on appelle le **mappage automatique**.

Lorsque vous désactivez le mappage automatique, vous avez la possibilité d'ajouter des mappages basés sur des colonnes ou des mappages basés sur des règles. Les mappages basés sur des règles vous permettent d'écrire des expressions avec critères spéciaux, tandis que le mappage fixe mappe les noms de colonnes logiques et physiques. Pour plus d'informations sur le mappage basé sur des règles, consultez [Modèles de colonne dans le flux de données de mappage](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="data-preview-in-sink"></a>Aperçu des données dans le récepteur

Lors de la récupération (fetch) d'un aperçu des données sur un cluster de débogage, aucune donnée n'est écrite dans votre récepteur. Une capture instantanée des données est renvoyée, mais rien n'est écrit à l'emplacement de destination. Pour tester l'écriture de données dans votre récepteur, exécutez un débogage de pipeline à partir du canevas du pipeline.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé votre flux de données, ajoutez une [activité de flux de données à votre pipeline](concepts-data-flow-overview.md).
