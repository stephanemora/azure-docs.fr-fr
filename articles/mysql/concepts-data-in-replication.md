---
title: Répliquer des données Azure Database pour MySQL.
description: Cet article décrit la réplication des données entrantes pour Azure Database pour MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: 72f8211ecc0534b15402911de8fc0ec3d541a835
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294902"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Répliquer des données dans Azure Database pour MySQL

La réplication des données entrantes permet de synchroniser les données à partir d’un serveur MySQL qui s’exécute en local, dans des machines virtuelles ou des services de base de données hébergés par d’autres fournisseurs cloud dans le service Azure Database pour MySQL. La réplication des données est basée sur une réplication selon la position du fichier journal binaire (binlog) native à MySQL. Pour en savoir plus sur la réplication binlog, consultez la [vue d’ensemble de la réplication binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Quand utiliser la réplication des données entrantes
Voici les principaux scénarios à prendre en compte avant d’utiliser la réplication des données entrantes :

- **Synchronisation de données hybride :** avec la réplication des données entrantes, vous pouvez maintenir la synchronisation des données entre vos serveurs locaux et Azure Database pour MySQL. Cette synchronisation est utile pour créer des applications hybrides. Cette méthode est intéressante si vous disposez d’un serveur de base de données local mais souhaitez déplacer les données vers une région proche des utilisateurs finaux.
- **Synchronisation de plusieurs cloud :** pour les solutions de cloud complexes, utilisez la réplication des données entrantes pour synchroniser les données entre Azure Database pour MySQL et différents fournisseurs de cloud, y compris les machines virtuelles et les services de base de données hébergés dans ces clouds.

## <a name="limitations-and-considerations"></a>Limitations et considérations

### <a name="data-not-replicated"></a>Données non répliquées
La [*base de données système mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) sur le serveur principal n’est pas répliquée. Les modifications apportées aux comptes et aux autorisations sur le serveur principal ne sont pas répliquées. Si vous créez un compte sur le serveur principal et que ce compte a besoin d’accéder au serveur réplica, créez manuellement le même compte sur le serveur réplica. Pour une présentation des tables figurant dans la base de données système, consultez le [manuel MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Configuration requise
- La version du serveur principal doit être au moins MySQL version 5.6. 
- Les versions du serveur principal et du serveur réplica doivent être identiques. Par exemple, ce doit être MySQL version 5.6 ou MySQL version 5.7.
- Chaque table doit avoir une clé primaire.
- Le serveur principal doit utiliser le moteur MySQL InnoDB.
- L’utilisateur doit disposer des autorisations nécessaires pour configurer la journalisation binaire et créer de nouveaux utilisateurs sur le serveur principal.

### <a name="other"></a>Autres
- Les identificateurs de transaction globaux (GTID) ne sont pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [configurer la réplication des données entrantes](howto-data-in-replication.md)
