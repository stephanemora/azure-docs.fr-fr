---
title: Journaux - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Accéder aux journaux de base de données Azure Database pour PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/20/2021
ms.openlocfilehash: c65d2947e6e0f9505f1827ec8dbb32f59855d332
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633889"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Journaux dans Azure Database pour PostgreSQL - Hyperscale (Citus)

Les journaux du serveur de base de données PostgreSQL sont disponibles pour chaque nœud d’un groupe de serveurs Hyperscale (Citus). Vous pouvez envoyer des journaux à un serveur de stockage ou à un service d’analytique. Les journaux d’activité peuvent servir à identifier, résoudre et réparer les erreurs de configuration et les problèmes de performances.

## <a name="capturing-logs"></a>Capture de journaux

Pour accéder aux journaux PostgreSQL d’un coordinateur Hyperscale (Citus) ou nœud Worker, vous devez activer le paramètre de diagnostic PostgreSQLLogs. Dans le portail Azure, ouvrez **Paramètres de diagnostic** et sélectionnez **+ Ajouter un paramètre de diagnostic**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Ajouter des paramètres de diagnostic - Bouton":::

Choisissez un nom pour les nouveaux paramètres de diagnostic, puis cochez la case **PostgreSQLLogs** ainsi que la case **Envoyer à l’espace de travail Log Analytics**.  Ensuite, sélectionnez **Enregistrer**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="Choisir des journaux PostgreSQL":::

## <a name="viewing-logs"></a>Consultation des journaux

Pour afficher et filtrer les journaux, nous allons utiliser des requêtes Kusto. Dans le Portail Azure, ouvrez **Journaux** pour votre groupe de serveurs Hyperscale (Citus). Si une boîte de dialogue de sélection de requête s’affiche, fermez-la :

:::image type="content" source="media/howto-hyperscale-logging/logs-dialog.png" alt-text="Page Journaux avec boîte de dialogue ouverte":::

Vous verrez ensuite une zone d’entrée pour entrer des requêtes.

:::image type="content" source="media/howto-hyperscale-logging/logs-query.png" alt-text="Zone d’entrée pour interroger les journaux":::

Entrez la requête suivante et sélectionnez le bouton **Exécuter**.

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
```

La requête ci-dessus répertorie les messages de journaux de tous les nœuds, ainsi que leur gravité et leur timestamp. Vous pouvez ajouter des clauses `where` pour filtrer les résultats. Par exemple, pour afficher uniquement les erreurs du nœud coordinateur, filtrez le niveau d’erreur et le nom du serveur comme suit :

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
| where LogicalServerName_s == 'example-server-group-c'
| where errorLevel_s == 'ERROR'
```

Remplacez le nom du serveur de l’exemple ci-dessus par le nom de votre serveur. Le nom du nœud coordinateur a le suffixe `-c` et les nœuds Worker sont nommés avec le suffixe `-w0`, `-w1`, etc.

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer avec les requêtes Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
- En savoir plus sur les [hubs d’événements Azure](../event-hubs/event-hubs-about.md)
