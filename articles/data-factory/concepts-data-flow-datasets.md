---
title: Jeux de données de la fonctionnalité de mappage de Data Flow d’Azure Data Factory
description: Azure Data Factory mappage de flux de données a compatibilité du jeu de données spécifique
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: efb82c57a5620ef3eace8b39f6f27f2286202f84
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521837"
---
# <a name="mapping-data-flow-datasets"></a>Jeux de données de Data Flow de mappage

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Les jeux de données sont une construction Data Factory qui définit la forme des données avec lesquelles vous travaillez dans votre pipeline. Dans le Data Flow, les données au niveau des lignes et des colonnes nécessitent une définition de jeu de données à granularité fine. Les jeux de données utilisés dans les pipelines de flux de contrôle ne nécessitent pas la même profondeur de compréhension des données.

Jeux de données dans le flux de données est utilisés dans les transformations de Source et récepteur. Ils sont utilisés pour définir les schémas de base de données. Si vous n’avez pas de schéma dans vos données, vous pouvez définir une dérive de schéma sur pour votre source et votre récepteur. Avec le schéma défini à partir du jeu de données, vous aurez les types de données associés, les formats de données, l’emplacement du fichier et les informations de connexion du service lié associé. Les métadonnées des jeux de données seront affiche dans votre fichier de transformation Source sous la source « Projection ». Le schéma du jeu de données représente le type de données physique et la forme tandis que la projection dans la transformation Source représente la représentation sous forme de flux de données des données avec les types et les noms définis.

## <a name="dataset-types"></a>Types de jeux de données

Actuellement dans le flux de données, vous trouverez quatre types de jeu de données :

* Base de données SQL Azure
* Azure SQL DW
* Parquet (à partir de ADLS et l’objet Blob)
* Texte délimité (à partir de ADLS et l’objet Blob)

Datasets de flux de données distinct du *type de source* à partir de la *type de connexion de Service lié*. En général dans Data Factory, vous choisissez le type de connexion (objet Blob, ADLS, etc.), puis vous définissez le type de fichier dans le jeu de données. Au sein du Data Flow, vous allez choisir les types de sources, qui peuvent être associées à différents types de connexions du service lié.

![Options de transformation source](media/data-flow/dataset1.png "sources")

## <a name="data-flow-compatible-datasets"></a>Jeux de données compatible avec le Data Flow

Lorsque vous créez un nouveau jeu de données, une case à cocher intitulée « Compatible avec le Data Flow » apparaît dans le coin supérieur droit du panneau. Cliquez sur ce bouton pour filtrer uniquement les jeux de données qui peuvent être utilisés avec les flux de données. 

## <a name="import-schemas"></a>Importer des schémas

Lors de l’importation du schéma des jeux de données de Data Flow un bouton Importer un schéma apparaît. Si vous cliquez sur ce bouton, deux options s’offrent à vous : Importer à partir de la source ou importer à partir d’un fichier local. Dans la plupart des cas, vous allez importer le schéma directement à partir de la source. Toutefois, si vous avez un fichier de schéma existant (fichier Parquet ou CSV avec en-têtes), vous pouvez pointer sur ce fichier local et Data Factory définira le schéma en fonction de ce fichier de schéma.

## <a name="create-new-table"></a>Créer une table

Dans le flux de données, vous pouvez demander l’ADF pour créer une nouvelle définition de table dans votre base de données cible en définissant un jeu de données dans la transformation de récepteur qui a un nouveau nom de table. Dans le jeu de données SQL, cliquez sur « Modifier » sous le nom de table et entrez un nouveau nom de table. Puis, dans la Transformation du récepteur, activez « Autoriser les dérives de schéma ». Seth le paramètre « Importer le schéma » None.

![Schéma de Transformation source](media/data-flow/dataset2.png "schéma SQL")

## <a name="choose-your-type-of-data-first"></a>Choisissez tout d’abord de votre type de données

### <a name="delimited-text"></a>Texte délimité

Dans le jeu de données de texte délimité, vous définirez le délimiteur pour gérer deux délimiteurs uniques ('\t 'for TSV,',' pour le volume partagé de cluster, ' |'...) ou utiliser plusieurs caractères de délimiteur. Définissez la bascule de ligne d’en-tête et avant d’accéder à la transformation Source pour détecter automatiquement les types de données. Si vous utilisez un jeu de texte délimité par des données à charger des données dans un récepteur, sélectionnez un dossier cible. Dans les paramètres de récepteur, vous pouvez définir le nom des fichiers de sortie.

### <a name="parquet"></a>Parquet

Utilisez Parquet en tant que le type de jeu de données intermédiaire préféré dans le flux de données ADF. Parquet stockera le schéma de métadonnées riches, ainsi que les données.

### <a name="database-types"></a>Types de base de données

Vous pouvez sélectionner la base de données SQL Azure ou Azure SQL DW.

Pour les autres types de jeu de données ADF, utilisez l’activité de copie à utiliser pour stocker vos données. Il existe un modèle ADF dans la galerie de modèles pour vous aider à créer ce modèle.

![copie intermédiaire](media/data-flow/templatedf.png "copie intermédiaire")

## <a name="choose-your-connection-type"></a>Choisissez votre type de connexion

Si vous utilisez Parquet ou texte délimité par des jeux de données, vous pouvez ensuite sélectionner l’emplacement de vos données : ADLS ou un objet Blob.

## <a name="next-steps"></a>Étapes suivantes

Commencez par [création d’un nouveau flux de données](data-flow-create.md) et ajoutez une Transformation Source. Configurez ensuite le jeu de données pour votre Source.

Utilisez le [activité de copie](copy-activity-overview.md) source de données pour afficher des données à partir de n’importe quel ADF et organiser dans ADLS ou un objet Blob pour l’accès par le flux de données.

