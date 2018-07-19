---
title: Limitations dans Azure Database pour MySQL
description: Cet article décrit les limitations dans Azure Database pour MySQL, telles que le nombre de connexions et les options du moteur de stockage.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/30/2018
ms.openlocfilehash: 1fd5905b8ea3f87fe6cfc2a830b73b8120a717dd
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341475"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitations dans Azure Database pour MySQL
Les sections suivantes abordent la capacité, la prise en charge du moteur de stockage, la prise en charge des privilèges, la prise en charge des instructions de manipulation des données et les limites fonctionnelles du service de base de données. Vous pouvez aussi consulter les [limitations générales](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) qui sont applicables au moteur de base de données MySQL.

## <a name="maximum-connections"></a>Nombre maximal de connexions
Le nombre maximal de connexions par niveau tarifaire et de vCores est le suivant : 

|**Niveau tarifaire**|**vCore(s)**| **Nombre maximal de connexions**|
|---|---|---|
|De base| 1| 50|
|De base| 2| 100|
|Usage général| 2| 300|
|Usage général| 4| 625|
|Usage général| 8| 1250|
|Usage général| 16| 2 500|
|Usage général| 32| 5 000|
|Mémoire optimisée| 2| 600|
|Mémoire optimisée| 4| 1250|
|Mémoire optimisée| 8| 2 500|
|Mémoire optimisée| 16| 5 000|

Lorsque la limite du nombre de connexions est dépassée, vous pouvez recevoir l’erreur suivante :
> ERROR 1040 (08004): Too many connections

## <a name="storage-engine-support"></a>Prise en charge du moteur de stockage

### <a name="supported"></a>Prise en charge
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Non pris en charge
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Prise en charge des privilèges

### <a name="unsupported"></a>Non pris en charge
- Rôle d’administrateur de bases de données : plusieurs paramètres de server peuvent dégrader les performances du serveur ou nier les propriétés ACID du système de gestion de base de données (SGBD). Par conséquent, pour préserver l’intégrité du service et le contrat SLA au niveau du produit, ce service n’expose pas le rôle d’administrateur de bases de données. Le compte d’utilisateur par défaut, qui est créé en même temps qu’une instance de base de données, permet à l’utilisateur d’exécuter la plupart des instructions DDL et DML dans l’instance de base de données gérée. 
- Privilèges de super utilisateur : de la même façon, les [privilèges de super utilisateur](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) sont eux aussi limités.

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

### <a name="vnet-service-endpoints"></a>Points de terminaison de service VNet
- Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Usage général et Mémoire optimisée.

### <a name="subscription-management"></a>Gestion des abonnements
- Le déplacement dynamique de serveurs créés au préalable entre les groupes de ressources et d’abonnements n’est pas pris en charge pour le moment.

## <a name="current-known-issues"></a>Problèmes connus
- L’instance de serveur MySQL affiche la mauvaise version de serveur une fois la connexion établie. Pour obtenir la version de moteur correcte de l’instance de serveur, utilisez la commande `select version();`.

## <a name="next-steps"></a>Étapes suivantes
- [Éléments disponibles dans chaque niveau de service](concepts-pricing-tiers.md)
- [Versions de base de données MySQL prises en charge](concepts-supported-versions.md)
