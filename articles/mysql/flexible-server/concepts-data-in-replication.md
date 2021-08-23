---
title: Réplication des données entrantes - Azure Database pour MySQL Flexible
description: Découvrez l’utilisation de la réplication des données entrantes pour effectuer une synchronisation entre un serveur externe et le service Azure Database pour MySQL Flexible.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 5cc19531f076d3b630faced7fef4ea5cb05be9f1
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751376"
---
# <a name="replicate-data-into-azure-database-for-mysql-flexible--server-preview"></a>Répliquer des données dans Azure Database pour MySQL serveur flexible (préversion)

La réplication des données entrantes vous permet de synchroniser les données d’un serveur MySQL externe avec le service Azure Database pour MySQL Flexible. Le serveur externe peut être hébergé localement, dans des machines virtuelles, Azure Database pour MySQL Single Server, ou il peut s'agir d'un service de base de données hébergé par d'autres fournisseurs de services cloud. La réplication des données est basée sur la position du fichier journal binaire (binlog). Pour en savoir plus sur la réplication binlog, consultez la [vue d’ensemble de la réplication binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

> [!Note]
> La réplication basée sur GTID n’est actuellement pas prise en charge pour les serveurs flexibles Azure Database pour MySQL. 

## <a name="when-to-use-data-in-replication"></a>Quand utiliser la réplication des données entrantes

Voici les principaux scénarios à prendre en compte concernant l’utilisation de la réplication des données entrantes :

- **Synchronisation de données hybride :** avec la réplication des données entrantes, vous pouvez maintenir la synchronisation des données entre vos serveurs locaux et un serveur flexible Azure Database pour MySQL. Cette synchronisation est utile pour créer des applications hybrides. Cette méthode est intéressante si vous disposez d'un serveur de base de données local mais souhaitez déplacer les données vers une région proche des utilisateurs finaux.
- **Synchronisation de plusieurs cloud :** pour les solutions de cloud complexes, utilisez la réplication des données entrantes pour synchroniser les données entre un serveur flexible Azure Database pour MySQL et différents fournisseurs de cloud, y compris les machines virtuelles et les services de base de données hébergés dans ces clouds.
- **Migration :** les clients peuvent effectuer une migration minimale à l’aide d’outils open source, tels que [MyDumper/MyLoader](https://centminmod.com/mydumper.html), avec la réplication des données. Un basculement sélectif de la charge de production de la base de données source vers la base de données de destination est possible avec la réplication des données. 

Pour les scénarios de migration, utilisez [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS).

## <a name="limitations-and-considerations"></a>Limitations et considérations

### <a name="data-not-replicated"></a>Données non répliquées

La [*base de données système mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) sur le serveur source n’est pas répliquée. De plus, les changements apportés aux comptes et aux autorisations sur le serveur source ne le sont pas non plus. Si vous créez un compte sur le serveur source et que ce compte a besoin d’accéder au serveur réplica, créez manuellement le même compte sur le serveur réplica. Pour une présentation des tables figurant dans la base de données système, consultez le [manuel MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Filtrage

Pour ignorer la réplication des tables à partir de votre serveur source (hébergé localement, dans des machines virtuelles ou un service de base de données hébergé par d’autres fournisseurs cloud), le paramètre `replicate_wild_ignore_table` est pris en charge. Si vous le souhaitez, mettez à jour ce paramètre sur le serveur de réplication hébergé dans Azure à l’aide du [portail Azure](how-to-configure-server-parameters-portal.md) ou [d’Azure CLI](how-to-configure-server-parameters-cli.md).

Pour en savoir plus sur ce paramètre, consultez la [documentation MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table).

### <a name="requirements"></a>Spécifications

- La version du serveur source doit être au moins MySQL version 5.7.
- Nous vous recommandons d’utiliser la même version de serveur source et de réplica. Par exemple, les deux doivent être MySQL version 5.7 ou MySQL version 8.0.
- Nous vous recommandons de disposer d’une clé primaire dans chaque table. Si nous disposons d’une table sans clé primaire, la réplication peut-être lenteur.
- Le serveur source doit utiliser le moteur MySQL InnoDB.
- L’utilisateur doit disposer des autorisations nécessaires pour configurer la journalisation binaire et créer de nouveaux utilisateurs sur le serveur source.
- Si SSL est activé sur le serveur source, vérifiez que le certificat d’autorité de certification SSL fourni pour le domaine a été inclus dans la procédure stockée `mysql.az_replication_change_master`. Consultez les [exemples](./how-to-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) suivants et le paramètre `master_ssl_ca`.
- Vérifiez que la machine qui héberge le serveur source autorise à la fois le trafic entrant et le trafic sortant sur le port 3306.
- Vérifiez que le serveur source dispose d’une **adresse IP publique**, que DNS est accessible publiquement ou que le serveur source comporte un nom de domaine complet (FQDN).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [configurer la réplication des données entrantes](how-to-data-in-replication.md)
- En savoir plus sur la [réplication dans Azure avec des réplicas en lecture](concepts-read-replicas.md)
