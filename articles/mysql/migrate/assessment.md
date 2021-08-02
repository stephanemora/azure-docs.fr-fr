---
title: Guide de migration de MySQL local vers Azure Database pour MySQL - Évaluation
description: Avant de se lancer dans la migration d’une charge de travail MySQL, il convient d’effectuer un certain nombre de vérifications préalables.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/25/2021
ms.openlocfilehash: f5a02a9e4ec6b6c1e9961637d588a3838570c432
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969910"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-assessment"></a>Guide de migration de MySQL local vers Azure Database pour MySQL - Évaluation

Avant de se lancer dans la migration d’une charge de travail MySQL, il convient d’effectuer un certain nombre de vérifications préalables. Il s’agit notamment d’analyser les données, l’environnement d’hébergement et les charges de travail des applications afin de vérifier que la zone d’atterrissage Azure est correctement configurée et préparée pour accueillir les charges de travail qui seront bientôt migrées.

### <a name="limitations"></a>Limites

Azure Database pour MySQL est une version entièrement prise en charge de l’édition communautaire de MySQL fonctionnant comme platform as a service. Toutefois, il existe [certaines limites](../concepts-limits.md) à connaître lors de l’évaluation initiale.

Les plus importantes d’entre elles sont les suivantes :

  - Prise en charge du moteur de stockage pour `InnoDB` et `Memory` uniquement

  - Prise en charge limitée de `Privilege` (`DBA`, `SUPER`, `DEFINER`)

  - Instructions de manipulation de données désactivées (`SELECT ... INTO OUTFILE`, `LOAD DATA INFILE`)

  - Automatisation des migrations importantes de bases de données (5.6 vers 5.7, 5.7 vers 8.0)

  - Quand vous utilisez les [fonctions définies par l’utilisateur (UDF) de MySQL Server](https://dev.mysql.com/doc/refman/5.7/en/server-udfs.html), la seule option d’hébergement viable est celle des machines virtuelles hébergées sur Azure, car il n’est pas possible de charger le composant `so` ou `dll` vers Azure Database pour MySQL.

De nombreux autres éléments sont des aspects opérationnels avec lesquels les administrateurs doivent se familiariser dans le cadre de la gestion du cycle de vie des charges de travail de données opérationnelles. Ce guide explore plusieurs de ces aspects opérationnels dans la section Gestion post-migration.

### <a name="mysql-versions"></a>Versions de MySQL

MySQL a une histoire riche qui débute en 1995. Depuis lors, il a évolué pour devenir un système de gestion de base de données largement utilisé. Azure Database pour MySQL a commencé par prendre en charge la version 5.6 de MySQL, puis la version 5.7 et récemment la version 8.0. Pour connaître les informations les plus récentes sur la prise en charge des versions d’Azure Database pour MySQL, consultez [Versions prises en charge du serveur Azure Database pour MySQL](../concepts-supported-versions.md). Dans la section Gestion post-migration, nous examinons la manière dont les mises à niveau (par exemple, de 5.7.20 vers 5.7.21) sont appliquées aux instances MySQL dans Azure.

> [!NOTE]
> Le bond de 5.x à 8.0 est en grande partie dû à l’acquisition de MySQL par Oracle. Pour en savoir plus sur l’histoire de MySQL, accédez à la [page wiki de MySQL](https://en.wikipedia.org/wiki/MySQL).

Il est essentiel de connaître la version source de MySQL. Les applications qui utilisent le système peuvent utiliser des objets de base de données et des fonctionnalités qui sont spécifiques à cette version. La migration d’une base de données vers une version antérieure peut entraîner des problèmes de compatibilité et une perte de fonctionnalités. Il est également recommandé de tester minutieusement les données et l’instance d’application avant de migrer vers une version plus récente, car les fonctionnalités introduites peuvent perturber votre application.

Exemples qui peuvent influencer le chemin et la version de la migration :

  - 5.6 : colonne TIMESTAMP pour le stockage correct des millisecondes et la recherche en texte intégral

  - 5.7 : prise en charge du type de données JSON natif

  - 8.0 : prise en charge du magasin de documents NoSQL, du DDL atomique et à sécurité intégrée et des fonctions de table JSON

    > [!NOTE]
    > MySQL 5.6 ne bénéficie plus du support général depuis février 2021. Les charges de travail MySQL doivent migrer vers la version 5.7 ou ultérieure de MySQL.

Pour vérifier la version du serveur MySQL :

```
SHOW VARIABLES LIKE "%version%";
```

#### <a name="database-storage-engines"></a>Moteurs de stockage de bases de données

Azure Database pour MySQL ne prend en charge que les moteurs de stockage de bases de données [InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html) et [Memory](https://dev.mysql.com/doc/refman/8.0/en/memory-storage-engine.html). Les autres moteurs de stockage, comme [MyISAM](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html), doivent être migrés vers un moteur de stockage pris en charge. Les caractéristiques opérationnelles et les performances sont les principales différences entre MyISAM et InnoDB. En général, les tables et la structure de schéma de niveau supérieur ne changent pas entre les moteurs, mais les types de colonnes d’index et de table peuvent changer pour des raisons de stockage et de performances. Bien qu’InnoDB soit connu pour avoir des fichiers de données de grande taille, ces détails de stockage sont gérés par le service Azure Database pour MySQL.

##### <a name="migrating-from-myisam-to-innodb"></a>Migration de MyISAM vers InnoDB

La base de données et les tables MyISAM doivent être converties en tables InnoDB. Après la conversion, la compatibilité et les performances des applications doivent être testées. Dans la plupart des cas, les tests nécessitent de recréer la table et de changer le moteur de stockage cible avec des instructions DDL. Il est peu probable que ce changement doive être effectué pendant la migration, car il a lieu pendant la création du schéma dans la cible Azure. Pour plus d’informations, consultez la [documentation destinée aux développeurs sur la conversion des tables](https://dev.mysql.com/doc/refman/5.6/en/converting-tables-to-innodb.html) sur le site web de MySQL.

Pour trouver des informations utiles sur les tables, utilisez la requête suivante :

```dotnetcli
    SELECT 
        tab.table_schema,   
        tab.table_name,   
        tab.engine as engine_type,   
        tab.auto_increment,   
        tab.table_rows,   
        tab.create_time,   
        tab.update_time,   
        tco.constraint_type 
    FROM information_schema.tables tab   
    LEFT JOIN information_schema.table_constraints tco   
        ON (tab.table_schema = tco.table_schema   
            AND tab.table_name = tco.table_name   
            )   
    WHERE  
        tab.table_schema NOT IN ('mysql', 'information_schema', 'performance_
schema', 'sys')  
        AND tab.table_type = 'BASE TABLE';
```

> [!NOTE]
> L’exécution d’une requête concernant INFORMATION\_SCHEMA sur plusieurs bases de données peut avoir un impact sur les performances. Exécutez-la pendant les périodes de faible utilisation.

Pour convertir une table de MyISAM en InnoDB, exécutez la commande suivante :

```
ALTER TABLE {table\_name} ENGINE=InnoDB;
```

> [!NOTE]
> Cette approche de conversion entraîne le verrouillage de la table, et toutes les applications peuvent attendre la fin de l’opération. Le verrouillage de la table fait que la base de données semble hors connexion pendant une courte période.

Au lieu de cela, la table peut être créée avec la même structure, mais avec un moteur de stockage différent. Une fois qu’elle est créée, copiez les lignes dans la nouvelle table :

```
INSERT INTO {table\_name} SELECT * FROM {myisam\_table} ORDER BY {primary\_key\_columns}
```

> [!NOTE]
> Pour que cette approche soit efficace, il faut supprimer la table d’origine, puis renommer la nouvelle table. Cette tâche entraîne un court temps d’arrêt.

#### <a name="database-data-and-objects"></a>Données et objets de base de données

Les données sont l’un des composants de la migration de base de données. Les objets prenant en charge la base de données doivent être migrés et validés pour que les applications continuent de fonctionner de manière fiable.

Voici une liste d’éléments que vous devez inventorier avant et après la migration :

  - Tables (schéma)

  - Clés primaires, clés étrangères

  - Index

  - Fonctions

  - Procédures

  - Déclencheurs

  - Vues

#### <a name="user-defined-functions"></a>Fonctions définies par l'utilisateur

MySQL permet l’utilisation de fonctions qui appellent du code externe. Malheureusement, les charges de travail de données utilisant des fonctions définies par l’utilisateur (UDF) avec du code externe ne peuvent pas être migrées vers Azure Database pour MySQL. Cela est dû au fait que le code so ou dll sous-jacent de la fonction MySQL requise ne peut pas être chargé sur le serveur Azure.

Exécutez la requête suivante pour rechercher les UDF qui peuvent être installées :

```
SELECT * FROM mysql.func;
```

### <a name="source-systems"></a>Systèmes sources

La quantité de préparation à la migration peut varier en fonction du système source et de son emplacement. Outre les objets de base de données, il faut tenir compte de la manière dont les données seront transférées du système source au système cible. La migration des données peut devenir difficile lorsque des pare-feu et d’autres composants de mise en réseau se trouvent entre la source et la cible.

De plus, le déplacement de données sur Internet peut être plus lent que l’utilisation de circuits dédiés vers Azure. Par conséquent, lorsque vous déplacez plusieurs gigaoctets, téraoctets et pétaoctets de données, envisagez de configurer une connexion [ExpressRoute](../../expressroute/expressroute-introduction.md) entre le réseau source et le réseau Azure.

Si ExpressRoute est déjà présent, il est probable que la connexion est utilisée par d’autres applications. L’exécution d’une migration sur un itinéraire existant peut causer une surcharge sur le débit du réseau et entraîner une baisse significative des performances, tant pour la migration que pour les autres applications utilisant le réseau.

Enfin, l’espace disque doit être évalué. Lorsque vous exportez une base de données volumineuse, tenez compte de la taille des données. Vérifiez que le système sur lequel l’outil est exécuté, et au bout du compte l’emplacement d’exportation, dispose d’un espace disque suffisant pour effectuer l’opération d’exportation.

#### <a name="cloud-providers"></a>Fournisseurs de cloud

La migration de bases de données à partir de fournisseurs de services cloud tels qu’Amazon Web Services (AWS) peut nécessiter une étape supplémentaire de configuration réseau afin d’accéder aux instances MySQL hébergées dans le cloud. Les outils de migration, comme les services de migration de données, nécessitent un accès à partir de plages d’adresses IP externes et peuvent être bloqués.

#### <a name="on-premises"></a>Local

À l’instar des fournisseurs de cloud, si la charge de travail de données MySQL se trouve derrière des pare-feu ou d’autres couches de sécurité réseau, un chemin doit être créé entre l’instance locale et Azure Database pour MySQL.

### <a name="tools"></a>Outils

De nombreux outils et méthodes peuvent être utilisés pour évaluer les charges de travail et les environnements de données MySQL. Chaque outil offre un ensemble différent de caractéristiques et de fonctionnalités d’évaluation et de migration. Dans le cadre de ce guide, nous passons en revue les outils les plus couramment utilisés pour évaluer les charges de travail de données MySQL.

#### <a name="azure-migrate"></a>Azure Migrate

Bien qu’[Azure Migrate](../../migrate/migrate-services-overview.md) ne prenne pas directement en charge la migration des charges de travail de bases de données MySQL, il peut être utilisé lorsque les administrateurs ne sont pas certains des utilisateurs et des applications qui consomment les données, qu’elles soient hébergées dans une machine virtuelle ou physique. L’[analyse des dépendances](../../migrate/concepts-dependency-visualization.md) peut être réalisée en installant et en exécutant l’agent d’analyse sur la machine hébergeant la charge de travail MySQL. L’agent recueille les informations sur une période définie, par exemple un mois. Les données de dépendance peuvent être analysées pour trouver les connexions inconnues établies avec la base de données. Les données de connexion peuvent aider à identifier les propriétaires d’applications qui doivent être informés de la migration en cours.

En plus de l’analyse des dépendances des applications et des données de connectivité des utilisateurs, Azure Migrate peut également être utilisé pour analyser les [serveurs Hyper-V, VMware ou physiques](../../migrate/migrate-appliance-architecture.md) afin de fournir des modèles d’utilisation des charges de travail de base de données et suggérer l’environnement cible approprié.

#### <a name="telgraf-for-linux"></a>Telgraf pour Linux

Les charges de travail Linux peuvent utiliser [Microsoft Monitoring Agent (MMA)](../../azure-monitor/agents/agent-linux.md) pour collecter des données sur vos machines virtuelles et physiques. Vous pouvez également envisager d’utiliser l’[agent Telegraf](../../azure-monitor/essentials/collect-custom-metrics-linux-telegraf.md) et son large éventail de plug-ins pour recueillir vos mesures de performances.

#### <a name="service-tiers"></a>Niveaux de service

Muni des informations d’évaluation (UC, mémoire, stockage, etc.), l’utilisateur de la migration doit ensuite décider du [niveau tarifaire](../concepts-pricing-tiers.md) d’Azure Database pour MySQL à utiliser.

Il existe actuellement trois niveaux :

  - **De base** : charges de travail nécessitant des performances légères en matière de calcul et d’E/S.

  - **Usage général** : la plupart des charges de travail métier nécessitant une capacité de calcul et de mémoire équilibrée avec un débit d’E/S évolutif.

  - **À mémoire optimisée** : charges de travail de base de données hautes performances nécessitant des performances en mémoire suffisantes pour un traitement transactionnel plus rapide et une concurrence plus élevée.

Le choix du niveau peut être influencé par les exigences en matière de RTO et de RPO de la charge de travail de données. Lorsque la charge de travail de données nécessite plus de 4 To de stockage, une étape supplémentaire est requise. Examinez et sélectionnez [une région qui prend en charge](../concepts-pricing-tiers.md#storage) jusqu’à 16 To de stockage.

> [!NOTE]
> Contactez l’équipe de MySQL (AskAzureDBforMySQL@service.microsoft.com) pour les régions qui ne prennent pas en charge vos besoins de stockage.

En règle générale, la prise de décision se focalise sur les besoins en stockage et en IOPS (opérations d’entrée/sortie par seconde). Ainsi, le système cible a toujours besoin d’au moins autant de stockage que le système source. En outre, comme les IOPS sont allouées à raison de 3/Go, il est important de faire correspondre les besoins en IOPS à la taille de stockage finale.

| Facteurs          | Niveau                                                                                                                                                                                                            |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **De base**            | Machine de développement, pas besoin de performances élevées, avec un stockage inférieur à 1 To                                                                                                                                    |
| **Usage général**  | Besoins en IOPS supérieurs à ce que le niveau de base peut fournir, mais pour un stockage inférieur à 16 To et moins de 4 Go de mémoire                                                                                               |
| **Mémoire optimisée** | Charges de travail de données qui utilisent une grande quantité de mémoire ou une configuration de serveur liée à la mémoire cache et à la mémoire tampon, comme innodb_buffer_pool_instances à forte concurrence, les grandes tailles d’objets blob ou les systèmes avec de nombreuses cibles de réplication |

#### <a name="costs"></a>Coûts

Après avoir évalué l’ensemble des charges de travail liées aux données MySQL de WWI, la société fictive WWI a déterminé qu’elle aurait besoin d’au moins 4 vCores et 20 Go de mémoire et au moins 100 Go d’espace de stockage avec une capacité de 450 IOPS. Du fait de cette exigence de 450 IOPS, elle doit allouer au moins 150 Go de stockage en raison de la [méthode d’allocation des IOPS d’Azure Database pour MySQL](../concepts-pricing-tiers.md#storage). En outre, elle a besoin d’au moins 100 % de votre stockage serveur provisionné comme stockage de sauvegarde et d’un réplica en lecture. Elle ne prévoit pas une sortie de plus de 5 Go.

À l’aide de la [calculatrice de prix d’Azure Database pour MySQL](https://azure.microsoft.com/en-us/pricing/details/mysql/), WWI a pu déterminer les coûts de l’instance Azure Database pour MySQL. À partir de septembre 2020, les coûts totaux de possession (coûts TCO) sont affichés dans le tableau suivant pour la base de données Conference de WWI :

| Ressource                  | Description                       | Quantité                                                    | Coût          |
|---------------------------|-----------------------------------|-------------------------------------------------------------|---------------|
| **Capacité de calcul (Usage général)** | 4 vCores, 20 Go                    | 1 @ 0,351 USD/h                                               | 3074,76 USD/an |
| **Stockage**                   | 5 Go                               | 12 x 150 @ 0,115 USD                                           | 207 USD/an     |
| **Sauvegarde**                    | Jusqu’à 100 % du stockage provisionné | Aucun coût supplémentaire jusqu’à 100 % du stockage serveur provisionné | 0 USD/an    |
| **Réplica en lecture**              | Réplica de région de 1 s           | calcul + stockage                                           | 3281,76 USD/an |
| **Réseau**                   | Sortie inférieure à 5 Go/mois                | Gratuit                                                        |               |
| **Total**                     |                                   |                                                             | 6563,52 USD/an |

Après avoir examiné les coûts initiaux, le DSI de WWI a confirmé que la société utilisait Azure pour une période beaucoup plus longue que 3 ans. Elle a décidé d’utiliser des [instances de réserve](../concept-reserved-pricing.md) de 3 ans pour économiser \~4 000 USD/an :

| Ressource                  | Description                       | Quantité                                                     | Coût         |
|---------------------------|-----------------------------------|--------------------------------------------------------------|--------------|
| **Capacité de calcul (Usage général)** | 4 vCores                          | 1 @ 0,1375 USD/h                                               | 1204,5 USD/an |
| **Stockage**                   | 5 Go                               | 12 x 150 @ 0,115 USD                                            | 207 USD/an    |
| **Sauvegarde**                    | Jusqu’à 100 % du stockage provisionné | Aucun coût supplémentaire jusqu’à 100 % du stockage serveur provisionné | 0 USD/an   |
| **Réseau**                   | Sortie inférieure à 5 Go/mois                | Gratuit                                                         |              |
| **Réplica en lecture**              | Réplica de région de 1 s           | calcul + stockage                                            | 1411,5 USD/an |
| **Total**                     |                                   |                                                              | 2823 USD/an   |

Comme le montre le tableau ci-dessus, les sauvegardes, la sortie réseau et les éventuels réplicas en lecture doivent être pris en compte dans le coût total de possession (coût TCO). À mesure que des bases de données sont ajoutées, le stockage et le trafic générés sont les seuls facteurs de coût supplémentaire à prendre en compte.

> [!NOTE]
> Les estimations ci-dessus n’incluent pas les coûts associés à [ExpressRoute](../../expressroute/expressroute-introduction.md), [Azure App Gateway](../../application-gateway/overview.md), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) ou [App Service](../../app-service/overview.md) pour les couches Application.
>
> Les prix ci-dessus peuvent changer à tout moment et varient en fonction de la région.

#### <a name="application-implications"></a>Implications pour les applications

Lors de la migration vers Azure Database pour MySQL, la conversion à la communication SSL (Secure Sockets Layer) sera probablement l’un des changements les plus importants pour vos applications. Le protocole SSL est activé par défaut dans Azure Database pour MySQL, et il est probable que la charge de travail locale d’application et de données n’est pas configurée pour se connecter à MySQL en utilisant le protocole SSL. Lorsque le protocole SSL est activé, son utilisation ajoute une surcharge de traitement et doit être analysée.

> [!NOTE]
> Même si le protocole SSL est activé par défaut, vous avez la possibilité de le désactiver.

Suivez les activités de l’article [Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à Azure Database pour MySQL](../howto-configure-ssl.md) afin de reconfigurer l’application pour qu’elle prenne en charge ce chemin d’authentification forte.

Enfin, modifiez le nom du serveur dans les chaînes de connexion de l’application ou changez le DNS pour qu’il pointe vers le nouveau serveur Azure Database pour MySQL.

### <a name="wwi-scenario"></a>Scénario WWI

WWI a commencé l’évaluation en recueillant des informations sur son patrimoine de données MySQL. Elle a pu compiler les éléments suivants :

| Nom         | Source      | Moteur Bd | Taille | E/S par seconde | Version | Propriétaire          | Temps d’arrêt |
|--------------|-------------|-----------|------|------|---------|----------------|----------|
| **WwwDB**        | AWS (PaaS)  | InnoDB    | 1 Go  | 150  | 5.7     | Service marketing | 1 h     |
| **BlogDB**       | AWS (PaaS)  | InnoDB    | 1 Go  | 100  | 5.7     | Service marketing | 4 heures    |
| **ConferenceDB** | Local | InnoDB    | 5 Go  | 50   | 5.5     | Service commercial     | 4 heures    |
| **CustomerDB**   | Local |           | 10 Go | 75   | 5.5     | Service commercial     | 2 heures    |
| **SalesDB**      | Local | InnoDB    | 20 Go | 75   | 5.5     | Service commercial     | 1 h     |

Chaque propriétaire de base de données a été contacté pour déterminer une période acceptable de temps d’arrêt. La méthode de planification et de migration choisie était basée sur le temps d’arrêt acceptable de la base de données.

Pour la première phase, WWI s’est concentré uniquement sur la base de données ConferenceDB. L’équipe avait besoin de l’expérience de migration pour aider à migrer les charges de travail de données. La base de données ConferenceDB a été choisie en raison de la simplicité de sa structure et de son temps d’arrêt acceptable important. Une fois la base de données migrée, l’équipe s’est concentrée sur la migration de l’application vers la zone d’atterrissage Azure sécurisée.

### <a name="assessment-checklist"></a>Liste de vérification de l’évaluation

  - Vérifiez que la charge de travail s’exécute correctement sur le système cible.

  - Vérifiez que les composants réseau appropriés sont en place pour la migration.

  - Comprenez les besoins en ressources de la charge de travail de données.

  - Estimez les coûts totaux.

  - Comprenez les exigences en matière de temps d’arrêt.

  - Soyez prêt à apporter des modifications à l’application.


> [!div class="nextstepaction"]
> [Planification](./planning.md)