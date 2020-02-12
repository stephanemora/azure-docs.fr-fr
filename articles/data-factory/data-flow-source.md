---
title: Transformation de la source d’un mappage de flux de données
description: Découvrez comment configurer une transformation de la source dans le mappage de flux de données.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 128b15bd5b3ba3c3ac891719bf5c3ec8e5137cce
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023512"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformation de la source d’un mappage de flux de données 

Une transformation de la source configure votre source de données pour le flux de données. Lors de la conception de flux de données, la première étape consiste toujours à configurer une transformation de source. Pour ajouter une source, cliquez sur **Ajouter une source** dans le canevas de flux de données.

Chaque flux de données nécessite au moins une transformation de source. Vous pouvez cependant ajouter autant de sources que nécessaire pour effectuer vos transformations de données. Vous pouvez joindre ces sources à l’aide d’une transformation de jointure, de recherche ou d’union.

Un jeu de données Data Factory unique est associé à chaque transformation de source. Le jeu de données définit la forme et l’emplacement des données que vous voulez lire ou écrire. Si le jeu de données est basé sur des fichiers, vous pouvez utiliser des caractères génériques et des listes de fichiers dans votre source pour utiliser plusieurs fichiers à la fois.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Connecteurs source pris en charge dans le flux de données de mappage

Le flux de données de mappage suit une approche basée sur l’extraction, le chargement et la transformation (ELT, extract, load, transform) et fonctionne avec des jeux de données *intermédiaires* qui se trouvent tous dans Azure. Actuellement, les jeux de données suivants peuvent être utilisés dans une transformation de source :
    
