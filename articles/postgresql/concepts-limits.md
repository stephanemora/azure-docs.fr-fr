---
title: Limitations des bases de données Azure pour PostgreSQL
description: Cet article décrit les limitations dans Azure Database pour PostgreSQL, telles que le nombre de connexions et les options du moteur de stockage.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/04/2018
ms.openlocfilehash: 5cd829236d8d8a58e68f7bf766790aa3f0cb656e
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757414"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limitations des bases de données Azure pour PostgreSQL
Les sections suivantes décrivent les limites fonctionnelles et les limites de capacités du service de base de données.

## <a name="maximum-connections"></a>Nombre maximal de connexions
Le nombre maximal de connexions par niveau tarifaire et de vCores est le suivant : 

|**Niveau tarifaire**| **vCore(s)**| **Nombre maximal de connexions** |
|---|---|---|
|De base| 1| 50 |
|De base| 2| 100 |
|Usage général| 2| 150|
|Usage général| 4| 250|
|Usage général| 8| 480|
|Usage général| 16| 950|
|Usage général| 32| 1 500|
|Mémoire optimisée| 2| 150|
|Mémoire optimisée| 4| 250|
|Mémoire optimisée| 8| 480|
|Mémoire optimisée| 16| 950|

Lorsque la limite du nombre de connexions est dépassée, vous pouvez recevoir l’erreur suivante :
> FATAL:  sorry, too many clients already

Le système Azure a besoin de cinq connexions pour effectuer le monitoring du serveur Azure Database pour PostgreSQL. 

## <a name="functional-limitations"></a>Limitations fonctionnelles
### <a name="scale-operations"></a>Opérations de mise à l’échelle
1.  La mise à l’échelle dynamique des serveurs dans les différents niveaux tarifaires n’est pas prise en charge pour le moment. Autrement dit, cela concerne le basculement entre les niveaux De base, Usage général et Mémoire optimisée.
2.  La diminution de la taille de stockage du serveur n’est pas prise en charge pour le moment.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment.

### <a name="subscription-management"></a>Gestion des abonnements
- Le déplacement dynamique de serveurs entre les groupes de ressources et d’abonnements n’est pas pris en charge pour le moment.

### <a name="point-in-time-restore-pitr"></a>Point-in-time-restore (PITR)
1.  Lorsque vous utilisez la fonctionnalité PITR, le nouveau serveur est créé avec la même configuration que le serveur sur lequel il est basé.
2.  La restauration d’un serveur supprimé n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes
- Comprendre [les éléments disponibles dans chaque niveau tarifaire](concepts-pricing-tiers.md)
- Découvrir [les versions prises en charge de la base de données PostgreSQL](concepts-supported-versions.md)
- Consulter le [guide pratique : sauvegarder et restaurer un serveur dans Azure Database pour PostgreSQL à l’aide du portail Azure](howto-restore-server-portal.md)
