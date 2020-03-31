---
title: Réplication des données entrantes - Azure Database pour MySQL
description: Découvrez comment utiliser la réplication de données entrantes à synchroniser entre un serveur externe et le service Azure Database pour MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20be34191355e6ade40e0f3b218818bfa5345a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533230"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Répliquer des données dans Azure Database pour MySQL

La Réplication des données entrantes vous permet de synchroniser les données d'un serveur MySQL externe avec le service Azure Database pour MySQL. Le serveur externe peut être hébergé localement, dans des machines virtuelles, ou il peut s'agir d'un service de base de données hébergé par d'autres fournisseurs de services cloud. La réplication des données est basée sur une réplication selon la position du fichier journal binaire (binlog) native à MySQL. Pour en savoir plus sur la réplication binlog, consultez la [vue d’ensemble de la réplication binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Quand utiliser la réplication des données entrantes
Voici les principaux scénarios à prendre en compte avant d’utiliser la réplication des données entrantes :

- **Synchronisation de données hybride :** avec la réplication des données entrantes, vous pouvez maintenir la synchronisation des données entre vos serveurs locaux et Azure Database pour MySQL. Cette synchronisation est utile pour créer des applications hybrides. Cette méthode est intéressante si vous disposez d'un serveur de base de données local mais souhaitez déplacer les données vers une région proche des utilisateurs finaux.
- **Synchronisation de plusieurs cloud :** pour les solutions de cloud complexes, utilisez la réplication des données entrantes pour synchroniser les données entre Azure Database pour MySQL et différents fournisseurs de cloud, y compris les machines virtuelles et les services de base de données hébergés dans ces clouds.
 
Pour les scénarios de migration, utilisez [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS).

## <a name="limitations-and-considerations"></a>Limitations et considérations

### <a name="data-not-replicated"></a>Données non répliquées
La [*base de données système mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) située sur le serveur maître n'est pas répliquée. Les modifications apportées aux comptes et aux autorisations sur le serveur maître ne sont pas répliquées. Si vous créez un compte sur le serveur maître et que ce compte a besoin d'accéder au serveur réplica, créez manuellement le même compte sur le serveur réplica. Pour une présentation des tables figurant dans la base de données système, consultez le [manuel MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="requirements"></a>Spécifications
- La version du serveur maître doit être au moins MySQL version 5.6. 
- Les versions du serveur maître et du serveur réplica doivent être identiques. Par exemple, ce doit être MySQL version 5.6 ou MySQL version 5.7.
- Chaque table doit avoir une clé primaire.
- Le serveur maître doit utiliser le moteur MySQL InnoDB.
- L’utilisateur doit disposer des autorisations nécessaires pour configurer la journalisation binaire et créer de nouveaux utilisateurs sur le serveur maître.
- Si SSL est activé sur le serveur maître, vérifiez que le certificat d’autorité de certification SSL fourni pour le domaine a été inclus dans la procédure stockée `mysql.az_replication_change_master`. Consultez les [exemples](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) suivants et le paramètre `master_ssl_ca`.
- Vérifiez que l’adresse IP du serveur maître a été ajoutée aux règles de pare-feu du serveur réplica Azure Database pour MySQL. Mettez à jour les règles de pare-feu à l’aide du [portail Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) ou d’[Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Vérifiez que la machine qui héberge le serveur maître autorise à la fois le trafic entrant et le trafic sortant sur le port 3306.
- Vérifiez que le serveur maître a une **adresse IP publique** et que le système DNS est accessible publiquement ou a un nom de domaine complet (FQDN).

### <a name="other"></a>Autres
- La réplication des données entrantes est prise en charge uniquement dans les niveaux tarifaires Usage général et Mémoire optimisée.
- Les identificateurs de transaction globaux (GTID) ne sont pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [configurer la réplication des données entrantes](howto-data-in-replication.md)
- En savoir plus sur la [réplication dans Azure avec des réplicas en lecture](concepts-read-replicas.md)
- Découvrez comment [migrer des données avec un temps d’arrêt minimal à l’aide de DMS](howto-migrate-online.md).