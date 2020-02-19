---
title: Limitations - Azure Database for MariaDB
description: Cet article décrit les limitations dans Azure Database for MariaDB, telles que le nombre de connexions et les options du moteur de stockage.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: df44cbefaec943a2df483f4804650b939c796cb5
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191148"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitations dans Azure Database for MariaDB
Les sections suivantes abordent la capacité, la prise en charge du moteur de stockage, la prise en charge des privilèges, la prise en charge des instructions de manipulation des données et les limites fonctionnelles du service de base de données.

## <a name="maximum-connections"></a>Nombre maximal de connexions
Le nombre maximal de connexions par niveau tarifaire et de vCores est le suivant :

|**Niveau tarifaire**|**vCore(s)**| **Nombre maximal de connexions**|
|---|---|---|
|De base| 1| 50|
|De base| 2| 100|
|Usage général| 2| 600|
|Usage général| 4| 1250|
|Usage général| 8| 2 500|
|Usage général| 16| 5 000|
|Usage général| 32| 10000|
|Usage général| 64| 20000|
|Mémoire optimisée| 2| 800|
|Mémoire optimisée| 4| 2 500|
|Mémoire optimisée| 8| 5 000|
|Mémoire optimisée| 16| 10000|
|Mémoire optimisée| 32| 20000|

Lorsque la limite du nombre de connexions est dépassée, vous pouvez recevoir l’erreur suivante :
> ERREUR 1040 (08004) : Trop de connexions

> [!IMPORTANT]
> Pour une expérience optimale, nous vous recommandons d’utiliser un regroupement de connexions comme ProxySQL pour gérer efficacement les connexions.

La création de connexions clientes à MariaDB prend du temps et, une fois établies, ces connexions occupent des ressources de base de données, même lorsqu’elles sont inactives. La plupart des applications requièrent de nombreuses connexions à courte durée, ce qui aggrave la situation. Par conséquent, il y a moins de ressources disponibles pour votre charge de travail réelle; ce qui entraîne une diminution des performances. Un regroupement de connexions qui réduit les connexions inactives et réutilise les connexions existantes permet d’éviter cela. Pour en savoir plus sur la configuration de ProxySQL, consultez notre [billet de blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

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
