---
title: Jeux de données de la fonctionnalité de mappage de Data Flow d’Azure Data Factory
description: Azure Data Factory mappage de flux de données a sepecific dataset compatibilité
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ad6cfdad519ab3901c58979970ea07439b3106e9
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726921"
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

## <a name="delimited-text-dataset"></a>Jeu de données de texte délimité

Dans le jeu de données de texte délimité, vous définirez le délimiteur pour gérer deux délimiteurs uniques ('\t 'for TSV,',' pour le volume partagé de cluster, ' |'...) ou utiliser plusieurs caractères de délimiteur. Définissez la bascule de ligne d’en-tête et avant d’accéder à la transformation Source pour détecter automatiquement les types de données.

## <a name="next-steps"></a>Étapes suivantes

Commencez par [création d’un nouveau flux de données](data-flow-create.md) et ajoutez une Transformation Source. Configurez ensuite le jeu de données pour votre Source.

Utilisez le [activité de copie](copy-activity-overview.md) source de données pour afficher des données à partir de n’importe quel ADF et organiser dans ADLS ou un objet Blob pour l’accès par le flux de données.

