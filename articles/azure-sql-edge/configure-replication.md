---
title: Configurer la réplication sur Azure SQL Edge (préversion)
description: En savoir plus sur la configuration de la réplication sur Azure SQL Edge (préversion)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6249d72ba43bf59a2862595f40adf2d1ac5a6346
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235165"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Configurer la réplication sur Azure SQL Edge (préversion) 

L'instance d'Azure SQL Edge peut être configurée en tant que titulaire d'un abonnement par émission de données pour une réplication transactionnelle unidirectionnelle ou une réplication de capture instantanée. L'instance d'Azure SQL Edge ne peut pas agir en tant que serveur de publication ou serveur de distribution pour une configuration de réplication transactionnelle. La réplication de fusion, la réplication P2P et la publication Oracle ne sont pas prises en charge par Azure SQL Edge.

## <a name="supported-configurations"></a>**Configurations prises en charge** :
  
- L'instance d'Azure SQL Edge doit être titulaire d'un abonnement par émission de données pour un serveur de publication.
- Le serveur de publication et le serveur de distribution peuvent être :
   - une instance de SQL Server exécutée localement ou une instance de SQL Server exécutée sur une machine virtuelle Azure. Pour plus d’informations, consultez [Présentation de SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). Les instances de SQL Server doivent utiliser une version supérieure à SQL Server 2016.
   - Une instance d’Azure SQL Managed Instance. Managed Instance peut héberger des bases de données de serveur de publication, de serveur de distribution et d’abonné. Pour plus d’informations, consultez [Réplication avec SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- La base de données de distribution et les agents de réplication ne peuvent pas être placés sur une instance d'Azure SQL Edge.  

> [!NOTE]
> Si vous tentez de configurer la réplication avec une version non prise en charge, les erreurs suivantes peuvent se produire : MSSQL_REPL20084 (Le processus n’a pas pu se connecter à l’abonné) et MSSQL_REPL40532 (Impossible d’ouvrir le serveur \<name> demandé par la connexion. La connexion a échoué).  

Pour bénéficier de toutes les fonctionnalités d'Azure SQL Edge, vous devez utiliser les dernières versions de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) et de [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

## <a name="remarks"></a>Notes

- La réplication peut être configurée à l'aide de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou en exécutant des instructions Transact-SQL sur le serveur de publication à l'aide de SQL Server Management Studio ou d'[Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio).
- La réplication ne peut utiliser que des connexions d'authentification SQL Server pour se connecter à une instance d'Azure SQL Edge.
- Les tables répliquées doivent avoir une clé primaire.
- Une même publication sur SQL Server peut à la fois prendre en charge les abonnés d'Azure SQL Edge et de SQL Server (localement et sur une machine virtuelle Azure).  
- La gestion, la supervision et le dépannage de la réplication doivent être effectués à partir de l’instance de SQL Server.  
- Seuls les abonnements par émission de données à Azure SQL Edge sont pris en charge.  
- Seul `@subscriber_type = 0` est pris en charge dans **sp_addsubscription** pour Azure SQL Edge.  
- Azure SQL Edge ne prend pas en charge les réplications bidirectionnelles, immédiates, actualisables et de pair à pair.
- Azure SQL Edge ne prend en charge qu’une partie des fonctionnalités disponibles dans SQL Server ou SQL Managed Instance, car une telle tentative de réplication d’une base de données (ou d’objets de la base de données) contenant une ou plusieurs fonctionnalités non prises en charge échoue. Par exemple, la tentative de réplication d'une base de données qui contient des objets comportant des types de données spatiales entraînera une erreur. Pour plus d'informations sur les fonctionnalités prises en charge par Azure SQL Edge, consultez [Fonctionnalités prises en charge par Azure SQL Edge](features.md).

## <a name="scenarios"></a>Scénarios  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Initialisation des données de référence sur une instance d'Edge

Souvent, la réplication se révèle utile lorsque l'instance d'Edge doit être initialisée avec des données de référence qui changent au fil du temps. Par exemple, la mise à jour de modèles ML sur l’instance Edge après leur entraînement sur une instance de SQL Server.

1. Créez une publication de réplication transactionnelle sur une base de données SQL Server.  
2. Sur l’instance de SQL Server, utilisez l’**Assistant Nouvel abonnement** ou des instructions Transact-SQL pour créer une émission de données sur l’abonnement à Azure SQL Edge.  
3. La base de données répliquée sur Azure SQL Edge peut être initialisée à l'aide d'une capture instantanée générée par l'agent d'instantané et distribuée par l'agent de distribution, ou à l'aide d'une sauvegarde de la base de données à partir du serveur de publication. Là encore, si la sauvegarde de la base de données contient des objets/fonctionnalités qui ne sont pas pris en charge par Azure SQL Edge, l'opération de restauration échouera.

## <a name="limitations"></a>Limites

Les options suivantes ne sont pas prises en charge par les abonnements Azure SQL Edge :

- Copier les associations de groupes de fichiers  
- Copier les schémas de partition de table  
- Copier les schémas de partition d’index  
- Copier les statistiques définies par l’utilisateur  
- Copier les liaisons par défaut  
- Copier les liaisons de règle  
- Copier les index de recherche en texte intégral  
- Copier le schéma XSD XML  
- Copier les index XML  
- Copier les autorisations  
- Copier les index spatiaux  
- Copier les index filtrés  
- Copier l’attribut de compression de données  
- Copier l’attribut de colonne éparse  
- Copiez les types suivants : filestream, hierarchyid ou données spatiales.
- Convertir hierarchyId en types de données MAX  
- Convertir le type spatial en types de données MAX  
- Copier les propriétés étendues  
- Copier les autorisations  

## <a name="examples"></a>Exemples

Créez une publication et un abonnement par émission de données. Pour plus d'informations, consultez les pages suivantes :
  
- [Créer une publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Créez un abonnement par émission de données](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) en utilisant le nom/l'adresse IP du serveur Azure SQL Edge comme abonné (par exemple **myEdgeinstance, 1433**) et un nom de base de données de l'instance d'Azure SQL Edge comme base de données de destination (par exemple, **AdventureWorks**).  

## <a name="see-also"></a>Voir aussi  

- [Créer une publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Créer un abonnement par émission de données](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types de réplication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Surveillance (réplication)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initialiser un abonnement](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


