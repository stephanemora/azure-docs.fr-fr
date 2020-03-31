---
title: Formats de fichier pris en charge dans Azure Data Factory
description: Cette rubrique décrit les formats de fichier et les codes de compression pris en charge par les connecteurs basés sur des fichiers dans Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439567"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Formats de fichier et codecs de compression pris en charge dans Azure Data Factory

*Cet article s’applique aux connecteurs suivants : [Amazon S3](connector-amazon-simple-storage-service.md), [Blob Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Stockage Fichier Azure](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) et [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Vous pouvez utiliser l’[activité de copie](copy-activity-overview.md) pour copier des fichiers en l’état entre deux magasins de données basés sur un fichier, auquel cas les données sont copiées efficacement sans sérialisation ni désérialisation. 

En outre, vous avez la possibilité d’analyser ou de générer des fichiers d’un format donné. Par exemple, vous pouvez effectuer les opérations suivantes :

* Copier des données à partir d’une base de données SQL Server locale et écrire dans Azure Data Lake Storage Gen2 au format Parquet.
* Copier des fichiers au format texte (CSV) à partir d’un système de fichiers local et les écrire dans le stockage d’objets BLOB Azure au format Avro.
* Copier des fichiers compressés à partir d’un système de fichiers local, les décompresser à la volée et écrire les fichiers extraits dans Azure Data Lake Storage Gen2.
* Copier des données au format de texte compressé Gzip (CSV) à partir du stockage Blob Azure et les écrire dans Azure SQL Database.
* Beaucoup d’autres activités qui nécessitent la sérialisation/désérialisation ou la compression/décompression.

## <a name="next-steps"></a>Étapes suivantes

Voir les autres articles relatifs à l’activité de copie :

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Performances de l’activité de copie](copy-activity-performance.md)
