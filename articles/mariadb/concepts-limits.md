---
title: Limitations - Azure Database for MariaDB
description: Cet article décrit les limitations dans Azure Database for MariaDB, telles que le nombre de connexions et les options du moteur de stockage.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 18f227c1888e0565eebb640fa61ced56dc994865
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632343"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitations dans Azure Database for MariaDB
Les sections suivantes abordent la capacité, la prise en charge du moteur de stockage, la prise en charge des privilèges, la prise en charge des instructions de manipulation des données et les limites fonctionnelles du service de base de données.

## <a name="server-parameters"></a>Paramètres de serveur

Les valeurs minimales et maximales de plusieurs paramètres de serveur populaires sont déterminées par le niveau tarifaire et les vCores. Reportez-vous aux tableaux ci-dessous pour connaître les limites.

### <a name="max_connections"></a>max_connections

|**Niveau tarifaire**|**vCore(s)**|**Valeur par défaut**|**Valeur minimale**|**Valeur maximale**|
|---|---|---|---|---|
|De base|1|50|10|50|
|De base|2|100|10|100|
|Usage général|2|300|10|600|
|Usage général|4|625|10|1250|
|Usage général|8|1250|10|2 500|
|Usage général|16|2 500|10|5 000|
|Usage général|32|5 000|10|10000|
|Usage général|64|10000|10|20000|
|Mémoire optimisée|2|600|10|800|
|Mémoire optimisée|4|1250|10|2 500|
|Mémoire optimisée|8|2 500|10|5 000|
|Mémoire optimisée|16|5 000|10|10000|
|Mémoire optimisée|32|10000|10|20000|

Lorsque la limite du nombre de connexions est dépassée, vous pouvez recevoir l’erreur suivante :
> ERREUR 1040 (08004) : Trop de connexions

> [!IMPORTANT]
> Pour une expérience optimale, nous vous recommandons d’utiliser un regroupement de connexions comme ProxySQL pour gérer efficacement les connexions.

