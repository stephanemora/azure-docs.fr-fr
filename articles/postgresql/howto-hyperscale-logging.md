---
title: Journaux - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Accéder aux journaux de base de données Azure Database pour PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 1dc7bc8e119de7c8fdcf09713286be2633457486
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90895871"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Journaux dans Azure Database pour PostgreSQL - Hyperscale (Citus)

Des journaux PostgreSQL sont disponibles sur chaque nœud d’un groupe de serveurs Hyperscale (Citus). Vous pouvez envoyer des journaux à un serveur de stockage ou à un service d’analytique. Les journaux d’activité peuvent servir à identifier, résoudre et réparer les erreurs de configuration et les problèmes de performances.

## <a name="accessing-logs"></a>Accès aux journaux d’activité

Pour accéder aux journaux PostgreSQL d’un coordinateur Hyperscale (Citus) ou d’un nœud Worker, ouvrez le nœud dans le portail Azure :

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="Liste des nœuds":::

Pour le nœud sélectionné, ouvrez **Paramètres de diagnostic**, puis cliquez sur **+Ajouter un paramètre de diagnostic**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Liste des nœuds":::

Choisissez un nom pour les nouveaux paramètres de diagnostic, puis cochez la case **PostgreSQLLogs**.  Choisissez la ou les destinations qui doivent recevoir les journaux.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="Liste des nœuds":::

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer avec les requêtes Log Analytics](/azure/azure-monitor/log-query/get-started-portal)
- En savoir plus sur les [hubs d’événements Azure](/azure/event-hubs/event-hubs-about)
