---
title: Formats de fichier pris en charge par l’activité de copie dans Azure Data Factory
description: Cette rubrique décrit les formats de fichier et les codes de compression pris en charge par l’activité de copie dans Azure Data Factory.
author: jianleishen
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jianleishen
ms.openlocfilehash: 7e468b44636662a0654e1ca7770e9df3094da0f6
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110086461"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Formats de fichier et codecs de compression pris en charge par l’activité de copie dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Cet article s’applique aux connecteurs suivants : [Amazon S3](connector-amazon-simple-storage-service.md), [Stockage compatible Amazon S3](connector-amazon-s3-compatible-storage.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [Oracle Cloud Storage](connector-oracle-cloud-storage.md) et [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Vous pouvez utiliser l’[activité de copie](copy-activity-overview.md) pour copier des fichiers en l’état entre deux magasins de données basés sur un fichier, auquel cas les données sont copiées efficacement sans sérialisation ni désérialisation. 

En outre, vous avez la possibilité d’analyser ou de générer des fichiers d’un format donné. Par exemple, vous pouvez effectuer les opérations suivantes :

* Copier des données à partir d'une base de données SQL Server et écrire dans Azure Data Lake Storage Gen2 au format Parquet.
* Copier des fichiers au format texte (CSV) à partir d’un système de fichiers local et les écrire dans le stockage d’objets BLOB Azure au format Avro.
* Copier des fichiers compressés à partir d’un système de fichiers local, les décompresser à la volée et écrire les fichiers extraits dans Azure Data Lake Storage Gen2.
* Copier des données au format de texte compressé Gzip (CSV) à partir du stockage Blob Azure et les écrire dans Azure SQL Database.
* Beaucoup d’autres activités qui nécessitent la sérialisation/désérialisation ou la compression/décompression.

## <a name="next-steps"></a>Étapes suivantes

Voir les autres articles relatifs à l’activité de copie :

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Performances de l’activité de copie](copy-activity-performance.md)
