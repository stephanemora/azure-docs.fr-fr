---
title: Jeux de données de la fonctionnalité de mappage de Data Flow d’Azure Data Factory
description: Le Data Flow de mappage Azure Data Factory offre une compatibilité de jeu de données spécifique
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 36ca5e07adf79de77ac4ab4149ff8e96a1dece8d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408746"
---
# <a name="mapping-data-flow-datasets"></a>Jeux de données de Data Flow de mappage

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Les jeux de données sont une construction Data Factory qui définit la forme des données avec lesquelles vous travaillez dans votre pipeline. Dans le Data Flow, les données au niveau des lignes et des colonnes nécessitent une définition de jeu de données à granularité fine. Les jeux de données utilisés dans les pipelines de flux de contrôle ne nécessitent pas la même profondeur de compréhension des données.

Les jeux de données dans les transformations de source de Data Flow et de récepteur sont utilisés pour définir le schéma de données de base. Si vous n’avez pas de schéma dans vos données, vous pouvez définir une dérive de schéma sur pour votre source et votre récepteur. Avec le schéma défini à partir du jeu de données, vous aurez les types de données associés, les formats de données, l’emplacement du fichier et les informations de connexion du service lié associé.

## <a name="dataset-types"></a>Types de jeux de données

Actuellement dans le flux de données, vous trouverez quatre types de jeu de données :

* Base de données SQL Azure
* Azure SQL DW
* Parquet
* Texte délimité

Les jeux de données du flux de données séparent le *type* de source du type de connexion du service lié. En général dans Data Factory, vous choisissez le type de connexion (objet Blob, ADLS, etc.), puis vous définissez le type de fichier dans le jeu de données. Au sein du Data Flow, vous allez choisir les types de sources, qui peuvent être associées à différents types de connexions du service lié.

![Options de transformation source](media/data-flow/dataset1.png "sources")

## <a name="data-flow-compatible-datasets"></a>Jeux de données compatible avec le Data Flow

Lorsque vous créez un nouveau jeu de données, une case à cocher intitulée « Compatible avec le Data Flow » apparaît dans le coin supérieur droit du panneau. Cliquez sur ce bouton pour filtrer uniquement les jeux de données qui peuvent être utilisés avec les flux de données. 

## <a name="import-schemas"></a>Importer des schémas

Lors de l’importation du schéma des jeux de données de Data Flow un bouton Importer un schéma apparaît. Si vous cliquez sur ce bouton, deux options s’offrent à vous : Importer à partir de la source ou importer à partir d’un fichier local. Dans la plupart des cas, vous allez importer le schéma directement à partir de la source. Toutefois, si vous avez un fichier de schéma existant (fichier Parquet ou CSV avec en-têtes), vous pouvez pointer sur ce fichier local et Data Factory définira le schéma en fonction de ce fichier de schéma.

