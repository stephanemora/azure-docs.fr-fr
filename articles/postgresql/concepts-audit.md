---
title: Journalisation d’audit dans Azure Database pour PostgreSQL - Serveur unique
description: Concepts relatifs à la journalisation d’audit pgAudit dans Azure Database pour PostgreSQL - Serveur unique
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 8a60c1db1ca3b3037aded6ed7d7a88f237edfe4e
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458697"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Journalisation d’audit dans Azure Database pour PostgreSQL - Serveur unique

La journalisation d’audit des activités de base de données dans Azure Database pour PostgreSQL – Serveur unique est disponible via l’extension d’audit PostgreSQL, [pgAudit](https://www.pgaudit.org/). L’extension pgAudit permet une journalisation d’audit détaillée des sessions et des objets.

> [!NOTE]
> L’extension pgAudit est disponible en préversion sur Azure Database pour PostgreSQL. Elle peut uniquement être activée sur les serveurs à usage général et à mémoire optimisée.

Si vous souhaitez des journaux de niveau ressource Azure pour des opérations telles que la mise à l’échelle du stockage et du calcul, consultez [Vue d’ensemble des journaux de plateforme Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considérations sur l’utilisation

Par défaut, les instructions de journal pgAudit sont émises en même temps que vos instructions de journal habituelles à l’aide de la fonction de journalisation standard de Postgres. Dans Azure Database pour PostgreSQL, ces fichiers .log peuvent être téléchargés à partir du portail Azure ou d’Azure CLI. Le stockage maximal pour la collection de fichiers est de 1 Go. Chaque fichier est disponible pour un maximum de sept jours. La valeur par défaut est de trois jours. Ce service est une option de stockage à court terme.

Vous pouvez également configurer tous les journaux à envoyer au magasin Journaux d’activité Azure Monitor pour analyse ultérieure dans Log Analytics. Si vous activez l’option Superviser la journalisation des ressources, vos journaux sont automatiquement envoyés au format JSON à Stockage Azure, Azure Event Hubs ou Journaux d’activité Monitor, selon votre choix.

L’activation de pgAudit génère un grand volume de journalisation sur un serveur, ce qui a un impact sur les performances et le stockage des journaux. Nous vous recommandons d’utiliser Journaux d’activité Monitor, qui offre des options de stockage à plus long terme, ainsi que des fonctionnalités d’analyse et de génération d’alertes. Désactivez la journalisation standard afin de réduire l’impact d’une journalisation supplémentaire sur les performances :

   1. Définissez le paramètre `logging_collector` sur **OFF**.
   1. Redémarrez le serveur pour appliquer les modifications.

Pour savoir comment configurer la journalisation vers Stockage, Event Hubs ou Monitor Logs, consultez la section relative aux journaux d’activité de ressources dans [Journaux dans Azure Database pour PostgreSQL – Serveur unique](concepts-server-logs.md).

## <a name="install-pgaudit"></a>Installer pgAudit

Pour installer pgAudit, vous devez l’inclure dans les bibliothèques partagées préchargées du serveur. Une modification apportée au paramètre `shared_preload_libraries` de Postgres nécessite un redémarrage du serveur pour prendre effet. Vous pouvez modifier les paramètres à l’aide du [portail](howto-configure-server-parameters-using-portal.md), de [CLI](howto-configure-server-parameters-using-cli.md) ou de l’[API REST](/rest/api/postgresql/singleserver/configurations/createorupdate).

Pour utiliser le [portail](https://portal.azure.com) :

   1. Sélectionnez votre serveur Azure Database pour PostgreSQL.
   1. À gauche, sous **Paramètres**, sélectionnez **Paramètres du serveur**.
   1. Recherchez **shared_preload_libraries**.
   1. Sélectionnez **PGAUDIT**.
   
      :::image type="content" source="./media/concepts-audit/share-preload-parameter.png" alt-text="Capture d’écran montrant Azure Database pour PostgreSQL activant shared_preload_libraries pour PGAUDIT.":::

   1. Redémarrez le serveur pour appliquer les modifications.
   1. Vérifiez que `pgaudit` est chargé dans `shared_preload_libraries` en exécutant la requête suivante dans psql :
   
        ```SQL
      show shared_preload_libraries;
      ```
      Vous devriez voir `pgaudit` dans le résultat de la requête qui renverra `shared_preload_libraries`.

   1. Connectez-vous à votre serveur à l’aide d’un client (par exemple, psql), puis activez l’extension pgAudit :
   
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Si une erreur s’affiche, vérifiez que vous avez redémarré le serveur après avoir enregistré `shared_preload_libraries`.

## <a name="pgaudit-settings"></a>Paramètres pgAudit

À l’aide de pgAudit, vous pouvez configurer la journalisation d’audit de sessions ou d’objets. La [journalisation d’audit des sessions](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) émet des journaux détaillés où se trouvent les instructions exécutées. La [journalisation d’audit des objets](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) est limitée à certaines relations. Vous pouvez choisir de configurer un ou plusieurs types de journalisation.

> [!NOTE]
> Les paramètres pgAudit sont spécifiés globalement et ne peuvent pas être spécifiés au niveau d’une base de données ou d’un rôle.

Après avoir [installé pgAudit](#install-pgaudit), vous pouvez configurer ses paramètres pour démarrer la journalisation.

Pour configurer pgAudit, dans le [portail](https://portal.azure.com) :

   1. Sélectionnez votre serveur Azure Database pour PostgreSQL.
   1. À gauche, sous **Paramètres**, sélectionnez **Paramètres du serveur**.
   1. Recherchez les paramètres **pg_audit**.
   1. Sélectionnez les paramètres appropriés pour les modifier. Par exemple, pour démarrer la journalisation, définissez **pgaudit.log** sur **WRITE**.
   
       :::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="Capture d’écran montrant Azure Database pour PostgreSQL configurant la journalisation avec pgAudit.":::
   1. Cliquez sur **Enregistrer** pour enregistrer vos modifications.

La [documentation pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) donne la définition de chaque paramètre. Testez d’abord les paramètres et confirmez que vous obtenez le comportement attendu. Par exemple :

- Lorsque le paramètre **pgaudit.log_client** est activé, il redirige les journaux vers un processus client comme psql au lieu de les écrire dans un fichier. De manière générale, laissez ce paramètre désactivé.
- Le paramètre **pgaudit.log_level** est activé uniquement lorsque **pgaudit.log_client** est activé.

> [!NOTE]
> Dans Azure Database pour PostgreSQL, **pgaudit.log** ne peut pas être défini en utilisant le raccourci du signe moins (`-`) comme décrit dans la documentation de pgAudit. Toutes les classes d’instructions requises, telles que READ et WRITE, doivent être spécifiées individuellement.

### <a name="audit-log-format"></a>Format du journal d’audit

Chaque entrée d’audit est indiquée par `AUDIT:` en début de ligne. Le format du reste de l’entrée est abordé en détail dans la [documentation pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Si vous avez besoin d’autres champs pour votre audit, utilisez le paramètre `log_line_prefix` Postgres. La chaîne `log_line_prefix` est une sortie qui apparaît au début de chaque ligne de journal Postgres. Par exemple, le paramètre `log_line_prefix` suivant fournit l’horodatage, le nom d’utilisateur, le nom de la base de données et l’ID de processus :

```
t=%m u=%u db=%d pid=[%p]:
```

Pour en savoir plus sur `log_line_prefix`, consultez la [documentation de PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="get-started"></a>Bien démarrer

Pour une prise en main rapide, définissez **pgaudit.log** sur **WRITE**. Ouvrez ensuite vos journaux pour examiner la sortie.

## <a name="view-audit-logs"></a>Afficher les journaux d’audit

Si vous utilisez des fichiers .log, vos journaux d’audit sont inclus dans le même fichier que vos journaux d’erreurs PostgreSQL. Vous pouvez télécharger des fichiers journaux à partir du [portail](howto-configure-server-logs-in-portal.md) ou de [CLI](howto-configure-server-logs-using-cli.md).

Si vous utilisez la journalisation des ressources Azure, la façon dont vous accédez aux journaux dépend du point de terminaison que vous choisissez. Pour Stockage, consultez [Journaux de ressources Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Pour Event Hubs, consultez également [Journaux de ressources Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

Pour Journaux d’activité Monitor, les journaux sont envoyés à l’espace de travail que vous avez sélectionné. Les journaux Postgres utilisent le mode de collecte `AzureDiagnostics` pour qu’ils puissent être interrogés à partir de la table `AzureDiagnostics`, comme illustré. Pour en savoir plus sur les requêtes et les alertes, consultez [Requêtes de journal dans Azure Monitor](../azure-monitor/logs/log-query-overview.md).

Utilisez cette requête pour commencer. Vous pouvez configurer des alertes basées sur les requêtes.

Recherchez tous les journaux Postgres pour un serveur particulier au cours du dernier jour :

```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur la journalisation dans Azure Database pour PostgreSQL](concepts-server-logs.md).
- En savoir plus sur la définition de paramètres à l’aide du [portail Azure](howto-configure-server-parameters-using-portal.md), d’[Azure CLI](howto-configure-server-parameters-using-cli.md) ou de l’[API REST](/rest/api/postgresql/singleserver/configurations/createorupdate).
