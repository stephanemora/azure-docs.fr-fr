---
title: Limitations des bases de données Azure pour PostgreSQL
description: Cet article décrit les limitations dans Azure Database pour PostgreSQL, telles que le nombre de connexions et les options du moteur de stockage.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/30/2018
ms.openlocfilehash: 52dfac826de86f67b3143cce49c35088547c4b39
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171770"
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
- La mise à l’échelle dynamique vers et depuis les niveaux tarifaires de base n’est pas prise en charge pour le moment.
- La diminution de la taille de stockage du serveur n’est pas prise en charge pour le moment.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment.

### <a name="subscription-management"></a>Gestion des abonnements
- Le déplacement dynamique de serveurs entre les groupes de ressources et d’abonnements n’est pas pris en charge pour le moment.

### <a name="vnet-service-endpoints"></a>Points de terminaison de service VNet
- Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Usage général et Mémoire optimisée.

### <a name="restoring-a-server"></a>Restauration d’un serveur
- Lorsque vous utilisez la fonctionnalité PITR, le nouveau serveur est créé avec les mêmes configurations de niveau tarifaire que le serveur sur lequel il est basé.
- Le nouveau serveur créé lors d’une restauration ne dispose pas des règles de pare-feu qui existaient sur le serveur d’origine. Les règles de pare-feu doivent être définies séparément pour ce nouveau serveur.
- La restauration d’un serveur supprimé n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes
- Comprendre [les éléments disponibles dans chaque niveau tarifaire](concepts-pricing-tiers.md)
- Découvrir [les versions prises en charge de la base de données PostgreSQL](concepts-supported-versions.md)
- Consulter le [guide pratique : sauvegarder et restaurer un serveur dans Azure Database pour PostgreSQL à l’aide du portail Azure](howto-restore-server-portal.md)
