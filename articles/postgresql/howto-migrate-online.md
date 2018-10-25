---
title: Migration avec temps d’arrêt minimal vers Azure Database pour PostgreSQL
description: Cet article décrit comment effectuer une migration de base de données PostgreSQL vers Azure Database pour PostgreSQL avec un temps d’arrêt minimal à l’aide du service Azure Database Migration Service.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 80e5d30677735b35d90fda6288d7bf6f2ea4aa1b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093831"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migration avec temps d’arrêt minimal vers Azure Database pour PostgreSQL
Vous pouvez effectuer des migrations PostgreSQL vers Azure Database pour PostgreSQL avec un temps d’arrêt minimal à l’aide de la toute nouvelle **fonctionnalité de synchronisation continue** du service [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Cette fonctionnalité limite les temps d’arrêt affectant l’application.

## <a name="overview"></a>Vue d’ensemble
DMS procède à une charge initiale de votre instance locale vers Azure Database pour PostgreSQL, puis synchronise en continu les nouvelles transactions vers Azure tandis que l’application s’exécute. Une fois les données à jour sur le côté Azure cible, vous arrêtez momentanément l’application (temps d’arrêt minimal), vous attendez que le dernier lot de données apparaisse côté cible (à partir du moment où vous arrêtez l’application jusqu’à ce qu’elle ne soit plus disponible pour accepter du trafic), puis vous mettez à jour votre chaîne de connexion pour qu’elle pointe vers Azure. Lorsque vous avez terminé, votre application sera disponible sur Azure !

![Synchronisation continue avec le service Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

La migration DMS des sources PostgreSQL est actuellement en préversion. Si vous souhaitez essayer le service pour migrer vos charges de travail PostgreSQL, inscrivez-vous via la [page de préversion](https://aka.ms/dms-preview) Azure DMS pour nous faire part de votre intérêt. Vos commentaires sont plus que précieux pour nous aider à améliorer le service.

## <a name="next-steps"></a>Étapes suivantes
- Regardez la vidéo [App Modernization with Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) (Modernisation d’applications avec Microsoft Azure) qui contient une démonstration sur la migration d’applications PostgreSQL vers Azure Database pour PostgreSQL.
- Consultez le tutoriel [Migrer PostgreSQL vers Azure Database pour PostgreSQL en ligne à l’aide de DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
