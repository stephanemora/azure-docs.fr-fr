---
title: Journaux de serveur dans Azure Database pour PostgreSQL - Serveur unique
description: Cet article explique comment Azure Database pour PostgreSQL - Serveur unique génère les journaux des requêtes et des erreurs et comment la rétention de journal est configurée.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: b295ab442e70772a86d6699e1063c7a1c728f1a7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091115"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Journaux de serveur dans Azure Database pour PostgreSQL - Serveur unique
La base de données Azure pour PostgreSQL génère des journaux d’activité des requêtes et des erreurs. Les journaux d’activité des requêtes et des erreurs peuvent être utilisés pour identifier, résoudre et réparer les erreurs de configuration et les problèmes de performances. (L’accès aux journaux d’activité des transactions n’est pas pris en charge.) 

## <a name="configure-logging"></a>Configuration de la journalisation 
Vous pouvez configurer la journalisation sur votre serveur avec les paramètres de journalisation. Sur chaque nouveau serveur, les options **log_checkpoints** et **log_connections** sont activées par défaut. Il existe d’autres paramètres que vous pouvez définir en fonction de vos besoins de journalisation : 

![Azure Database pour PostgreSQL - Paramètres de journalisation](./media/concepts-server-logs/log-parameters.png)

Pour plus d’informations sur ces paramètres, consultez la documentation PostgreSQL [Signalement et journalisation des erreurs](https://www.postgresql.org/docs/current/static/runtime-config-logging.html). Pour savoir comment configurer les paramètres Azure Database pour PostgreSQL, consultez la [documentation du portail](howto-configure-server-parameters-using-portal.md) ou la [documentation de la CLI](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Accès aux journaux d’activité serveur via le portail ou l’interface CLI
Si vous avez activé les journaux d’activité, vous pouvez y accéder à partir d’Azure Database pour PostgreSQL avec le [portail Azure](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md) et les API REST Azure. Les fichiers journaux tournent toutes les heures ou par tranches de 100 Mo, sachant que la limite atteinte en premier prévaut. Vous pouvez définir la période de conservation de ce stockage de journal en utilisant le paramètre **log\_période\_conservation** associé à votre serveur. La valeur par défaut est de 3 jours et la valeur maximale est de 7 jours. Votre serveur doit disposer de suffisamment de stockage pour contenir les fichiers journaux conservés. (Ce paramètre de rétention ne régit pas les journaux de diagnostic Azure.)


## <a name="diagnostic-logs"></a>Journaux de diagnostic
Azure Database pour PostgreSQL est intégré aux journaux de diagnostic Azure Monitor. Une fois que vous avez activé les journaux sur votre serveur PostgreSQL, vous pouvez choisir qu’ils soient transmis vers des [journaux Azure Monitor](../azure-monitor/log-query/log-query-overview.md), des Event Hubs ou Stockage Azure. 

> [!IMPORTANT]
> Cette fonctionnalité de diagnostic des journaux de serveur n’est disponible que dans les [niveaux tarifaires](concepts-pricing-tiers.md) Usage général et Mémoire optimisée.

Pour activer les journaux de diagnostic via le portail Azure :

   1. Sur le portail, accédez à *Paramètres de diagnostic* dans le menu de navigation de votre serveur Postgres.
   2. Sélectionnez *Ajouter le paramètre de diagnostic*.
   3. Donnez un nom à ce paramètre. 
   4. Sélectionnez l’emplacement en aval par défaut (compte de stockage, hub d’événements, analytique des journaux). 
   5. Sélectionnez les types de données de votre choix.
   6. Enregistrez votre paramètre.

Le tableau suivant décrit ce que contient chaque journal. En fonction du point de terminaison de sortie choisi, les champs et l’ordre dans lequel ils apparaissent peuvent varier. 

|**Champ** | **Description** |
|---|---|
| TenantId | Votre ID d’abonné |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Horodatage du moment où le journal a été enregistré en UTC |
| Type | Type de journal. Toujours `AzureDiagnostics` |
| SubscriptionId | GUID de l’abonnement auquel appartient le serveur |
| ResourceGroup | Nom du groupe de ressources auquel le serveur appartient |
| ResourceProvider | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | URI de ressource |
| Ressource | Nom du serveur |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Niveau de journalisation, par exemple : LOG, ERROR, NOTICE |
| Message | Message de journal principal | 
| Domain | Version du serveur, par exemple : postgres-10 |
| Detail | Message du journal secondaire (le cas échéant) |
| ColumnName | Nom de la colonne (le cas échéant) |
| SchemaName | Nom du schéma (le cas échéant) |
| DatatypeName | Nom du type de données (le cas échéant) |
| LogicalServerName | Nom du serveur | 
| _ResourceId | URI de ressource |
| Préfixe | Préfixe de la ligne de journal |



## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur l’accès aux journaux d’activité à partir du [portail Azure](howto-configure-server-logs-in-portal.md) ou de l’[interface de ligne de commande Azure](howto-configure-server-logs-using-cli.md).
- En savoir plus sur la [tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
