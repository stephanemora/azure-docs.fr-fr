---
title: Résoudre une corruption de base de données – Base de données Azure pour MySQL
description: En savoir plus sur la résolution des problèmes de corruption de base de données pour Azure Database pour MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930146"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>Résoudre des problèmes de corruption de base de données sur Azure pour MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Une corruption de base de données peut entraîner un temps d’arrêt de votre application et il est essentiel de résoudre le problème à temps pour éviter toute perte de données. En cas d’endommagement de la base de données, les journaux d’activité du serveur consignent l’erreur **InnoDB : corruption de page de base de données sur disque ou échec**.

Ce guide explique corriger une base de données ou une table endommagées. Azure Database pour MySQL utilise le moteur InnoDB et effectue des opérations de vérification et de réparation automatisées. InnoDB recherche les pages endommagées en effectuant des sommes de contrôle sur chaque page lue. S’il trouve une incohérence de somme de contrôle, il arrête automatiquement le serveur MySQL.

Essayez l’une des options ci-dessous pour réduire rapidement les problèmes d’endommagement de votre base de données.

## <a name="restart-your-mysql-server"></a>Redémarrer votre serveur MySQL

En général, vous remarquez qu’une base de données ou une table est endommagée lorsque votre application accède à celles-ci. InnoDB intègre un mécanisme de récupération après incident capable de résoudre la plupart des problèmes lors d’un redémarrage du serveur. Par conséquent, le redémarrage du serveur devrait aider celui-ci à récupérer d’un incident à l’origine de l’état incorrect de la base de données.

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>Résoudre une altération des données avec une méthode de vidage et restauration

Il est recommandé de résoudre le problème d’endommagement avec une **méthode de vidage et restauration**. Cela implique d’obtenir l’accès à la table endommagée, de se servir de l’utilitaire **mysqldump** pour créer une sauvegarde logique de la table qui conserve la structure et les données de la table, puis de recharger la table dans la base de données.

### <a name="backup-your-database-or-tables"></a>Sauvegarder vos bases de données ou tables

> [!Important]
> - Assurez-vous d’avoir configuré une règle de pare-feu pour accéder au serveur à partir de votre ordinateur client. Voyez comment configurer une [règle de pare-feu sur un serveur unique](howto-manage-firewall-using-portal.md) et une [règle de pare-feu sur un serveur flexible](flexible-server/how-to-connect-tls-ssl.md).
> - Utiliser l’option SSL ```--ssl-cert``` pour l’utilitaire **mysqldump** si SSL est activé

Créer un fichier de sauvegarde à partir de la ligne de commande à l’aide de l’utilitaire mysqldump en utilisant cette commande

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Voici les paramètres à fournir :
- [ssl-cert=/path/to/pem] Téléchargez le certificat SSL sur votre ordinateur client et définissez le chemin d’accès dans la commande. N’UTILISEZ PAS cette méthode si le protocole SSL est désactivé.
- [host] est votre serveur Azure Database pour MySQL
- [uname] est le nom d’utilisateur de votre administrateur du serveur
- [pass] est le mot de passe de votre utilisateur administrateur
- [dbname] est le nom de votre base de données
- [backupfile.sql] est le nom du fichier de sauvegarde de votre base de données

> [!Important]
> - Pour un serveur unique, utilisez le format ```admin-user@servername``` pour remplacer ```myserveradmin``` dans les commandes ci-dessous.
> - Pour un serveur flexible, utilisez le format ```admin-user``` pour remplacer ```myserveradmin``` dans les commandes ci-dessous.

Si une table spécifique est endommagée, sélectionnez des tables spécifiques dans votre base de données à sauvegarder à l’aide de cet exemple
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Pour sauvegarder une ou plusieurs bases de données, utilisez le commutateur --database et répertoriez les noms de base de données séparés par des espaces.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>Restaurer votre base ou vos tables

Les étapes suivantes montrent comment restaurer votre base de données ou vos tables. Une fois le fichier de sauvegarde créé, vous pouvez restaurer la table ou les bases de données à l’aide de l’utilitaire ***mysql**. Exécutez la commande comme indiqué ci-dessous :

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Voici un exemple de restauration de ```testdb``` à partir d’un fichier de sauvegarde créé avec l’utilitaire **mysqldump**. 

> [!Important]
> - Pour un serveur unique, utilisez le format ```admin-user@servername``` pour remplacer ```myserveradmin``` dans la commande ci-dessous.
> - Pour un serveur flexible, utilisez le format ```admin-user``` pour remplacer ```myserveradmin``` dans la commande ci-dessous. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Étapes suivantes
Si les étapes ci-dessus ne permettent pas de résoudre le problème, vous pouvez toujours restaurer le serveur entier.
- [Restaurer un serveur unique Azure Database pour MySQL](howto-restore-server-portal.md)
- [Restaurer un serveur flexible Azure Database pour MySQL](flexible-server/how-to-restore-server-portal.md)



