---
title: Magasins de données pris en charge dans Azure Data Share
description: Découvrez les magasin de données qui sont pris en charge pour l’utilisation d’Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 5d4b1282b0a08657aea6f8a13aae7ed1fe49079b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964207"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Magasins de données pris en charge dans Azure Data Share

Azure Data Share fournit un partage de données flexible et ouvert, incluant la possibilité de partager depuis et vers des magasins de données différents. Les fournisseurs de données peuvent partager des données à partir d’un type de magasin de données, tandis que leurs consommateurs de données peuvent choisir le magasin de données dans lequel recevoir les données. 

Dans cet article, vous allez découvrir l’ensemble complet des magasins de données Azure qui sont pris en charge dans Azure Data Share. Vous pouvez également trouver des informations sur les combinaisons de magasins de données, qui sont exploitables par les fournisseurs de données et les consommateurs de données. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Quels magasins de données sont pris en charge dans Azure Data Share ? 

Le tableau ci-dessous détaille les sources de données prises en charge pour Azure Data Share. 

| Banque de données | Partage basé sur une capture instantanée | Partage en place 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Stockage Blob Azure |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |Version préliminaire publique | |
| Azure Synapse Analytics (anciennement Azure SQL DW) |Version préliminaire publique | |
| Explorateur de données Azure | |Version préliminaire publique |

## <a name="data-store-support-matrix"></a>Matrice de prise en charge des magasins de données

Azure Data Share offre une flexibilité aux consommateurs de données lorsqu’ils décident du magasin de données devant recevoir les données. Par exemple, les données partagées à partir d’Azure SQL Database peuvent être reçues dans Azure Data Lake Store Gen 2, Azure SQL Database ou Azure Synapse Analytics. Les clients peuvent choisir le format dans lequel recevoir les données lors de la configuration d’un partage de données reçues. 

Le tableau ci-dessous détaille les différentes combinaisons et choix à la disposition des consommateurs de données, lors de l’acceptation et de la configuration de leur partage de données. Pour plus d’informations sur la configuration des mappages de jeu de données, consultez le [Guide pratique pour configurer les mappages de jeu de données](how-to-configure-mapping.md).

|  | Stockage Blob Azure | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Stockage Blob Azure | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Azure SQL Database | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (anciennement Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Partager à partir d’un compte de stockage
Azure Data Share prend en charge le partage de fichiers, dossiers et de systèmes de fichiers à partir d’Azure Data Lake Gen1 et Azure Data Lake Gen2. Le service prend également en charge le partage de blobs, de dossiers et de conteneurs à partir de Stockage Blob Azure. Lorsque les dossiers sont partagés dans un partage basé sur une capture instantanée, le consommateur de données peut choisir d’effectuer une copie complète des données de partage ou de tirer parti de la fonctionnalité de capture instantanée incrémentielle pour copier uniquement les fichiers nouveaux ou mis à jour. Les fichiers existants portant le même nom seront remplacés.

## <a name="share-from-a-sql-based-source"></a>Partager à partir d’une source SQL
Azure Data Share prend en charge le partage de tables ou d’affichages à partir d’Azure SQL Database et d’Azure Synapse Analytics (anciennement Azure SQL DW). Le consommateur de données peut choisir d’accepter les données dans Azure Data Lake Storage Gen2 ou Stockage Blob Azure en tant que fichier CSV ou Parquet. La capture instantanée complète remplace le contenu du fichier cible. Le consommateur de données peut également accepter les données dans une table SQL. Si la table SQL cible n’est pas disponible côté consommateur de données, Azure Data Share crée la table SQL avec le schéma source. La capture instantanée complète ajoute le contenu de la table source à la table SQL cible. La capture instantanée incrémentielle n’est pas prise en charge actuellement.

## <a name="share-from-azure-data-explorer"></a>Partager depuis Azure Data Explorer
Azure Data Share prend en charge la possibilité de partager des bases de données sur place à partir de clusters Azure Data Explorer. Le fournisseur de données peut partager au niveau de la base de données ou du cluster. Lorsqu’il est partagé au niveau de la base de données, le consommateur de données est uniquement en mesure d’accéder aux bases de données spécifiques partagées par le fournisseur de données. En cas de partage au niveau du cluster, le consommateur de données peut accéder à toutes les bases de données à partir du cluster du fournisseur, y compris toutes les futures bases de données créées par le fournisseur de données.

Pour accéder aux bases de données partagées, le consommateur de données doit disposer de son propre cluster Azure Data Explorer. Le cluster Azure Data Explorer du consommateur de données doit se trouver dans le même centre de données Azure que le cluster Azure Data Explorer du fournisseur de données. Lorsque la relation de partage est établie, Azure Data Share crée un lien symbolique entre les clusters Azure Data Explorer du fournisseur et ceux du consommateur.

Azure Data Explorer prend en charge deux modes d’ingestion de données : en lot et en continu. Les données reçues en lot dans la base de données partagée s’affichent entre quelques secondes et quelques minutes du côté du consommateur de données. Les données reçues en continu peuvent prendre jusqu’à 24 heures pour apparaître du côté du consommateur de données. 

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).