La création de connexions clientes à MariaDB prend du temps et, une fois établies, ces connexions occupent des ressources de base de données, même lorsqu’elles sont inactives. La plupart des applications requièrent de nombreuses connexions à courte durée, ce qui aggrave la situation. Par conséquent, il y a moins de ressources disponibles pour votre charge de travail réelle; ce qui entraîne une diminution des performances. Un regroupement de connexions qui réduit les connexions inactives et réutilise les connexions existantes permet d’éviter cela. Pour en savoir plus sur la configuration de ProxySQL, consultez notre [billet de blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

### <a name="query_cache_size"></a>query_cache_size

Le cache des requêtes est désactivé par défaut. Pour activer le cache des requêtes, configurez le paramètre `query_cache_type`. 

Consultez la [documentation MariaDB](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) pour en savoir plus sur ce paramètre.

|**Niveau tarifaire**|**vCore(s)**|**Valeur par défaut**|**Valeur minimale**|**Valeur maximale**|
|---|---|---|---|---|
|De base|1|Non configurable dans le niveau de base|N/A|N/A|
|De base|2|Non configurable dans le niveau de base|N/A|N/A|
|Usage général|2|0|0|16777216|
|Usage général|4|0|0|33554432|
|Usage général|8|0|0|67108864|
|Usage général|16|0|0|134217728|
|Usage général|32|0|0|134217728|
|Usage général|64|0|0|134217728|
|Mémoire optimisée|2|0|0|33554432|
|Mémoire optimisée|4|0|0|67108864|
|Mémoire optimisée|8|0|0|134217728|
|Mémoire optimisée|16|0|0|134217728|
|Mémoire optimisée|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

Consultez la [documentation MariaDB](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) pour en savoir plus sur ce paramètre.

|**Niveau tarifaire**|**vCore(s)**|**Valeur par défaut**|**Valeur minimale**|**Valeur maximale**|
|---|---|---|---|---|
|De base|1|Non configurable dans le niveau de base|N/A|N/A|
|De base|2|Non configurable dans le niveau de base|N/A|N/A|
|Usage général|2|524 288|32 768|4 194 304|
|Usage général|4|524 288|32 768|8388608|
|Usage général|8|524 288|32 768|16777216|
|Usage général|16|524 288|32 768|33554432|
|Usage général|32|524 288|32 768|33554432|
|Usage général|64|524 288|32 768|33554432|
|Mémoire optimisée|2|524 288|32 768|8388608|
|Mémoire optimisée|4|524 288|32 768|16777216|
|Mémoire optimisée|8|524 288|32 768|33554432|
|Mémoire optimisée|16|524 288|32 768|33554432|
|Mémoire optimisée|32|524 288|32 768|33554432|

### <a name="join_buffer_size"></a>join_buffer_size

Consultez la [documentation MariaDB](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) pour en savoir plus sur ce paramètre.

|**Niveau tarifaire**|**vCore(s)**|**Valeur par défaut**|**Valeur minimale**|**Valeur maximale**|
|---|---|---|---|---|
|De base|1|Non configurable dans le niveau de base|N/A|N/A|
|De base|2|Non configurable dans le niveau de base|N/A|N/A|
|Usage général|2|262 144|128|268435455|
|Usage général|4|262 144|128|536870912|
|Usage général|8|262 144|128|1073741824|
|Usage général|16|262 144|128|2147483648|
|Usage général|32|262 144|128|4294967295|
|Usage général|64|262 144|128|4294967295|
|Mémoire optimisée|2|262 144|128|536870912|
|Mémoire optimisée|4|262 144|128|1073741824|
|Mémoire optimisée|8|262 144|128|2147483648|
|Mémoire optimisée|16|262 144|128|4294967295|
|Mémoire optimisée|32|262 144|128|4294967295|

### <a name="max_heap_table_size"></a>max_heap_table_size

Consultez la [documentation MariaDB](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) pour en savoir plus sur ce paramètre.

|**Niveau tarifaire**|**vCore(s)**|**Valeur par défaut**|**Valeur minimale**|**Valeur maximale**|
|---|---|---|---|---|
|De base|1|Non configurable dans le niveau de base|N/A|N/A|
|De base|2|Non configurable dans le niveau de base|N/A|N/A|
|Usage général|2|16777216|16384|268435455|
|Usage général|4|16777216|16384|536870912|
|Usage général|8|16777216|16384|1073741824|
|Usage général|16|16777216|16384|2147483648|
|Usage général|32|16777216|16384|4294967295|
|Usage général|64|16777216|16384|4294967295|
|Mémoire optimisée|2|16777216|16384|536870912|
|Mémoire optimisée|4|16777216|16384|1073741824|
|Mémoire optimisée|8|16777216|16384|2147483648|
|Mémoire optimisée|16|16777216|16384|4294967295|
|Mémoire optimisée|32|16777216|16384|4294967295|

### <a name="tmp_table_size"></a>tmp_table_size

Consultez la [documentation MariaDB](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) pour en savoir plus sur ce paramètre.

|**Niveau tarifaire**|**vCore(s)**|**Valeur par défaut**|**Valeur minimale**|**Valeur maximale**|
|---|---|---|---|---|
|De base|1|Non configurable dans le niveau de base|N/A|N/A|
|De base|2|Non configurable dans le niveau de base|N/A|N/A|
|Usage général|2|16777216|1 024|67108864|
|Usage général|4|16777216|1 024|134217728|
|Usage général|8|16777216|1 024|268435456|
|Usage général|16|16777216|1 024|536870912|
|Usage général|32|16777216|1 024|1073741824|
|Usage général|64|16777216|1 024|1073741824|
|Mémoire optimisée|2|16777216|1 024|134217728|
|Mémoire optimisée|4|16777216|1 024|268435456|
|Mémoire optimisée|8|16777216|1 024|536870912|
|Mémoire optimisée|16|16777216|1 024|1073741824|
|Mémoire optimisée|32|16777216|1 024|1073741824|

### <a name="time_zone"></a>time_zone

Les tables de fuseaux horaires peuvent être remplies en appelant la procédure stockée `mysql.az_load_timezone` à partir d’un outil tel que la ligne de commande MySQL ou MySQL Workbench. Pour savoir comment appeler la procédure stockée et définir les fuseaux horaires au niveau global ou au niveau de la session, reportez-vous aux articles relatif au [Portail Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou à [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter).

## <a name="storage-engine-support"></a>Prise en charge du moteur de stockage

### <a name="supported"></a>Prise en charge
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Non pris en charge
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Prise en charge des privilèges

### <a name="unsupported"></a>Non pris en charge
- Rôle d’administrateur de base de données : plusieurs paramètres de serveur peuvent dégrader de façon inattendue les performances du serveur ou nier les propriétés ACID du système de gestion de base de données. Par conséquent, pour préserver l’intégrité du service et le contrat SLA au niveau du produit, ce service n’expose pas le rôle d’administrateur de bases de données. Le compte d’utilisateur par défaut, qui est créé en même temps qu’une instance de base de données, permet à l’utilisateur d’exécuter la plupart des instructions DDL et DML dans l’instance de base de données gérée.
- Privilège de superutilisateur : de la même façon, les [privilèges de superutilisateur](https://mariadb.com/kb/en/library/grant/#global-privileges) sont eux aussi limités.
- DEFINER : requiert des privilèges de superutilisateur pour créer et est limité. Si vous importez des données à l'aide d'une sauvegarde, supprimez les commandes `CREATE DEFINER` manuellement ou à l'aide de la commande `--skip-definer` lors de l'exécution de mysqldump.

## <a name="data-manipulation-statement-support"></a>Prise en charge des instructions de manipulation des données

### <a name="supported"></a>Prise en charge
- `LOAD DATA INFILE` est prise en charge, mais le paramètre `[LOCAL]` doit être spécifié et dirigé vers un chemin d'accès UNC (stockage Azure monté via SMB).

### <a name="unsupported"></a>Non pris en charge
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Limitations fonctionnelles

### <a name="scale-operations"></a>Opérations de mise à l’échelle
- La mise à l’échelle dynamique vers et depuis les niveaux tarifaires de base n’est pas prise en charge pour le moment.
- La diminution de la taille de stockage du serveur n’est pas prise en charge.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment.

### <a name="point-in-time-restore"></a>Restauration dans le temps
- Lorsque vous utilisez la fonctionnalité PITR, le nouveau serveur est créé avec la même configuration que le serveur sur lequel il est basé.
- La restauration d’un serveur supprimé n’est pas prise en charge.

### <a name="subscription-management"></a>Gestion des abonnements
- Le déplacement dynamique de serveurs créés au préalable entre les groupes de ressources et d’abonnements n’est pas pris en charge pour le moment.

### <a name="vnet-service-endpoints"></a>Points de terminaison de service VNet
- Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Usage général et Mémoire optimisée.

### <a name="storage-size"></a>Taille de stockage
- Veuillez vous reporter aux [niveaux tarifaires](concepts-pricing-tiers.md) pour les limites de taille de stockage par niveau tarifaire.

## <a name="current-known-issues"></a>Problèmes connus
- L’instance de serveur MariaDB affiche la version de serveur incorrecte une fois la connexion établie. Pour obtenir la version de moteur correcte de l’instance de serveur, utilisez la commande `select version();`.

## <a name="next-steps"></a>Étapes suivantes
- [Éléments disponibles dans chaque niveau de service](concepts-pricing-tiers.md)
- [Versions de base de données MariaDB prises en charge](concepts-supported-versions.md)
