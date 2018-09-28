---
title: Répliquez des données dans Azure Database for MariaDB.
description: Cet article décrit la réplication des données entrantes pour Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a6bb6ae45c4240f21b1e0baf4956b053d4b98720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948177"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Répliquer des données dans Azure Database for MariaDB

La réplication des données entrantes permet de synchroniser les données à partir d’un serveur MariaDB qui s’exécute en local, dans des machines virtuelles ou des services de base de données hébergés par d’autres fournisseurs cloud dans le service Azure Database for MariaDB. La réplication des données entrantes est basée sur la réplication selon la position du fichier journal binaire (binlog) native à MariaDB. Pour découvrir plus en détail la réplication binlog, consultez la [vue d’ensemble de la réplication binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Quand utiliser la réplication des données entrantes
Voici les principaux scénarios à prendre en compte avant d’utiliser la réplication des données entrantes :

- **Synchronisation de données hybride :** avec la réplication des données entrantes, vous pouvez maintenir des données synchronisées entre vos serveurs locaux et Azure Database for MariaDB. Cette synchronisation est utile pour créer des applications hybrides. Cette méthode est intéressante si vous disposez d’un serveur de base de données local mais souhaitez déplacer les données vers une région proche des utilisateurs finaux.
- **Synchronisation de plusieurs cloud :** pour les solutions cloud complexes, utilisez la réplication des données entrantes pour synchroniser les données entre Azure Database for MariaDB et différents fournisseurs de cloud, notamment les machines virtuelles et les services de base de données hébergés dans ces clouds.

## <a name="limitations-and-considerations"></a>Limitations et considérations

### <a name="data-not-replicated"></a>Données non répliquées
La [*base de données système mysql*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) sur le serveur maître n’est pas répliquée. Les modifications apportées aux comptes et aux autorisations sur le serveur maître ne sont pas répliquées. Si vous créez un compte sur le serveur maître et que ce compte a besoin d’accéder au serveur réplica, créez manuellement le même compte sur le serveur réplica. Pour une présentation des tables figurant dans la base de données système, consultez la [documentation MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Configuration requise
- La version du serveur maître doit être au moins MariaDB version 10.2.
- Les versions du serveur maître et du serveur réplica doivent être identiques. Par exemple, les deux doivent être MariaDB version 10.2.
- Chaque table doit avoir une clé primaire.
- Le serveur maître doit utiliser le moteur InnoDB.
- L’utilisateur doit disposer des autorisations nécessaires pour configurer la journalisation binaire et créer de nouveaux utilisateurs sur le serveur maître.

### <a name="other"></a>Autres
- La réplication des données entrantes est prise en charge uniquement dans les niveaux tarifaires Usage général et Mémoire optimisée.
- Les identificateurs de transaction globaux (GTID) ne sont pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [configurer la réplication des données entrantes](howto-data-in-replication.md).
