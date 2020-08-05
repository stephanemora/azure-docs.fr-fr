---
title: Journaux - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Accéder aux journaux de base de données Azure Database pour PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094336"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Journaux dans Azure Database pour PostgreSQL - Hyperscale (Citus)

Des journaux PostgreSQL sont disponibles sur chaque nœud d’un groupe de serveurs Hyperscale (Citus). Vous pouvez envoyer des journaux à un serveur de stockage ou à un service d’analytique. Les journaux d’activité peuvent servir à identifier, résoudre et réparer les erreurs de configuration et les problèmes de performances.

## <a name="accessing-logs"></a>Accès aux journaux d’activité

Pour accéder aux journaux PostgreSQL d’un coordinateur Hyperscale (Citus) ou d’un nœud Worker, ouvrez le nœud dans le portail Azure :

![Liste des nœuds](media/howto-hyperscale-logging/choose-node.png)

Pour le nœud sélectionné, ouvrez **Paramètres de diagnostic**, puis cliquez sur **+Ajouter un paramètre de diagnostic**.

![Ajouter des paramètres de diagnostic - Bouton](media/howto-hyperscale-logging/diagnostic-settings.png)

Choisissez un nom pour les nouveaux paramètres de diagnostic, puis cochez la case **PostgreSQLLogs**.  Choisissez la ou les destinations qui doivent recevoir les journaux.

![Choisir des journaux PostgreSQL](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer avec les requêtes Log Analytics](/azure/azure-monitor/log-query/get-started-portal)
- En savoir plus sur les [hubs d’événements Azure](/azure/event-hubs/event-hubs-about)
