---
title: Migration avec temps d’arrêt minimal - Azure Database pour MySQL
description: Cet article décrit comment effectuer une migration de base de données MySQL vers Azure Database pour MySQL avec un temps d’arrêt minimal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: ''
ms.date: 6/19/2021
ms.openlocfilehash: 728c1d76539eb34efe5e5fc475837810ea5a5111
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113088581"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migration avec temps d’arrêt minimal vers Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Vous pouvez effectuer des migrations MySQL vers Azure Database pour MySQL avec un temps d’arrêt minimal à l’aide de la réplication des données, ce qui limite le temps d’arrêt engendré par l’application.

Vous pouvez également consulter le [guide relatif à la migration de base de données](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) pour obtenir des informations détaillées et des cas d’usage sur la migration de base de données vers Azure Database pour MySQL. Ce guide fournit des conseils pour réussir la planification et l’exécution d’une migration MySQL vers Azure.

## <a name="overview"></a>Vue d’ensemble

À l’aide de la réplication des données, vous pouvez configurer la source comme votre principal et la cible comme votre réplica, afin qu’il existe une synchronisation continue de nouvelles transactions avec Azure lorsque l’application reste en cours d’exécution. Une fois les données à jour sur le côté Azure cible, vous arrêtez momentanément l’application (temps d’arrêt minimal), vous attendez que le dernier lot de données apparaisse côté cible (à partir du moment où vous arrêtez l’application jusqu’à ce qu’elle ne soit plus disponible pour accepter du trafic), puis vous mettez à jour votre chaîne de connexion pour qu’elle pointe vers Azure. Lorsque vous avez terminé, votre application sera disponible sur Azure !

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la migration de bases de données vers Azure Database pour MySQL, consultez le [Guide de migration des bases de données](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
