---
title: Résolution des problèmes liés à SQL Insights (préversion)
description: Résolution des problèmes liés à SQL Insights dans Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 35aa53def1a72f98309e7616ce64194dd77c5a4d
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331283"
---
# <a name="troubleshooting-sql-insights-preview"></a>Résolution des problèmes liés à SQL Insights (préversion)
Pour résoudre des problèmes liés à la collecte de données dans SQL insights, vérifiez l’état de la machine d’analyse dans l’onglet **Gérer le profil**. Elle aura l’un des états suivants :

- Collecte 
- Collecte non active 
- Collecte avec des erreurs 
 
Cliquez sur l'**État** pour accéder aux journaux et obtenir plus d’informations, ce qui peut vous aider à résoudre le problème. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="État de la machine d’analyse":::

## <a name="not-collecting-state"></a>État Collecte non active 
L’état de la machine d’analyse est *Collecte non active* s’il n’existe aucune donnée dans *InsightsMetrics* pour SQL au cours des 10 dernières minutes. 

> [!NOTE]
> Vérifiez que vous utilisez une [version prise en charge de SQL](sql-insights-overview.md#supported-versions) pour tenter de recueillir des données. Par exemple, si votre profil et votre chaîne de connexion sont valides, mais que votre version d’Azure SQL Database n’est pas prise en charge, vous obtenez un état Données non collectées.

SQL Insights utilise la requête suivante pour récupérer ces informations :

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Vérifiez s’il existe des journaux de Telegraf qui permettent d’identifier la cause racine du problème. S’il existe des entrées de journal, vous pouvez cliquer sur *Collecte non active*, puis consulter les journaux et les informations de résolution des problèmes courants. 


S’il n’existe aucun journal, vous devez vérifier les journaux sur la machine virtuelle d’analyse pour les services suivants installés par deux extensions de machine virtuelle :

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - Service : mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - Service : wli 
  - Service : ms-telegraf 
  - Service : td-agent-bit-wli 
  - Journal d’extension pour vérifier les échecs d’installation : /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>Journaux d’activité du service wli 

Journaux d’activité de service : `/var/log/wli.log`

Pour afficher les journaux d’activité récents : `tail -n 100 -f /var/log/wli.log`
 

Si vous voyez le journal des erreurs suivant, cela indique un problème avec le service **mdsd**.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Journaux d’activité du service Telegraf 

Journaux d’activité de service : `/var/log/ms-telegraf/telegraf.log`

Pour afficher les journaux récents : `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` Pour afficher les journaux des erreurs et des avertissements récents : `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 La configuration utilisée par Telegraf est générée par le service wli et placée dans : `/etc/ms-telegraf/telegraf.d/wli`
 
Si une configuration incorrecte est générée, le service ms-telegraf peut ne pas démarrer. Vérifiez si le service ms-telegraf est en cours d’exécution avec la commande : `service ms-telegraf status`

Pour afficher les messages d’erreur du service telegraf, exécutez-le manuellement à l’aide de la commande suivante : 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>Journaux d’activité du service mdsd 

Vérifiez les [limitations actuelles](../agents/azure-monitor-agent-overview.md#current-limitations) de l’agent Azure Monitor. 


Journaux d’activité de service :  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Pour afficher les erreurs récentes : `tail -n 100 -f /var/log/mdsd.err`

 Si vous devez contacter le support, collectez les informations suivantes : 

- Journaux d’activité dans `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Journal dans `/var/log/waagent.log` 
- Journaux d’activité dans `/var/log/mdsd*`
- Fichiers dans `/etc/mdsd.d/`
- File `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Configuration de machine virtuelle d’analyse non valide

Une des causes de l’état *Collecte non active* est due au fait que la configuration de la machine virtuelle d’analyse n’est pas valide.  La configuration par défaut est la suivante :

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Cette configuration spécifie les jetons de remplacement à utiliser dans la configuration du profil sur votre machine virtuelle d’analyse. Elle vous permet également de référencer des secrets à partir d’Azure Key Vault. Vous ne devez donc pas conserver les valeurs secrètes dans une configuration, ce qui est fortement recommandé.

#### <a name="secrets"></a>Secrets
Les secrets sont des jetons dont les valeurs sont extraites au moment de l’exécution d’un Azure Key Vault. Un secret est défini par une paire référence/nom de secret d’un Key Vault. Cela permet à Azure Monitor d’obtenir la valeur dynamique du secret et de l’utiliser dans des références de configuration en aval.

Vous pouvez définir autant de secrets que nécessaire dans la configuration, y compris des secrets stockés dans des Key Vaults distincts.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

Les autorisations d’accès au Key Vault sont fournies à une identité de service géré sur la machine virtuelle d’analyse. Azure Monitor s’attend à ce que le Key Vault fournisse au moins l’autorisation d’obtention de secrets sur la machine virtuelle. Vous pouvez l’activer dans le portail Azure, PowerShell, l’interface CLI ou le modèle Resource Manager.

#### <a name="parameters"></a>Paramètres
Les paramètres sont des jetons qui peuvent être référencés dans la configuration du profil via la création de modèles JSON. Les paramètres ont un nom et une valeur. Les valeurs peuvent être de n’importe quel type JSON, y compris des objets et des tableaux. Un paramètre est référencé dans la configuration de profil à l’aide de son nom dans cette convention `.Parameters.<name>`.

Les paramètres peuvent référencer des secrets dans Key Vault à l’aide de la même convention. Par exemple, `sqlAzureConnections` référence le secret `telegrafPassword` à l’aide de la convention `$telegrafPassword`.

Au moment de l’exécution, tous les paramètres et secrets sont résolus et fusionnés avec la configuration du profil pour construire la configuration réelle à utiliser sur la machine.

> [!NOTE]
> Les noms de paramètres de `sqlAzureConnections`, `sqlVmConnections` et `sqlManagedInstanceConnections` sont tous obligatoires dans la configuration, même si vous n’avez pas de chaînes de connexion à fournir pour certains d’entre eux.


## <a name="collecting-with-errors-state"></a>État Collecte avec des erreurs
L’état de la machine d’analyse est *Collecte avec des erreurs* s’il existe au moins un journal *InsightsMetrics*, mais qu’il existe également des erreurs dans la table *Opération*.

SQL Insights utilise les requêtes suivantes pour récupérer ces informations :

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```kusto
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> En l'absence de données dans le type « WorkloadDiagnosticLogs », vous pouvez être amené à mettre à jour votre profil de surveillance pour stocker ces données.  Dans l'interface utilisateur de SQL Insights, sélectionnez « Gérer le profil », « Modifier le profil » et « Mettre à jour le profil de surveillance ».


Dans les cas courants, nous fournissons des informations de résolution des problèmes dans notre vue des journaux : 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Vue des journaux d’activité de résolution des problèmes.":::



## <a name="next-steps"></a>Étapes suivantes

- Obtenez plus d’informations sur l’[activation de SQL Insights](sql-insights-enable.md).
