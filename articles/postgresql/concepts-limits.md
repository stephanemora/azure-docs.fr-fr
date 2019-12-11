---
title: Limites - Azure Database pour PostgreSQL - Serveur unique
description: Cet article décrit les limites dans Azure Database pour PostgreSQL - Serveur unique, telles que le nombre de connexions et les options du moteur de stockage.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: d74206ebdf35a8f5b353553cb89e954cb2313611
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768535"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limites dans Azure Database pour PostgreSQL - Serveur unique
Les sections suivantes décrivent les limites fonctionnelles et les limites de capacités du service de base de données. Si vous souhaitez en savoir plus sur les niveaux de ressources (calcul, mémoire, stockage), consultez l'article [Niveaux de tarification](concepts-pricing-tiers.md).


## <a name="maximum-connections"></a>Nombre maximal de connexions
Le nombre maximal de connexions par niveau tarifaire et de vCores est le suivant : 

|**Niveau tarifaire**| **vCore(s)**| **Nombre maximal de connexions** | **Nombre maximal de connexions utilisateur** |
|---|---|---|---|
|De base| 1| 55 | 50|
|De base| 2| 105 | 100|
|Usage général| 2| 150| 145|
|Usage général| 4| 250| 245|
|Usage général| 8| 480| 475|
|Usage général| 16| 950| 945|
|Usage général| 32| 1 500| 1495|
|Usage général| 64| 1900| 1895|
|Mémoire optimisée| 2| 300| 295|
|Mémoire optimisée| 4| 500| 495|
|Mémoire optimisée| 8| 960| 955|
|Mémoire optimisée| 16| 1900| 1895|
|Mémoire optimisée| 32| 1987| 1982|

Lorsque la limite du nombre de connexions est dépassée, vous pouvez recevoir l’erreur suivante :
> FATAL:  sorry, too many clients already

Le système Azure a besoin de cinq connexions pour effectuer le monitoring du serveur Azure Database pour PostgreSQL. 

## <a name="functional-limitations"></a>Limitations fonctionnelles
### <a name="scale-operations"></a>Opérations de mise à l’échelle
- La mise à l’échelle dynamique vers et depuis les niveaux tarifaires de base n’est pas prise en charge pour le moment.
- La diminution de la taille de stockage du serveur n’est pas prise en charge pour le moment.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment. Si vous souhaitez mettre à niveau vers la version principale suivante, effectuez une [sauvegarde et une restauration](./howto-migrate-using-dump-and-restore.md) vers un serveur créé avec la nouvelle version du moteur.

> Notez qu’avant PostgreSQL version 10, la [stratégie de gestion de versions PostgreSQL](https://www.postgresql.org/support/versioning/) considérait une mise à niveau _principale_ comme une augmentation du premier _ou_ du deuxième nombre (par exemple, 9.5 à 9.6 était considéré comme une mise à niveau de version _principale_).
> À compter de la version 10, seule une modification du premier nombre est considérée comme une mise à niveau de version principale (par exemple, 10.0 à 10.1 est une mise à niveau de version _mineure_ et 10 à 11 est une mise à niveau de version _principale_).

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
