---
title: Limitations dans Azure Database pour PostgreSQL - serveur unique
description: Cet article décrit les limitations dans Azure Database pour PostgreSQL - serveur unique, comme le nombre de connexions et les options de moteur de stockage.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 01133662ca3a7364efd362f6db99d33243b2ad4b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073545"
---
# <a name="limitations-in-azure-database-for-postgresql---single-server"></a>Limitations dans Azure Database pour PostgreSQL - serveur unique
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
|Usage général| 64| 1900|
|Mémoire optimisée| 2| 300|
|Mémoire optimisée| 4| 500|
|Mémoire optimisée| 8| 960|
|Mémoire optimisée| 16| 1900|
|Mémoire optimisée| 32| 1900|

Lorsque la limite du nombre de connexions est dépassée, vous pouvez recevoir l’erreur suivante :
> FATAL:  sorry, too many clients already

Le système Azure a besoin de cinq connexions pour effectuer le monitoring du serveur Azure Database pour PostgreSQL. 

## <a name="functional-limitations"></a>Limitations fonctionnelles
### <a name="scale-operations"></a>Opérations de mise à l’échelle
- La mise à l’échelle dynamique vers et depuis les niveaux tarifaires de base n’est pas prise en charge pour le moment.
- La diminution de la taille de stockage du serveur n’est pas prise en charge pour le moment.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment. Si vous souhaitez mettre à niveau vers la version principale suivante, effectuez une [sauvegarde et une restauration](./howto-migrate-using-dump-and-restore.md) vers un serveur créé avec la nouvelle version du moteur.

### <a name="vnet-service-endpoints"></a>Points de terminaison de service VNet
- Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Usage général et Mémoire optimisée.

### <a name="restoring-a-server"></a>Restauration d’un serveur
- Lorsque vous utilisez la fonctionnalité PITR, le nouveau serveur est créé avec les mêmes configurations de niveau tarifaire que le serveur sur lequel il est basé.
- Le nouveau serveur créé lors d’une restauration ne dispose pas des règles de pare-feu qui existaient sur le serveur d’origine. Les règles de pare-feu doivent être définies séparément pour ce nouveau serveur.
- La restauration d’un serveur supprimé n’est pas prise en charge.

### <a name="utf-8-characters-on-windows"></a>Caractères UTF-8 sur Windows
- Dans certains scénarios, les caractères UTF-8 ne sont pas entièrement pris en charge dans PostgreSQL open source sur Windows, ce qui affecte la base de données Azure pour PostgreSQL. Veuillez consulter la conversation sur [le bogue 15476 # dans l’archive postgresql](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes
- Comprendre [les éléments disponibles dans chaque niveau tarifaire](concepts-pricing-tiers.md)
- Découvrir [les versions prises en charge de la base de données PostgreSQL](concepts-supported-versions.md)
- Consulter le [guide pratique : sauvegarder et restaurer un serveur dans Azure Database pour PostgreSQL à l’aide du portail Azure](howto-restore-server-portal.md)
