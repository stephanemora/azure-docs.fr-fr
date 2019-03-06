---
title: Copier de façon incrémentielle des données en utilisant Azure Data Factory | Microsoft Docs
description: Ces didacticiels vous montrent comment copier de façon incrémentielle des données d’un magasin de données source dans un magasin de données de destination. Dans le premier, les données sont copiées à partir d’une table.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: cb75e943416c227730589ab5e7feeb7b8ba5e245
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957922"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Charger de façon incrémentielle des données d’un magasin de données source vers un magasin de données de destination

Dans une solution d’intégration de données, le chargement incrémentielle (ou delta) de données après un chargement initial complet de données est un scénario largement utilisé. Les tutoriels de cette section vous montrent différentes méthodes de chargement des données de façon incrémentielle à l’aide d’Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Chargement de données delta à partir d’une base de données à l’aide d’un filigrane
Dans ce cas, vous définissez un filigrane dans votre base de données source. Un filigrane est une colonne incluant une clé d’incrémentation ou l’horodatage de la dernière mise à jour. La solution de chargement delta charge les données modifiées entre un ancien filigrane et un nouveau filigrane. Le flux de travail de cette approche est illustré dans le diagramme suivant : 

![Flux de travail d’utilisation d’un filigrane](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Pour des instructions pas à pas, consultez les didacticiels suivants : 

- [Copier de façon incrémentielle les données d’une table d’Azure SQL Database dans un stockage Blob Azure](tutorial-incremental-copy-powershell.md)
- [Copier de façon incrémentielle des données provenant de plusieurs tables d’une base de données SQL Server locale vers Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Chargement de données delta à partir d’une base de données SQL à l’aide de la technologie Change Tracking
La technologie Change Tracking est une solution légère dans SQL Server et Azure SQL Database qui fournit un mécanisme de suivi des modifications efficace pour les applications. Elle permet à une application d’identifier facilement les données qui ont été insérées, mises à jour ou supprimées. 

Le flux de travail de cette approche est illustré dans le diagramme suivant :

![Flux de travail pour l’utilisation de Change Tracking](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Pour des instructions pas à pas, consultez les didacticiels suivants : <br/>
[Copier de façon incrémentielle des données d’Azure SQL Database dans le stockage Blob Azure à l’aide de la technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Chargement des fichiers nouveaux et modifiés uniquement à l’aide de LastModifiedDate
Vous pouvez commencer par obtenir les métadonnées (LastModifiedDate) de vos fichiers, puis copier les fichiers nouveaux et modifiés uniquement dans le magasin de destination.

Pour des instructions pas à pas, consultez les didacticiels suivants : <br/>
[Copier de façon incrémentielle des fichiers nouveaux et modifiés uniquement en fonction de la valeur LastModifiedDate du Stockage Blob Azure vers le Stockage Blob Azure](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Chargement de nouveaux fichiers uniquement à l’aide d’un nom de dossier ou de fichier partitionné chronologiquement.
Vous pouvez copier les nouveaux fichiers uniquement, où les fichiers ou dossiers ont déjà été partitionnés chronologiquement avec des informations de tranches de temps comme composants du nom de fichier ou de dossier (par exemple, /aaaa/mm/jj/fichier.csv). 

Pour des instructions pas à pas, consultez les didacticiels suivants : <br/>
[Copier de façon incrémentielle de nouveaux fichiers uniquement en fonction d’un nom de dossier ou de fichier partitionné chronologiquement du Stockage Blob Azure vers le Stockage Blob Azure](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Étapes suivantes
Accéder au didacticiel suivant : 

> [!div class="nextstepaction"]
>[Copier de façon incrémentielle les données d’une table d’Azure SQL Database dans un stockage Blob Azure](tutorial-incremental-copy-powershell.md)
