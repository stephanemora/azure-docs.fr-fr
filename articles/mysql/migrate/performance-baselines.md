---
title: Guide de migration de MySQL local vers Azure Database pour MySQL - Base de référence des performances
description: La compréhension de la charge de travail MySQL existante est un des meilleurs investissements à faire pour garantir la réussite d’une migration.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: d04a02eb23bb386a6a751d5fab4a3b33c9673ac0
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970991"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-performance-baselines"></a>Guide de migration de MySQL local vers Azure Database pour MySQL - Base de référence des performances

La compréhension de la charge de travail MySQL existante est un des meilleurs investissements à faire pour garantir la réussite d’une migration. L’excellence des performances du système dépend de la pertinence du matériel et de la qualité de la conception d’applications. Des éléments tels que le processeur, la mémoire, le disque et la mise en réseau doivent être dimensionnés et configurés de manière appropriée pour la charge prévue. Le matériel et la configuration font partie de l’équation des performances du système. Le développeur doit comprendre la charge des requêtes de base de données et les requêtes les plus coûteuses à exécuter. Le fait de se concentrer sur les requêtes les plus coûteuses peut faire une différence significative dans les métriques de performances globales.

La création de bases de référence des performances des requêtes est essentielle pour un projet de migration. Les bases de référence des performances peuvent être utilisées pour vérifier la configuration de la zone d’atterrissage Azure pour les charges de travail de données migrées. La plupart des systèmes fonctionnent en continu et ont différentes périodes de forte charge. Il est important de mesurer les charges de travail des pics pour la base de référence. Les mesures doivent être capturées plusieurs fois. Plus loin dans ce document, nous explorons les paramètres du serveur source et dans quelle mesure ils sont essentiels pour la base de référence des performances globales. Les paramètres du serveur ne doivent pas être négligés lors d’un projet de migration.

### <a name="tools"></a>Outils

