---
title: Le service Import/Export d’Azure SQL Database prend beaucoup de temps pour importer ou exporter une base de données | Microsoft Docs
description: Le service Import/Export d’Azure SQL Database prend beaucoup de temps pour importer ou exporter une base de données
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975368"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Le service Import/Export d’Azure SQL Database prend beaucoup de temps pour importer ou exporter une base de données

Si vous utilisez le service Import/Export d’Azure SQL Database, vous remarquerez peut-être que le processus prend parfois beaucoup de temps. Cet article fournit des informations supplémentaires sur les causes possibles de ces délais et présente d’autres méthodes que vous pouvez appliquer pour contourner ces problèmes.

## <a name="azure-sql-database-importexport-service"></a>Service Import/Export d’Azure SQL Database

Le service Import/Export d’Azure SQL Database est un service web basé sur REST qui s’exécute dans chaque centre de données Microsoft Azure. Il s’agit du service qui est appelé quand vous utilisez l’option [Importer la base de données](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) ou [Exporter](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) pour déplacer votre base de données SQL dans le portail Microsoft Azure. Le service fournit un service de mise en file d’attente des requêtes gratuit et un service de calcul gratuit pour effectuer des importations et des exportations d’une base de données Microsoft Azure SQL vers le stockage d’objets blob (Binary Large Object) Microsoft Azure.

Les opérations d’importation et d’exportation ne sont pas une sauvegarde de base de données physique traditionnelle, mais une sauvegarde logique de la base de données qui utilise un format BACPAC spécial. Ce format BACPAC logique vous permet d’éviter d’avoir à utiliser un format physique qui peut varier entre les versions de SQL Server et SQL Database. Ainsi, vous pouvez l’utiliser pour restaurer en toute sécurité la base de données vers une base de données SQL et vers une base de données SQL Server.

## <a name="what-causes-the-process-to-take-a-long-time"></a>Qu’est-ce qui rend le processus si long ?

Le service Import/Export d’Azure SQL Database fournit un nombre limité de machines virtuelles de calcul par région pour traiter les opérations d’importation et d’exportation. La machine virtuelle de calcul est hébergée par région afin d’éviter pour l’importation ou l’exportation tout délai et frais de bande passante entre les régions. Ainsi, si un trop grand nombre de requêtes sont effectuées en même temps dans la même région, des délais importants se produisent lors du traitement des opérations. Le temps nécessaire pour exécuter les requêtes peut varier de quelques secondes à plusieurs heures.

> [!NOTE]
> Si une requête n’est pas traitée dans un délai de quatre jours, le service l’annule automatiquement.

## <a name="recommended-solutions"></a>Solutions recommandées

Si vos exportations de base de données sont uniquement utilisées pour la récupération après une suppression accidentelle des données, toutes les éditions de base de données Azure SQL Server fournissent une fonctionnalité de restauration libre-service à partir des sauvegardes générées par le système. Toutefois, si vous avez besoin de ces exportations pour d’autres raisons et si vous avez besoin de performances d’importation ou d’exportation beaucoup plus rapides ou plus ou moins prévisibles, vous devez envisager les options suivantes :

* [Exporter vers un fichier BACPAC à l’aide de l’utilitaire SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Exporter vers un fichier BACPAC à l’aide de SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Exécutez l’importation ou l’exportation BACPAC directement dans votre code à l’aide de l’API Microsoft® SQL Server® DacFx (Data-Tier Application Framework). Pour toute information supplémentaire, consultez :
  * [Exporter une application de la couche Données](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Espace de noms Microsoft.SqlServer.Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Télécharger DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Considérations à prendre en compte lors de l’exportation ou importation d’une base de données Azure SQL

* Toutes les méthodes décrites dans cet article utilisent le quota DTU, ce qui entraîne une limitation par le service Azure SQLDB. Vous pouvez [afficher les statistiques DTU pour la base de données dans le portail Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Si la base de données atteint des limites de ressources, [mettez à niveau le niveau de service](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) pour ajouter d’autres ressources.
* Les applications clientes (telles que l’utilitaire sqlpackage ou votre application DAC personnalisée) doivent idéalement être exécutées à partir d’une machine virtuelle située dans la même région que votre base de données SQL, sinon vous risquez de rencontrer des problèmes de performances dus à la latence réseau.
* L’exportation de tables volumineuses sans index cluster peut être très lente, voire entraîner un échec. Cela est dû au fait que la table ne peut pas être fractionnée et exportée en parallèle, et qu’elle doit être exportée dans une transaction unique, ce qui entraîne une lenteur et des risques d’échec pendant l’exportation, en particulier pour les tables volumineuses. 


## <a name="related-documents"></a>Documents associés

[Considérations à prendre en compte lors de l’exportation d’une base de données Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
