---
title: Magasins de données pris en charge dans Azure Data Share
description: Découvrez les magasins de données qui sont pris en charge pour l’utilisation dans Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: def73d137f3cc2c79ae8417995ec6bdf6c519b7d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812621"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Magasins de données pris en charge dans Azure Data Share

Azure Data Share fournit un partage de données flexible et ouvert, incluant la possibilité de partager depuis et vers des magasins de données différents. Les fournisseurs de données peuvent partager des données à partir d’un type de magasin de données, tandis que leurs consommateurs de données peuvent choisir le magasin de données dans lequel recevoir les données. 

Dans cet article, vous allez découvrir l’ensemble complet des magasins de données Azure qui sont pris en charge dans Azure Data Share. Vous apprendrez également comment les fournisseurs de données et les consommateurs de données peuvent combiner différents magasins de données. 

## <a name="supported-data-stores"></a>Magasins de données pris en charge 

Le tableau suivant décrit les magasins de données pris en charge par Azure Data Share. 

| Banque de données | Partage basé sur des instantanés complets | Partage basé sur des instantanés incrémentiels | Partage en place 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Stockage Blob Azure |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (anciennement Azure SQL Data Warehouse) |✓ | | |
| Pool SQL dédié Azure Synapse Analytics (espace de travail) |✓ | | |
| Explorateur de données Azure | | |✓ |

## <a name="data-store-support-matrix"></a>Matrice de prise en charge des magasins de données

Azure Data Share permet aux consommateurs de données de choisir un magasin de données afin d’accepter des données. Par exemple, les données partagées à partir d’Azure SQL Database peuvent être reçues dans Azure Data Lake Store Gen2, Azure SQL Database ou Azure Synapse Analytics. Lorsque les clients configurent un partage de données de réception, ils peuvent choisir le format de réception de ces données. 

Le tableau suivant décrit les combinaisons et les options que les consommateurs de données peuvent choisir lorsqu’ils acceptent et configurent un partage de données. Pour plus d'informations, consultez [Configurer un mappage de jeu de données](how-to-configure-mapping.md).

| Banque de données | Stockage Blob | Data Lake Storage Gen 1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (anciennement SQL Data Warehouse) | Pool SQL dédié Synapse Analytics (espace de travail) | Explorateur de données
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Stockage Blob | ✓ || ✓ |||
| Data Lake Storage Gen 1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (anciennement SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Pool SQL dédié Synapse Analytics (espace de travail) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Explorateur de données ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Partager à partir d’un compte de stockage
Azure Data Share prend en charge le partage de fichiers, dossiers et de systèmes de fichiers à partir d’Azure Data Lake Storage Gen1 et Azure Data Lake Storage Gen2. Le service prend également en charge le partage de blobs, de dossiers et de conteneurs à partir de Stockage Blob Azure. Vous pouvez partager des objet blob de pages, d’ajouts et de blocs et ils sont reçus en tant qu’objets blob de blocs.

Lorsque les systèmes de fichiers, les conteneurs ou les dossiers sont partagés dans un partage basé sur un instantané, les consommateurs de données peuvent choisir d’effectuer une copie complète des données partagées. Ils peuvent également utiliser la fonctionnalité d’instantané incrémentiel pour copier uniquement les fichiers nouveaux ou mis à jour. 

Un instantané incrémentiel est basé sur l’heure de la dernière modification des fichiers. Les fichiers existants portant le même nom que les fichiers des données reçues sont remplacés dans un instantané. Les fichiers supprimés de la source ne sont pas supprimés de la cible. 

Pour plus d’informations, consultez [Partager et recevoir des données à partir de Stockage Blob Azure et d’Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Partager à partir d’une source SQL
Azure Data Share prend en charge le partage de tables et d’affichages à partir d’Azure SQL Database et d’Azure Synapse Analytics (anciennement Azure SQL Data Warehouse). Il prend en charge le partage de tables à partir d’un pool SQL dédié Azure Synapse Analytics (espace de travail). Le partage à partir d’un pool SQL serverless Azure Synapse Analytics (espace de travail) n’est pas pris en charge actuellement. 

Les consommateurs de données peuvent choisir d’accepter les données dans Azure Data Lake Storage Gen2 ou Stockage Blob Azure en tant que fichier CSV ou Parquet. Ils peuvent également accepter des données sous forme de tables dans Azure SQL Database et Azure Synapse Analytics.

Lorsque les consommateurs acceptent des données dans Azure Data Lake Storage Gen2 ou Stockage Blob Azure, des instantanés complets remplacent le contenu du fichier cible s’il existe déjà. Lorsque les données sont reçues dans une table, et que la table cible n’existe pas encore, Azure Data Share crée la table SQL en utilisant le schéma source. S’il existe déjà une table cible du même nom, celle-ci est remplacée par le dernier instantané complet. Les instantanés incrémentiels ne sont actuellement pas pris en charge.

Pour en savoir plus, consultez [Partager et recevoir des données d’Azure SQL Database et d’Azure Synapse Analytics](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Partager depuis Explorateur de données
Azure Data Share prend en charge la possibilité de partager des bases de données sur place à partir de clusters Azure Data Explorer. Un fournisseur de données peut partager au niveau de la base de données ou du cluster. 

Lorsque les données sont partagées au niveau de la base de données, les consommateurs de données ne peuvent accéder qu’aux bases de données partagées par le fournisseur de données. Lorsqu’un fournisseur partage des données au niveau du cluster, les consommateurs de données peuvent accéder à toutes les bases de données à partir du cluster du fournisseur, y compris toutes les futures bases de données créées par ce fournisseur.

Pour accéder aux bases de données partagées, les consommateurs de données doivent disposer de leur propre cluster Azure Data Explorer. Leur cluster doit figurer dans le même centre de données Azure que le cluster Azure Data Explorer du fournisseur de données. 

Lorsqu’une relation de partage est établie, Azure Data Share crée un lien symbolique entre le cluster du fournisseur et celui du consommateur. Les données ingérées dans le cluster source à l’aide du mode de traitement par lot s’affichent dans le cluster cible en quelques minutes.

Pour plus d’informations, consultez [Partager et recevoir des données à partir d’Azure Data Explorer](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).
