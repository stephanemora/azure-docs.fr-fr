---
title: Migration avec temps d’arrêt minimal vers Azure Database pour MySQL
description: Cet article décrit comment effectuer une migration de base de données MySQL vers Azure Database pour MySQL avec un temps d’arrêt minimal à l’aide du service Azure Database Migration Service.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 55d5cf97225508d6c7c490347cfe21ced832300e
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091712"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migration avec temps d’arrêt minimal vers Azure Database pour MySQL
Vous pouvez effectuer des migrations MySQL vers Azure Database pour MySQL avec un temps d’arrêt minimal à l’aide de la toute nouvelle **fonctionnalité de synchronisation continue** du service [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Cette fonctionnalité limite les temps d’arrêt affectant l’application.

## <a name="overview"></a>Vue d’ensemble
DMS procède à une charge initiale de votre instance locale vers Azure Database pour MySQL, puis synchronise en continu les nouvelles transactions vers Azure tandis que l’application s’exécute. Une fois les données à jour sur le côté Azure cible, vous arrêtez momentanément l’application (temps d’arrêt minimal), vous attendez que le dernier lot de données apparaisse côté cible (à partir du moment où vous arrêtez l’application jusqu’à ce qu’elle ne soit plus disponible pour accepter du trafic), puis vous mettez à jour votre chaîne de connexion pour qu’elle pointe vers Azure. Lorsque vous avez terminé, votre application sera disponible sur Azure !

![Synchronisation continue avec le service Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

- La migration DMS des sources MySQL est actuellement en préversion publique. Si vous souhaitez essayer le service pour migrer vos charges de travail MySQL, accédez au portail. Vos commentaires sont plus que précieux pour nous aider à améliorer le service.

## <a name="next-steps"></a>Étapes suivantes
- Regardez la vidéo [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (Migration facile des applications MySQL/PostgreSQL vers un service géré Azure) qui comprend une démonstration sur la migration des applications MySQL vers Azure Database pour MySQL.
- [Migration de MySQL vers Azure Database pour MySQL en ligne à l’aide de DMS] (https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
