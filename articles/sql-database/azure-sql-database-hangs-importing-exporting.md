---
title: Le service Import/Export prend beaucoup de temps
description: Le service Import/Export d’Azure SQL Database prend beaucoup de temps pour importer ou exporter une base de données
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: e1638c9779ca50507a1ce30dd3bbc9c18248964a
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807066"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Le service Import/Export d’Azure SQL Database prend beaucoup de temps pour importer ou exporter une base de données

Parfois, lorsque vous utilisez le service Import/Export d’Azure SQL Database, il arrive que le processus dure plus longtemps que prévu. Cet article décrit les causes probables d’un tel ralentissement et présente des méthodes permettant de contourner ce problème.

## <a name="azure-sql-database-importexport-service"></a>Service Import/Export d’Azure SQL Database

Le service Import/Export d’Azure SQL Database est un service web basé sur REST qui s’exécute dans chaque centre de données Azure. Ce service est appelé lorsque vous utilisez l’option [Importer la base de données](sql-database-import.md#using-azure-portal) ou [Exporter](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) pour déplacer votre base de données SQL dans le portail Microsoft Azure. Grâce à lui, vous pouvez placer des requêtes en file d’attente ou effectuer des calculs. Cela permet d’effectuer des importations et des exportations entre Azure SQL Database et Azure Blob Storage.

Les opérations d’importation et d’exportation ne sont pas une sauvegarde de base de données physique traditionnelle, mais une sauvegarde logique de la base de données qui utilise un format BACPAC spécial. Ce format BACPAC logique vous permet d’éviter d’avoir à utiliser un format physique qui peut varier entre les versions de Microsoft SQL Server et Azure SQL Database. Ainsi, vous pouvez l’utiliser pour restaurer en toute sécurité la base de données vers une base de données SQL Server et vers une base de données SQL.

## <a name="what-causes-delays-in-the-process"></a>Quelles peuvent être les causes d’un ralentissement du processus ?

Le service Import/Export d’Azure SQL Database fournit un nombre limité de machines virtuelles de calcul par région pour traiter les opérations d’importation et d’exportation. Les machines virtuelles de calcul sont hébergées par région afin d’éviter pour l’importation ou l’exportation tout délai et frais de bande passante entre les régions. Ainsi, si un trop grand nombre de requêtes sont effectuées simultanément dans la même région, le traitement des opérations risque d’être ralenti de façon notable. Le temps nécessaire pour exécuter les requêtes peut varier de quelques secondes à plusieurs heures.

> [!NOTE]
> Si une requête n’est pas traitée dans un délai de quatre jours, le service l’annule automatiquement.

## <a name="recommended-solutions"></a>Solutions recommandées

Si vos exportations de base de données sont uniquement utilisées pour la récupération après une suppression accidentelle des données, toutes les éditions d’Azure SQL Database fournissent une fonctionnalité de restauration libre-service à partir des sauvegardes générées par le système. Toutefois, si vous avez besoin de ces exportations pour d’autres raisons et si vous avez besoin de performances d’importation/exportation beaucoup plus rapides ou plus ou moins prévisibles, nous vous recommandons les options suivantes :

* [Procédez à une exportation vers un fichier BACPAC à l’aide de l’utilitaire SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Procédez à une exportation vers un fichier BACPAC à l’aide de SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Exécutez l’importation ou l’exportation BACPAC directement dans votre code à l’aide de l’API Microsoft SQL Server Data-Tier Application Framework (DacFx). Si vous souhaitez en savoir plus, veuillez consulter :
  * [Exporter une application de la couche Données](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Espace de noms Microsoft.SqlServer.Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Télécharger DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Éléments à prendre en compte lorsque vous exportez ou importez une base de données SQL Azure

* Toutes les méthodes décrites dans cet article utilisent le quota « Unité de transaction de base de données » (ou DTU, pour « Database Transaction Unit »), ce qui entraîne une limitation par le service Azure SQL Database. Vous pouvez [afficher les statistiques DTU pour la base de données dans le portail Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Si la base de données atteint ses limites de ressources, [mettez à niveau le niveau de service](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) pour ajouter d’autres ressources.
* Dans l’idéal, vous devez exécuter des applications clientes (telles que l’utilitaire SqlPackage ou votre application DAC personnalisée) à partir d’une machine virtuelle dans la même région que votre base de données SQL. Dans le cas contraire, vous risquez de rencontrer des problèmes de performances liés à la latence du réseau.
* L’exportation de tables volumineuses sans index cluster peut être très lente, voire entraîner un échec. Ce comportement se produit parce que la table ne peut pas être fractionnée et exportée en parallèle. Au lieu de cela, elle doit être exportée dans une transaction unique, ce qui entraîne un ralentissement des performances et une défaillance potentielle pendant l’exportation, en particulier pour les tables volumineuses.


## <a name="related-documents"></a>Documents associés

[Considérations à prendre en compte lors de l’exportation d’une base de données Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
