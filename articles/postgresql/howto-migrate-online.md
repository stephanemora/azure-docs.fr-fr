---
title: Migration avec temps d’arrêt minimal vers Azure Database pour PostgreSQL
description: Cet article décrit comment effectuer une migration de base de données PostgreSQL vers Azure Database pour PostgreSQL avec un temps d’arrêt minimal à l’aide du service Azure Database Migration Service.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: ceb64781dc7e5243f785ad239c24e5f21b0481ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421211"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migration avec temps d’arrêt minimal vers Azure Database pour PostgreSQL
Vous pouvez effectuer des migrations PostgreSQL vers Azure Database pour PostgreSQL avec un temps d’arrêt minimal à l’aide de la toute nouvelle **fonctionnalité de synchronisation continue** du service [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Cette fonctionnalité limite les temps d’arrêt affectant l’application.

## <a name="overview"></a>Vue d'ensemble
Azure DMS procède à une charge initiale de votre instance locale vers Azure Database pour PostgreSQL, puis synchronise en continu les nouvelles transactions vers Azure tandis que l’application s’exécute. Une fois les données à jour sur le côté Azure cible, vous arrêtez momentanément l’application (temps d’arrêt minimal), vous attendez que le dernier lot de données apparaisse côté cible (à partir du moment où vous arrêtez l’application jusqu’à ce qu’elle ne soit plus disponible pour accepter du trafic), puis vous mettez à jour votre chaîne de connexion pour qu’elle pointe vers Azure. Lorsque vous avez terminé, votre application sera disponible sur Azure !

![Synchronisation continue avec le service Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Étapes suivantes
- Regardez la vidéo [App Modernization with Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) (Modernisation d’applications avec Microsoft Azure) qui contient une démonstration sur la migration d’applications PostgreSQL vers Azure Database pour PostgreSQL.
- Consultez le tutoriel [Migrer PostgreSQL vers Azure Database pour PostgreSQL en ligne à l’aide de DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