Vous trouverez ci-dessous les outils utilisés pour collecter les métriques du serveur et les informations sur les charges de travail de base de données. Utilisez les métriques capturées pour déterminer le niveau approprié d’Azure Database pour MySQL et les options de mise à l’échelle associées.

  - [MySQL Enterprise Monitor :](https://www.mysql.com/products/enterprise/monitor.html) Cet outil payant de l’édition Enterprise peut fournir une liste triée des requêtes les plus coûteuses, des métriques du serveur, des E/S des fichiers et des informations sur la topologie.

  - [Percona Monitoring and Management (PMM)](https://www.percona.com/software/database-tools/percona-monitoring-and-management) : Une solution pointue de supervision de base de données open source. Il permet de réduire la complexité, d’optimiser les performances et d’améliorer la sécurité des environnements de bases de données critiques, quel que soit l’emplacement où ils sont déployés.

### <a name="server-parameters"></a>Paramètres de serveur

Les configurations par défaut du serveur MySQL peuvent ne pas convenir exactement à une charge de travail. MySQL contient une multitude de paramètres de serveur mais dans la plupart des cas, l’équipe de migration doit se concentrer sur seulement quelques-uns d’entre eux. Les paramètres suivants doivent être évalués dans les environnements **source** et **cible**. Des configurations incorrectes peuvent affecter la rapidité de la migration. Nous réexaminerons ces paramètres lors de l’exécution des étapes de migration.

  - **innodb\_buffer\_pool\_size** : une valeur élevée garantit que les ressources en mémoire sont utilisées en premier, avant d’utiliser des E/S disque. Les valeurs standard vont de 80 à 90 % de la mémoire disponible. Par exemple, un système avec 8 Go de mémoire doit allouer 5 à 6 Go pour la taille du pool.

  - **innodb\_log\_file\_size** : les journaux de restauration par progression permettent de garantir des écritures durables rapides. Cette sauvegarde transactionnelle est utile lors d’un plantage du système. Commencer avec innodb\_log\_file\_size = 512M (avec 1 Go de journaux de restauration par progression) doit fournir suffisamment d’espace pour les écritures. Les applications nécessitant beaucoup d’écritures utilisant MySQL 5.6 ou ultérieur doivent commencer avec innodb\_log\_file\_size = 4G.

  - **max\_connections** : ce paramètre peut aider à limiter l’erreur `Too many connections`. La valeur par défaut est de 151 connexions. Il est préférable d’utiliser un pool de connexions au niveau de l’application, mais la configuration de la connexion au serveur peut également nécessiter une augmentation.

  - **innodb\_file\_per\_table** : ce paramètre indique à InnoDB s’il doit stocker les données et les index dans l’espace de table partagé ou dans un fichier .idb distinct pour chaque table. Avoir un fichier par table permet au serveur de récupérer de l’espace quand des tables sont supprimées, tronquées ou reconstruites. Les bases de données contenant un grand nombre de tables ne doivent pas utiliser la configuration « une table par fichier ». À compter de la version MySQL 5.6, la valeur par défaut est ON (Activé). Les versions des bases de données antérieures doivent définir la configuration sur ON (Activé) avant de charger les données. Ce paramètre affecte seulement les tables nouvellement créées.

  - **innodb\_flush\_log\_at\_trx\_commit** : la valeur par défaut 1 signifie qu’InnoDB est entièrement conforme à ACID. Cette configuration des transactions à risque plus faible peut constituer une surcharge importante sur les systèmes avec des disques lents, car des fsyncs supplémentaires sont nécessaires pour vider chaque modification apportée aux journaux de restauration par progression. L’affectation de la valeur 2 au paramètre est un peu moins fiable, car les transactions validées ne sont vidées dans les journaux de restauration par progression qu’une fois par seconde. Le risque peut être acceptable dans certaines situations pour la base de données maître et c’est une bonne valeur pour un réplica. Une valeur 0 permet d’améliorer les performances du système, mais le serveur de base de données est alors plus susceptible de perdre des données en cas de défaillance. Pour faire court, utilisez la valeur 0 seulement pour un réplica.

  - **innodb\_flush\_method** : ce paramètre contrôle la façon dont les données et les journaux sont vidés sur le disque. Utilisez `O_DIRECT` en présence d’un contrôleur RAID matériel avec un cache en écriture différée protégé par une batterie. Utilisez `fdatasync` (valeur par défaut) pour les autres scénarios.

  - **innodb\_log\_buffer\_size** : ce paramètre correspond à la taille de la mémoire tampon pour les transactions qui n’ont pas encore été validées. La valeur par défaut (1 Mo) est généralement appropriée. Les transactions avec des champs blob/texte de grande taille peuvent remplir rapidement la mémoire tampon et déclencher une charge d’E/S supplémentaire. Examinez la variable d’état `Innodb_log_waits`. Si elle est différente de 0, augmentez `innodb_log_buffer_size`.

  - **query\_cache\_size** : le cache des requêtes est un goulot d’étranglement bien connu qui se produit lors des accès concurrentiels modérés. La valeur initiale doit être définie sur 0 pour désactiver le cache. Exemple : `query_cache_size = 0`. C’est la valeur par défaut sur MySQL 5.6 et ultérieur.

  - **log\_bin** : ce paramètre active la journalisation binaire. L’activation de la journalisation binaire est obligatoire si le serveur doit agir en tant que maître de réplication.

  - **server\_id** : ce paramètre est une valeur unique pour les serveurs d’identité dans les topologies de réplication.

  - **expire\_logs\_days** : ce paramètre détermine le nombre de jours qui vont être vidés automatiquement dans les journaux binaires.

  - **skip\_name\_resolve** : utilisateur qui effectue la résolution du nom d’hôte du client. Si le DNS est lent, la connexion sera lente. En cas de désactivation de la résolution de noms, les instructions GRANT doivent utiliser seulement des adresses IP. Toutes les instructions GRANT effectuées antérieurement doivent être réexécutées pour utiliser l’adresse IP.

Exécutez la commande suivante pour exporter les paramètres du serveur dans un fichier pour les passer en revue. Avec une analyse simple, la sortie peut être utilisée pour réappliquer les mêmes paramètres de serveur après la migration, si c’est approprié pour le serveur Azure Database pour MySQL. Consultez [Configurer les paramètres de serveur dans Azure Database pour MySQL en utilisant le portail Azure](../howto-server-parameters.md).

`mysql -u root -p -A -e "SHOW GLOBAL VARIABLES;" > settings.txt`

Les paramètres de serveur MySQL 5.5.60 installés par défaut se trouvent dans l’annexe C.

Avant que la migration commence, exportez les paramètres de la configuration MySQL source. Comparez ces valeurs aux paramètres de l’instance de zone d’atterrissage Azure après la migration. Si des paramètres ont été modifiés par rapport à la valeur par défaut de l’instance de la zone d’atterrissage Azure cible, vérifiez qu’ils sont replacés à leur valeur d’origine après la migration. En outre, l’utilisateur de la migration doit vérifier que les paramètres du serveur peuvent être définis avant la migration.

Pour obtenir la liste des paramètres de serveur qui ne peuvent pas être configurés, consultez [Paramètres de serveur non configurables](../concepts-server-parameters.md#non-configurable-server-parameters).

#### <a name="egress-and-ingress"></a>Entrée et sortie

Pour chaque chemin et outil respectif de migration des données, les paramètres de serveur MySQL source et cible doivent être modifiés pour prendre en charge la sortie et l’entrée les plus rapides possible. Les paramètres peuvent différer en fonction de l’outil. Par exemple, un outil qui effectue une migration en parallèle peut nécessiter plus de connexions sur la source et sur la cible en comparaison d’un outil utilisant un seul thread.

Passez en revue les paramètres de délai d’expiration qui peuvent être affectés par les jeux de données. Il s’agit notamment des paramètres suivants :

  - [connect\_timeout ](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_connect_timeout)

  - [wait\_timeout ](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_wait_timeout)

En outre, passez en revue tous les paramètres qui vont affecter des valeurs maximales :

  - [max\_allowed\_packet ](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_allowed_packet)

> [!NOTE]
> `MySQL server has gone away` est une erreur courante lors d’une migration. Les paramètres mentionnés ici sont généralement à l’origine de cette erreur et doivent être modifiés pour la résoudre.

### <a name="wwi-scenario"></a>Scénario WWI

WWI a passé en revue la charge de travail de sa base de données Conference et a déterminé qu’elle avait une très petite charge. Bien qu’un serveur de niveau de base aurait bien fonctionné pour eux , ils ne souhaitaient pas effectuer des tâches ultérieurement pour migrer vers un autre niveau. Le serveur qui est déployé va au final héberger les autres charges de travail de données MySQL : ils ont donc choisi le niveau `General Performance`.

Lors de l’examen de la base de données MySQL, il apparaît que le serveur MySQL 5.5 s’exécute avec les paramètres de serveur par défaut définis lors de l’installation initiale.  

> [!div class="nextstepaction"]
> [Migration de données](./data-migration.md)