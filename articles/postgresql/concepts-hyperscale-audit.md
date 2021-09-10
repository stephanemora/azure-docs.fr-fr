---
title: Journal d’audit – Azure Database pour PostgreSQL – Hyperscale (Citus)
description: Concepts relatifs au journal d’audit pgAudit dans Azure Database pour PostgreSQL – Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 330f102dffb392540879d8d583e02a68fbe436c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532496"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Journal d’audit dans Azure Database pour PostgreSQL – Hyperscale (Citus)

> [!IMPORTANT]
> L’extension pgAudit dans Hyperscale (Citus) est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>
> Vous pouvez consulter la liste complète des nouvelles fonctionnalités dans [Fonctionnalités d’évaluation pour Hyperscale (Citus)](hyperscale-preview-features.md).

Le journal d’audit des activités de base de données dans Azure Database pour PostgreSQL – Hyperscale (Citus) est disponible via l’extension d’audit PostgreSQL nommée [pgAudit](https://www.pgaudit.org/). pgAudit permet une journalisation d’audit détaillée des sessions ou des objets.

Si vous souhaitez des journaux de niveau ressource Azure pour des opérations telles que la mise à l’échelle du stockage et du calcul, consultez le [journal d’activité Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considérations sur l’utilisation
Par défaut, les instructions de journal pgAudit sont émises en même temps que vos instructions de journal habituelles à l’aide de la fonctionnalité de journalisation standard de Postgres. Dans Azure Database pour PostgreSQL – Hyperscale (Citus), vous pouvez également configurer tous les journaux à envoyer au magasin de journaux Azure Monitor pour analyse ultérieure dans Log Analytics. Si vous activez la journalisation des ressources Azure Monitor, vos journaux sont automatiquement envoyés (au format JSON) aux journaux de Stockage Azure, d’Event Hubs ou d’Azure Monitor, selon votre choix.

## <a name="enabling-pgaudit"></a>Activation de pgAudit

L’extension pgAudit est préinstallée et activée sur la plupart des nœuds de groupe de serveurs Hyperscale (Citus). Si elle n’est pas activée sur vos nœuds, ouvrez une [demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pgaudit-settings"></a>Paramètres pgAudit

pgAudit vous permet de configurer la journalisation d’audit des sessions et des objets. La [journalisation d’audit des sessions](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) émet des journaux détaillés où se trouvent les instructions exécutées. La [journalisation d’audit des objets](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) est limitée à certaines relations. Vous pouvez choisir de configurer un ou plusieurs types de journalisation. 

> [!NOTE]
> Les paramètres pgAudit sont spécifiés globalement et ne peuvent pas être spécifiés au niveau d’une base de données ou d’un rôle.
>
> En outre, les paramètres pgAudit sont spécifiés par nœud dans un groupe de serveurs. Pour apporter une modification sur tous les nœuds, vous devez l’appliquer à chaque nœud individuellement.

Vous devez configurer les paramètres pgAudit pour démarrer la journalisation. La [documentation pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) donne la définition de chaque paramètre. Testez d’abord les paramètres pour vérifier que vous obtenez le comportement attendu.

> [!NOTE]
> L’affectation de la valeur ON à `pgaudit.log_client` permet de rediriger les journaux vers un processus client (comme psql) au lieu de les écrire dans un fichier. Ce paramètre doit généralement rester désactivé. <br> <br>
> `pgaudit.log_level` est activé uniquement lorsque `pgaudit.log_client` est activé.

> [!NOTE]
> Dans Azure Database pour PostgreSQL – Hyperscale (Citus), `pgaudit.log` ne peut pas être défini à l’aide d’un raccourci de signe `-` (moins) comme décrit dans la documentation pgAudit. Toutes les classes d’instruction requises (READ, WRITE, etc.) doivent être spécifiées individuellement.

## <a name="audit-log-format"></a>Format du journal d’audit
Chaque entrée d’audit est indiquée par `AUDIT:` en début de ligne. Le format du reste de l’entrée est abordé en détail dans la [documentation pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Prise en main
Pour commencer, définissez `pgaudit.log` sur `WRITE`, puis ouvrez vos journaux de serveur pour consulter la sortie. 

## <a name="viewing-audit-logs"></a>Affichage des journaux d’audit
La façon dont vous accédez aux journaux dépend du point de terminaison que vous choisissez. Pour le stockage Azure, consultez l’article [Compte de stockage des journaux](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Pour Event Hubs, consultez l’article [Diffusion des journaux Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

Pour les journaux Azure Monitor, les journaux sont envoyés à l’espace de travail que vous avez sélectionné. Les journaux Postgres utilisent le mode de collecte **AzureDiagnostics**, pour qu’ils puissent être interrogés à partir de la table AzureDiagnostics. Les champs de la table sont décrits ci-dessous. En savoir plus sur l’interrogation et la génération d’alertes dans la vue d’ensemble [Interroger les journaux Azure Monitor](../azure-monitor/logs/log-query-overview.md).

Vous pouvez utiliser cette requête pour commencer. Vous pouvez configurer des alertes basées sur les requêtes.

Rechercher toutes les entrées pgAudit dans les journaux Postgres pour un serveur particulier au cours du dernier jour
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment configurer la journalisation dans Azure Database pour PostgreSQL – Hyperscale (Citus) et comment accéder aux journaux](howto-hyperscale-logging.md)
