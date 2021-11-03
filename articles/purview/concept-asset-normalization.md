---
title: Normalisation des ressources
description: Découvrez comment Azure Purview évite la présence de ressources en double dans votre carte de données grâce à la normalisation des ressources
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 07/23/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 470bfa9fc111002dbb7518833d71f918df265b45
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010970"
---
# <a name="asset-normalization"></a>Normalisation des ressources

Lors de l’ingestion de ressources dans le mappage de données Azure Purview, différentes sources mettant à jour la même ressource de données peuvent envoyer des noms qualifiés similaires mais légèrement différents. Bien que ces noms qualifiés représentent la même ressource, de légères différences, comme un caractère supplémentaire ou une casse différente, peuvent faire apparaître différentes ces ressources dans la surface. Pour éviter de stocker les entrées en double et de provoquer de la confusion lors de la consommation du catalogue de données, Azure Purview applique une normalisation lors de l’ingestion afin de garantir que tous les noms complets du même type d’entité sont dans le même format.

Par exemple, vous analysez un objet blob Azure avec le nom qualifié `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet`. Cet objet blob est également consommé par un pipeline Azure Data Factory qui ajoute ensuite des informations de traçabilité à la ressource. Le pipeline ADF peut être configuré pour lire le fichier en tant que `https://myAccount.file.core.windows.net//myshare/folderA/folderB/my-file.parquet`. Si le nom qualifié est différent, ce pipeline ADF consomme le même élément de données. La normalisation garantit que toutes les métadonnées de Stockage Blob Azure et d’Azure Data Factory sont visibles sur une seule ressource, `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet`.

## <a name="normalization-rules"></a>Règles de normalisation

Vous trouverez ci-dessous les règles de normalisation appliquées par Azure Purview.

### <a name="encode-curly-brackets"></a>Encoder les accolades
S’applique à : Toutes les ressources

Avant : `https://myaccount.file.core.windows.net/myshare/{folderA}/folder{B/`

Après : `https://myaccount.file.core.windows.net/myshare/%7BfolderA%7D/folder%7BB/`

### <a name="trim-section-spaces"></a>Supprimer les espaces des sections
S’applique à : Blob Azure, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Data Factory, Azure SQL Database, Azure SQL Database Managed Instance, Pool Azure SQL, Azure Cosmos DB, Recherche cognitive Azure, Azure Data Explorer, Azure Data Share, Amazon S3

Avant : `https://myaccount.file.core.windows.net/myshare/  folder A/folderB   /`

Après : `https://myaccount.file.core.windows.net/myshare/folder A/folderB/`

### <a name="remove-hostname-spaces"></a>Supprimer les espaces des noms d’hôte
S’applique à : Blob Azure, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Database Managed Instance, Pool Azure SQL, Azure Cosmos DB, Recherche cognitive Azure, Azure Data Explorer, Azure Data Share, Amazon S3

Avant : `https://myaccount .file. core.win dows. net/myshare/folderA/folderB/`

Après : `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="remove-square-brackets"></a>Supprimer les crochets 
S’applique à : Azure SQL Database, Azure SQL Database Managed Instance, Pool Azure SQL

Avant : `mssql://foo.database.windows.net/[bar]/dbo/[foo bar]`

Après : `mssql://foo.database.windows.net/bar/dbo/foo%20bar`

> [!NOTE]
> Les espaces entre deux crochets seront encodés

### <a name="lowercase-scheme"></a>Schéma en minuscules
S’applique à : Blob Azure, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Database Managed Instance, Pool Azure SQL, Azure Cosmos DB, Recherche cognitive Azure, Azure Data Explorer, Amazon S3

Avant : `HTTPS://myaccount.file.core.windows.net/myshare/folderA/folderB/`

Après : `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-hostname"></a>Nom d’hôte en minuscules
S’applique à : Blob Azure, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Database Managed Instance, Pool Azure SQL, Azure Cosmos DB, Recherche cognitive Azure, Azure Data Explorer, Amazon S3

Avant : `https://myAccount.file.Core.Windows.net/myshare/folderA/folderB/`

Après : `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-file-extension"></a>Extension de fichier en minuscules
S’applique à : Blob Azure, Azure Files, Azure Data Lake Storage Gen1 Azure Data Lake Storage Gen2, Amazon S3

Avant : `https://myAccount.file.core.windows.net/myshare/folderA/data.TXT`

Après : `https://myaccount.file.core.windows.net/myshare/folderA/data.txt`

### <a name="remove-duplicate-slash"></a>Supprimer les barres obliques en double
S’applique à : Blob Azure, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Data Factory, Azure SQL Database, Azure SQL Database Managed Instance, Pool Azure SQL, Azure Cosmos DB, Recherche cognitive Azure, Azure Data Explorer, Azure Data Share, Amazon S3

Avant : `https://myAccount.file.core.windows.net//myshare/folderA////folderB/`

Après : `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-adf-sections"></a>Sections ADF en minuscules
S’applique à : Azure Data Factory

Avant : `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/fooBar/providers/Microsoft.DataFactory/factories/fooFactory/pipelines/barPipeline/activities/barFoo`

Après : `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/foobar/providers/microsoft.datafactory/factories/foofactory/pipelines/barpipeline/activities/barfoo`

### <a name="convert-to-adl-scheme"></a>Convertir en schéma ADL
S’applique à Azure Data Lake Storage Gen1

Avant : `https://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

Après : `adl://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

## <a name="next-steps"></a>Étapes suivantes

[Analysez un compte Stockage Blob Azure](register-scan-azure-blob-storage-source.md) dans le mappage de données d’Azure Purview. 