* [Stockage Blob Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Les paramètres spécifiques à ces connecteurs se trouvent dans l’onglet **Options de la source**. Vous trouverez des informations sur ces paramètres dans la documentation relative aux connecteurs. 

Azure Data Factory a accès à plus de [90 connecteurs natifs](connector-overview.md). Pour inclure dans votre flux de données des données provenant de ces autres sources, utilisez l’outil Copier l’activité pour charger ces données dans l’une des zones de transit prises en charge.

## <a name="source-settings"></a>Paramètres de la source

Une fois que vous avez ajouté une source, configurez-la à l’aide de l’onglet **Paramètres de la source**. Sous cet onglet, vous pouvez sélectionner ou créer le jeu de données vers lequel pointe la source. Vous pouvez également sélectionner des options de schéma et d’échantillonnage pour vos données.

![Onglet Paramètres de la source](media/data-flow/source1.png "Onglet Paramètres de la source")

**Tester la connexion :** Faites un test pour déterminer si le service Spark du flux de données peut se connecter au service lié utilisé dans votre jeu de données source. Le mode de débogage doit être activé pour que cette fonctionnalité soit activée.

**Dérive de schéma :** La [dérive de schéma](concepts-data-flow-schema-drift.md) est la capacité de la fabrique de données à gérer nativement des schémas flexibles dans vos flux de données sans avoir besoin de définir explicitement des changements de colonnes.

* Cochez la case **Autoriser la dérive de schéma** si les colonnes sources sont amenées à changer souvent. Ce paramètre autorise tous les champs sources entrants à circuler jusqu’au récepteur, par le biais des transformations.

* Si vous cochez la case **Déduire les types de colonnes dérivés**, le système demandera à la fabrique de données de détecter et définir les types de données pour chaque nouvelle colonne découverte. Si cette fonctionnalité est désactivée, toutes les colonnes dérivées seront de type chaîne.

**Valider le schéma :** Quand la case Valider le schéma est cochée, l’exécution du flux de données échoue si les données sources entrantes ne correspondent pas au schéma défini du jeu de données.

**Nombre de lignes à ignorer :** Ce champ spécifie le nombre de lignes à ignorer au début du jeu de données.

**Échantillonnage :** Activez l’échantillonnage pour limiter le nombre de lignes provenant de la source. Utilisez ce paramètre quand vous testez ou échantillonnez des données à partir de votre source à des fins de débogage.

**Lignes multilignes :** sélectionnez l’option des lignes multilignes si votre fichier texte source contient des valeurs de chaîne qui s’étendent sur plusieurs lignes, c’est-à-dire de nouvelles lignes à l’intérieur d’une valeur. Ce paramètre est uniquement disponible dans les jeux de données DelimitedText.

Pour vérifier que votre source est correctement configurée, activez le mode débogage et récupérez un aperçu des données. Pour en savoir plus, consultez [Mode débogage](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quand le mode débogage est activé, la configuration de la limite du nombre de lignes dans les paramètres de débogage remplace le paramètre d’échantillonnage dans la source lors de l’affichage de l’aperçu des données.

## <a name="projection"></a>Projection

Comme les schémas des jeux de données, la projection d’une source définit les colonnes, les types et les formats des données sources. Pour la plupart des types de jeu de données tels que SQL et Parquet, la projection dans une source est corrigée pour refléter le schéma défini dans un jeu de données. Quand vos fichiers sources ne sont pas fortement typés (par exemple, des fichiers CSV plats et non des fichiers Parquet), vous pouvez définir les types de données pour chaque champ de la transformation de source.

![Paramètres de l’onglet Projection](media/data-flow/source3.png "Projection")

Si votre fichier texte ne comporte aucun schéma défini, sélectionnez **Détecter le type de données** afin que Data Factory échantillonne et déduise les types de données. Sélectionnez **Définir le format par défaut** pour détecter automatiquement les formats de données par défaut.

**Réinitialiser le schéma** réinitialise la projection en fonction de ce qui est défini dans le jeu de données référencé.

Vous pouvez modifier les types de données des colonnes lors d’une transformation de colonne dérivée en aval. Utilisez une transformation de sélection pour modifier les noms de colonnes.

### <a name="import-schema"></a>Importer un schéma

Le bouton **Importer un schéma** sous l’onglet **Projection** vous permet d’utiliser un cluster de débogage actif pour créer une projection de schéma. Disponible dans chaque type de source, l’importation du schéma ici remplace la projection définie dans le jeu de données. L’objet de jeu de données ne sera pas changé.

Cela est utile dans les jeux de données tels que Avro et CosmosDB qui prennent en charge des structures de données complexes et qui n’ont pas besoin de définitions de schéma pour exister dans le jeu de données.

## <a name="optimize-the-source-transformation"></a>Optimiser la transformation de la source

Sous l’onglet **Optimiser** de la transformation de la source, vous pouvez voir un type de partition **Source**. Cette option est disponible uniquement quand votre source est Azure SQL Database. En effet, Data Factory tente de rendre les connexions parallèles pour exécuter des requêtes volumineuses sur votre source SQL Database.

![Paramètres de la partition source](media/data-flow/sourcepart3.png "partitionnement")

Vous n’avez pas besoin de partitionner les données de votre source SQL Database, mais les partitions sont utiles pour les requêtes volumineuses. Vous pouvez baser votre partition sur une colonne ou sur une requête.

### <a name="use-a-column-to-partition-data"></a>Utiliser une colonne pour partitionner des données

Dans la table source, sélectionnez une colonne sur laquelle effectuer le partitionnement. Définissez également le nombre de partitions.

### <a name="use-a-query-to-partition-data"></a>Utiliser une requête pour partitionner des données

Vous pouvez choisir de partitionner les connexions en fonction d’une requête. Entrez le contenu d’un prédicat WHERE. Par exemple, entrez year > 1980.

Pour plus d’informations sur l’optimisation dans le mappage de flux de données, consultez la section [Onglet Optimiser](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Étapes suivantes

Commencez à créer une [transformation de colonne dérivée](data-flow-derived-column.md), puis une [transformation de sélection](data-flow-select.md).
