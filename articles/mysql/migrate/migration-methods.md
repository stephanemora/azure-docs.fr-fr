---
title: Guide de migration de MySQL local vers Azure Database pour MySQL - Méthodes de migration
description: Le transfert des données de la source vers la cible nécessite l’utilisation d’outils ou de fonctionnalités de MySQL pour effectuer la migration.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 98db2aacd0b49406c3f70d0da89dfb6e991112a7
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962503"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-migration-methods"></a>Guide de migration de MySQL local vers Azure Database pour MySQL - Méthodes de migration

Le transfert des données de la source vers la cible nécessite l’utilisation d’outils ou de fonctionnalités de MySQL pour effectuer la migration.

Il est important d’effectuer toutes les étapes d’évaluation et de planification avant de commencer les étapes suivantes. Les décisions et les données collectées sont des dépendances de migration et de sélection d’outils.

Dans cette section, nous explorons les outils couramment utilisés :

  - MySQL Workbench

  - mysqldump

  - mydumper et myloader

  - Réplication de données en cours (binlog)

### <a name="mysql-workbench"></a>MySQL Workbench

[MySQL Workbench](https://www.mysql.com/products/workbench/) offre une interface graphique utilisateur riche qui permet aux développeurs et aux administrateurs de concevoir, de développer et de gérer leurs instances MySQL.

La dernière version de MySQL Workbench fournit des [fonctionnalités de migration d’objets](https://www.mysql.com/products/workbench/migrate/) sophistiquées lors du déplacement d’une base de données d’une source vers une cible.

#### <a name="data-import-and-export"></a>Importation et exportation de données

MySQL Workbench fournit une interface utilisateur basée sur un Assistant pour effectuer une exportation complète ou partielle des tables et des objets de base de données. Pour obtenir un exemple d’utilisation de MySQL Workbench, consultez [Migrer votre base de données MySQL à l’aide de l’importation et de l’exportation](../concepts-migrate-import-export.md).

### <a name="dump-and-restore-mysqldump"></a>Vidage et restauration (mysqldump)

`mysqldump` est généralement fourni dans le cadre de l’installation de MySQL. Il s’agit d’un [utilitaire client](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) qui peut être exécuté pour créer des sauvegardes logiques qui correspondent à un ensemble d’instructions SQL qui peuvent être relues pour reconstruire la base de données à un point dans le temps. `mysqldump` n’est pas conçu comme une solution rapide ou scalable pour la sauvegarde ou la migration de grandes quantités de données. L’exécution d’un grand ensemble d’instructions SQL INSERT peut avoir des performances médiocres en raison des E/S disque nécessaires à la mise à jour des index. Toutefois, lorsqu’il est associé à d’autres outils qui nécessitent le schéma d’origine, `mysqldump` est un outil formidable pour générer la base de données et les schémas de table. Les schémas peuvent créer l’environnement de la zone d’atterrissage cible.

L’utilitaire `mysqldump`  fournit des fonctionnalités utiles pendant la phase de migration des données. Les considérations relatives aux performances doivent être évaluées avant l’exécution de l’utilitaire. Consultez [Considérations relatives aux performances](../concepts-migrate-dump-restore.md#performance-considerations).

### <a name="mydumper-and-myloader"></a>mydumper et myloader

Les environnements avec des bases de données volumineuses nécessitant une migration rapide doivent utiliser [mydumper et myloader.](https://github.com/maxbube/mydumper) Ces outils sont écrits en C++ et utilisent des techniques multithread pour envoyer les données aussi rapidement que possible à l’instance MySQL cible. `mydumper` et `myloader` tirent parti du parallélisme et peuvent accélérer la migration d’un facteur de 10 ou plus.

Les versions binaires des outils disponibles pour le téléchargement public ont été compilées pour Linux. Pour exécuter ces outils sur Windows, les projets open source doivent être recompilés. La compilation du code source et la création de mises en production ne sont pas une tâche simple pour la plupart des utilisateurs.

### <a name="data-in-replication-binlog"></a>Réplication de données en cours (binlog)

Comme pour d’autres systèmes de gestion de base de données, MySQL fournit une fonctionnalité de réplication de journal nommée [réplication binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). La fonctionnalité de réplication `binlog` permet de migrer les données et de créer des réplicas en lecture.

Utilisez la réplication binlog pour [migrer vos données vers Azure Database pour MySQL](../concepts-data-in-replication.md) dans un scénario en ligne. La réplication des données permet de réduire le temps d’arrêt nécessaire pour apporter des modifications aux données cibles finales.

Pour pouvoir utiliser la fonctionnalité de réplication `binlog`, certaines conditions d’installation sont [requises :](../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)

  - Il est recommandé au serveur maître d’utiliser le moteur InnoDB MySQL. Si vous utilisez un moteur de stockage autre que InnoDB, vous devez migrer ces tables vers InnoDB.

  - L’utilisateur doit disposer des autorisations nécessaires pour configurer la journalisation binaire et créer des utilisateurs sur le serveur maître.

  - Si le protocole SSL est activé sur le serveur maître, vérifiez que le certificat d’autorité de certification SSL fourni pour le domaine a été inclus dans la procédure stockée mysql.az\_replication\_change\_master. Consultez les [exemples](../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) suivants et le paramètre master\_ssl\_ca.

  - Vérifiez que l’adresse IP du serveur maître a été ajoutée aux règles de pare-feu du serveur réplica Azure Database pour MySQL. Mettez à jour les règles de pare-feu à l’aide du portail Azure ou d’Azure CLI.

  - Vérifiez que la machine qui héberge le serveur maître autorise à la fois le trafic entrant et le trafic sortant sur le port 3306.

  - Vérifiez que le serveur maître a une adresse IP accessible (publique ou privée) entre la source et les cibles.

Pour effectuer une migration à l’aide de la réplication, consultez [Comment configurer Azure Database pour MySQL pour la réplication de données entrantes MySQL](../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication).

La méthode de réplication `binlog` a des exigences de stockage et de processeur élevées. Les utilisateurs de la migration doivent tester la charge placée sur le système source pendant les migrations en ligne et déterminer si elles sont acceptables.

### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

[Azure Database Migration Services (DMS)](https://azure.microsoft.com/en-us/services/database-migration/) est un outil cloud Azure qui permet aux administrateurs d’effectuer le suivi des différents paramètres pour la migration et de les réutiliser si nécessaire. DMS fonctionne en créant des projets de migration avec des paramètres qui pointent vers différentes sources et destinations. Il prend en charge les [migrations hors connexion](../../dms/tutorial-mysql-azure-mysql-offline-portal.md). Il prend aussi en charge les charges de travail de données locales et les charges de travail cloud, telles qu’Amazon Relational Database Service (RDS) MySQL.

Bien que le service DMS soit un outil en ligne, il s’appuie sur la fonctionnalité de réplication `binlog` de MySQL pour effectuer ses tâches. À l’heure actuelle, DMS automatise partiellement le processus de migration hors connexion. DMS exige la génération et l’application du schéma correspondant dans l’instance de base de données Azure pour MySQL cible. Les schémas peuvent être exportés à l’aide de l’utilitaire client `mysqldump`.

### <a name="fastestminimum-downtime-migration"></a>Migration la plus rapide avec un minimum de temps d’arrêt

Il existe de nombreuses approches pour la migration des données. Le choix de l’approche est fonction de l’ensemble de compétences de l’équipe de migration et du temps d’arrêt que les propriétaires de base de données et d’applications sont prêts à accepter. Certains outils prennent en charge les approches de migration de données parallèles multithread, tandis que d’autres outils ont été conçus pour des migrations simples de données de table uniquement.

L’approche la plus rapide et la plus complète consiste à utiliser la réplication `binlog` (soit directement avec MySQL, soit par le biais de DMS), mais elle implique les coûts liés à l’ajout de clés primaires.

### <a name="decision-table"></a>Table de décision

WWI peut choisir parmi de nombreuses approches pour la migration de ses charges de travail MySQL. Nous avons fourni un tableau des approches potentielles, ainsi que les avantages et inconvénients de chacune :

| Objectif                           | Description                                | Outil                             | Prérequis        | Avantages          | Inconvénients                                   |
|-------------------------------------|--------------------------------------------|----------------------------------|----------------------|---------------------|-------------------------------------------------|
| **Migration la plus rapide possible**          | Approche parallèle                          | mydumper et myloader            | Linux                | Hautement parallélisé | Limitation cible                               |
| **Migration en ligne**                    | Maintenez la session active le plus longtemps possible | binlog                           | Aucun                 | Fluide            | Traitement et stockage supplémentaires                    |
| **Migration hors connexion**                    | Maintenez la session active le plus longtemps possible | Database Migration Service (DMS) | Aucun                 | Processus reproductible  | Limité uniquement aux données, prend en charge toutes les versions de MySQL |
| **Migration hors connexion hautement personnalisée** | Exporter des objets de manière sélective                 | mysqldump                        | Aucun                 | Hautement personnalisable | Manuel                                          |
| **Migration hors connexion semi-automatisée**    | Exportation et importation à partir de l’interface utilisateur                 | MySQL Workbench                  | Télécharger et installer | Semi-automatisé      | Seuls les ensembles courants de commutateurs sont pris en charge      |

### <a name="wwi-scenario"></a>Scénario WWI

WWI a sélectionné sa base de données de conférence comme première charge de travail de migration. La charge de travail a été choisie parce qu’elle présentait le risque le moins élevé et les temps d’arrêt les plus disponibles en raison de l’écart dans le calendrier de conférence annuel. En outre, en fonction de l’évaluation de l’équipe de migration, l’équipe a déterminé qu’elle tentait d’effectuer une migration hors connexion à l’aide de MySQL Workbench.

### <a name="migration-methods-checklist"></a>Check-list des méthodes de migration

  - Vérifiez que la méthode appropriée est sélectionnée en fonction des environnements cible et source.

  - Vérifiez que la méthode peut répondre aux besoins de l’entreprise.

  - Vérifiez toujours si la charge de travail des données prend en charge la méthode.  


> [!div class="nextstepaction"]
> [Test Plans](./test-plans.md)