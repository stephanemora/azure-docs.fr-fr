---
title: Répliquez des données dans Azure Database for MariaDB.
description: Cet article décrit la réplication des données entrantes pour Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 28c2c01e85120ec17e6f782fb0686a627d50d0d0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70136741"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Répliquer des données dans Azure Database for MariaDB

La réplication des données entrantes permet de synchroniser les données à partir d’un serveur MariaDB qui s’exécute en local, dans des machines virtuelles ou des services de base de données hébergés par d’autres fournisseurs cloud dans le service Azure Database for MariaDB. La réplication des données entrantes est basée sur la réplication selon la position du fichier journal binaire (binlog) native à MariaDB. Pour découvrir plus en détail la réplication binlog, consultez la [vue d’ensemble de la réplication binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Quand utiliser la réplication des données entrantes
Voici les principaux scénarios à prendre en compte avant d’utiliser la réplication des données entrantes :

- **Synchronisation de données hybride :** avec la réplication des données entrantes, vous pouvez maintenir la synchronisation des données entre vos serveurs locaux et Azure Database for MariaDB. Cette synchronisation est utile pour créer des applications hybrides. Cette méthode est intéressante si vous disposez d’un serveur de base de données local mais souhaitez déplacer les données vers une région proche des utilisateurs finaux.
- **Synchronisation de plusieurs clouds :** pour les solutions cloud complexes, utilisez la réplication des données entrantes pour synchroniser les données entre Azure Database for MariaDB et différents fournisseurs de cloud, notamment les machines virtuelles et les services de base de données hébergés dans ces clouds.

## <a name="limitations-and-considerations"></a>Limitations et considérations

### <a name="data-not-replicated"></a>Données non répliquées
La [*base de données système mysql*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) sur le serveur maître n’est pas répliquée. Les modifications apportées aux comptes et aux autorisations sur le serveur maître ne sont pas répliquées. Si vous créez un compte sur le serveur maître et que ce compte a besoin d’accéder au serveur réplica, créez manuellement le même compte sur le serveur réplica. Pour une présentation des tables figurant dans la base de données système, consultez la [documentation MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Configuration requise
- La version du serveur maître doit être au moins MariaDB version 10.2.
- Les versions du serveur maître et du serveur réplica doivent être identiques. Par exemple, les deux doivent être MariaDB version 10.2.
- Chaque table doit avoir une clé primaire.
- Le serveur maître doit utiliser le moteur InnoDB.
- L’utilisateur doit disposer des autorisations nécessaires pour configurer la journalisation binaire et créer de nouveaux utilisateurs sur le serveur maître.
- Si SSL est activé sur le serveur maître, vérifiez que le certificat d’autorité de certification SSL fourni pour le domaine a été inclus dans la procédure stockée `mariadb.az_replication_change_master`. Consultez les [exemples](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) suivants et le paramètre `master_ssl_ca`.
- Vérifiez que l’adresse IP du serveur maître a été ajoutée aux règles de pare-feu du serveur réplica Azure Database for MariaDB. Mettez à jour les règles de pare-feu à l’aide du [portail Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) ou d’[Azure CLI](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Vérifiez que la machine qui héberge le serveur maître autorise à la fois le trafic entrant et le trafic sortant sur le port 3306.
- Vérifiez que le serveur maître dispose d’une **adresse IP publique** ou que le DNS est public

### <a name="other"></a>Autres
- La réplication des données entrantes est prise en charge uniquement dans les niveaux tarifaires Usage général et Mémoire optimisée.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [configurer la réplication des données entrantes](howto-data-in-replication.md).
