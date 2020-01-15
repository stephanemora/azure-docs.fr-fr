---
title: Magasins de données pris en charge dans Azure Data Share
description: Découvrez les magasin de données qui sont pris en charge pour l’utilisation d’Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 56103ed89d2e7813fd60bc50ecca7271f5421a4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438688"
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
| Explorateur de données Azure | |[Préversion limitée](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>Matrice de prise en charge des magasins de données

Azure Data Share offre une flexibilité aux consommateurs de données lorsqu’ils décident du magasin de données devant recevoir les données. Par exemple, les données partagées à partir d’Azure SQL Database peuvent être reçues dans Azure Data Lake Store Gen 2, Azure SQL Database ou Azure Synapse Analytics. Les clients peuvent choisir le format dans lequel recevoir les données lors de la configuration d’un partage de données reçues. 

Le tableau ci-dessous détaille les différentes combinaisons et choix à la disposition des consommateurs de données, lors de l’acceptation et de la configuration de leur partage de données. Pour plus d’informations sur la configuration des mappages de jeu de données, consultez le [Guide pratique pour configurer les mappages de jeu de données](how-to-configure-mapping.md).

|  | Stockage Blob Azure | Azure SQL Data Lake Gen1 | Azure SQL Data Lake Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Stockage Blob Azure |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Azure SQL Database |✓ | |✓|✓|✓|
| Azure Synapse Analytics |✓ | |✓|✓|✓|

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).
