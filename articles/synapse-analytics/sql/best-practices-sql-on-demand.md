---
title: Meilleures pratiques pour SQL à la demande (préversion) dans Azure Synapse Analytics
description: Recommandations et meilleures pratiques à connaître si vous utilisez SQL à la demande.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426781"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Meilleures pratiques pour SQL à la demande (préversion) dans Azure Synapse Analytics

Cet article présente un ensemble de meilleures pratiques pour l’utilisation de SQL à la demande (préversion). SQL à la demande est une ressource supplémentaire au sein d’Azure Synapse Analytics.

## <a name="general-considerations"></a>Considérations d’ordre général

SQL à la demande vous permet d’interroger des fichiers dans vos comptes de stockage Azure. Il ne dispose pas de capacités de stockage ou d’ingestion locales. Cela qui signifie que tous les fichiers que la requête cible sont externes à SQL à la demande. Tout ce qui est lié à la lecture de fichiers à partir du stockage peut avoir un impact sur les performances des requêtes.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocaliser le compte Stockage Azure et SQL à la demande

Pour réduire la latence, colocalisez votre compte de stockage Azure et votre point de terminaison SQL à la demande. Les comptes de stockage et les points de terminaison approvisionnés lors de la création de l’espace de travail se trouvent dans la même région.

Pour optimiser les performances, si vous accédez à d’autres comptes de stockage avec SQL à la demande, assurez-vous qu’ils se trouvent dans la même région. Autrement, vous obtiendrez une latence accrue pour le transfert réseau des données entre la région distante et la région du point de terminaison.

## <a name="azure-storage-throttling"></a>Limitation de Stockage Azure

Plusieurs applications et services peuvent accéder à votre compte de stockage. Lorsque les IOPS ou le débit combinés générés par des applications, services et charges de travail SQL à la demande dépassent les limites du compte de stockage, une limitation du stockage se produit. Celle-ci a un impact significatif sur les performances des requêtes.

Quand une limitation est détectée, SQL à la demande dispose d’une fonction intégrée pour la gestion de ce scénario. SQL à la demande ralentit la fréquence des demandes adressées au stockage jusqu’à ce que la limitation soit résolue.

> [!TIP]
> Pour optimiser l’exécution des requêtes, vous devez éviter de solliciter le compte de stockage avec d’autres charges de travail lors de l’exécution de la requête.

## <a name="prepare-files-for-querying"></a>Préparer les fichiers pour l’interrogation

Si possible, vous pouvez préparer les fichiers pour améliorer les performances :

- Convertir CSV en Parquet – Parquet est un format en colonnes. Dans la mesure où il est compressé, ses fichiers sont de plus petite taille que des fichiers CSV contenant les mêmes données. SQL à la demande a besoin de moins de temps et de demandes de stockage pour le lire.
- Si une requête cible un seul fichier volumineux, il est avantageux de fractionner celui-ci en fichiers plus petits.
- Essayez de conserver une taille de fichier CSV inférieure à 10 Go.
- Il est préférable d’avoir des fichiers de taille identique pour un chemin d’accès OPENROWSET unique ou un emplacement de table externe.
- Partitionnez vos données en stockant des partitions dans différents dossiers ou noms de fichiers. Consultez [Utiliser les fonctions filename et filepath pour cibler des partitions spécifiques](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Utiliser les fonctions filename et filepath pour cibler des partitions spécifiques

Les données sont souvent organisées en partitions. Vous pouvez donner pour instruction à SQL à la demande d’interroger des dossiers et fichiers particuliers. Cela permet de réduire le nombre de fichiers et la quantité de données que la requête doit lire et traiter. En prime, vous obtiendrez de meilleures performances.

Pour plus d’informations, consultez les fonctions [filename](develop-storage-files-overview.md#filename-function) et [filepath](develop-storage-files-overview.md#filepath-function), ainsi que les exemples montrant comment [interroger des fichiers spécifiques](query-specific-files.md).

Si vos données stockées ne sont pas partitionnées, envisagez de les partitionner afin de pouvoir utiliser ces fonctions pour optimiser les requêtes ciblant ces fichiers. Lorsque de l’[interrogation de tables Spark partitionnées](develop-storage-files-spark-tables.md) à partir de SQL à la demande, la requête cible automatiquement uniquement les fichiers nécessaires.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Utiliser CETAS pour améliorer les performances des requêtes et les jointures

[CETAS](develop-tables-cetas.md) est l’une des fonctionnalités les plus importantes disponibles dans SQL à la demande. CETAS est une opération parallèle qui crée des métadonnées de table externe et exporte le résultat de la requête SELECT vers un ensemble de fichiers dans votre compte de stockage.

Vous pouvez utiliser CETAS pour stocker dans un nouveau jeu de fichiers des parties de requêtes souvent utilisées, telles des tables de référence jointes. Ensuite, vous pouvez joindre une telle table externe au lieu de répéter des jointures communes dans plusieurs requêtes.

Comme CETAS génère des fichiers Parquet, les statistiques sont automatiquement créées lorsque la première requête cible cette table externe, ce qui améliore les performances.

## <a name="next-steps"></a>Étapes suivantes

Consultez notre article [Dépannage](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) concernant les problèmes courants et leurs solutions. Si vous utilisez un pool SQL plutôt que SQL à la demande, consultez l’article [Bonnes pratiques concernant les pools SQL](best-practices-sql-pool.md) pour obtenir des instructions spécifiques.
