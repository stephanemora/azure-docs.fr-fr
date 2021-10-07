---
title: Résoudre des problèmes liés à SQL Insights (préversion)
description: Découvrez comment résoudre les problèmes liés à SQL Insights dans Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: b76e08103b7af7591e7f71d3fe40e1e018a45665
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610652"
---
# <a name="troubleshoot-sql-insights-preview"></a>Résoudre des problèmes liés à SQL Insights (préversion)
Pour résoudre les problèmes liés à la collecte de données dans SQL Insights, vérifiez l’état de la machine d’analyse dans l’onglet **Gérer le profil**. Les états sont les suivants :

- **Collecte** 
- **Collecte non active** 
- **Collecte avec des erreurs** 
 
Sélectionnez l’état pour afficher les journaux et plus de détails susceptibles de vous aider à résoudre le problème. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Capture d’écran montrant l’état d’une machine d’analyse.":::

## <a name="status-not-collecting"></a>État : Collecte non active 
L’état de la machine d’analyse est **Collecte non active** s’il n’existe aucune donnée dans *InsightsMetrics* pour SQL au cours des dix dernières minutes. 

> [!NOTE]
> Vérifiez que vous utilisez une [version prise en charge de SQL](sql-insights-overview.md#supported-versions) pour tenter de collecter des données. Par exemple, si votre profil et votre chaîne de connexion sont valides, mais que votre version d’Azure SQL Database n’est pas prise en charge, vous obtenez un état **Collecte non active**.

SQL Insights utilise la requête suivante pour récupérer ces informations :

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Vérifiez si des journaux de Telegraf aident à identifier la cause racine du problème. S’il existe des entrées de journal, vous pouvez sélectionner **Collecte non active**, puis consulter les journaux et les informations de résolution des problèmes courants. 

S’il n’existe aucune entrée de journal, vous devez vérifier les journaux sur la machine virtuelle d’analyse pour les services suivants installés par deux extensions de machine virtuelle :

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - Service : mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - Service : wli 
  - Service : ms-telegraf 
  - Service : td-agent-bit-wli 
  - Journal d’extension pour vérifier les échecs d’installation : /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 

### <a name="wli-service-logs"></a>Journaux d’activité du service wli 

Journaux d’activité de service : `/var/log/wli.log`

Pour afficher les journaux d’activité récents : `tail -n 100 -f /var/log/wli.log`
 
Si vous voyez le journal des erreurs suivant, il y a un problème avec le service mdsd.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```

### <a name="telegraf-service-logs"></a>Journaux d’activité du service Telegraf 

Journaux d’activité de service : `/var/log/ms-telegraf/telegraf.log`

Pour afficher les journaux d’activité récents : `tail -n 100 -f /var/log/ms-telegraf/telegraf.log`

Pour afficher les journaux récents des erreurs et des avertissements : `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

La configuration que Telegraf utilise est générée par le service wli et placée sous le chemin : `/etc/ms-telegraf/telegraf.d/wli`
 
Si une mauvaise configuration est générée, le service ms-telegraf peut échouer à démarrer. Vérifiez si le service ms-telegraf est en cours d’exécution à l’aide de cette commande : `service ms-telegraf status`

Pour afficher les messages d’erreur du service Telegraf, exécutez-le manuellement à l’aide de la commande suivante : 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>Journaux d’activité du service mdsd 

Vérifiez les [prérequis](../agents/azure-monitor-agent-install.md#prerequisites) pour l’agent Azure Monitor. 


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

L’une des causes de l’état **Collecte non active** est une configuration non valide de la machine virtuelle d’analyse. Voici la configuration par défaut :

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

Cette configuration spécifie les jetons de remplacement à utiliser dans la configuration du profil sur votre machine virtuelle d’analyse. Elle vous permet également de référencer des secrets à partir d’Azure Key Vault. Vous n’avez donc pas besoin de conserver les valeurs secrètes dans une configuration (ce que nous vous recommandons vivement).

#### <a name="secrets"></a>Secrets
Les secrets sont des jetons dont les valeurs sont récupérées au moment de l’exécution d’un coffre de clés Azure. Un secret est défini par une paire composée d’une référence au coffre de clés et d’un nom de secret. Cette définition permet à Azure Monitor d’obtenir la valeur dynamique du secret et de l’utiliser dans des références de configuration en aval.

Vous pouvez définir autant de secrets que nécessaire dans la configuration, y compris des secrets stockés dans des coffres de clés distincts.

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

L’autorisation d’accéder au coffre de clés est accordée à une identité managée sur la machine virtuelle d’analyse. Azure Monitor s’attend à ce que le coffre de clés fournisse au moins des secrets pour que la machine virtuelle obtienne une autorisation. Vous pouvez l’activer à partir du portail Azure, de PowerShell, d’Azure CLI ou d’un modèle Azure Resource Manager.

#### <a name="parameters"></a>Paramètres
Les paramètres sont des jetons qui peuvent être référencés dans la configuration du profil via des modèles JSON. Les paramètres ont un nom et une valeur. Les valeurs peuvent être de n’importe quel type JSON, y compris des objets et des tableaux. Un paramètre est référencé dans la configuration du profil par son nom selon la convention suivante : `.Parameters.<name>`.

Les paramètres peuvent référencer des secrets dans Key Vault à l’aide de la même convention. Par exemple, `sqlAzureConnections` référence le secret `telegrafPassword` selon la convention `$telegrafPassword`.

Au moment de l’exécution, tous les paramètres et secrets sont résolus et fusionnés avec la configuration du profil pour construire la configuration réelle à utiliser sur la machine.

> [!NOTE]
> Les noms de paramètre `sqlAzureConnections`, `sqlVmConnections` et `sqlManagedInstanceConnections` sont tous obligatoires dans la configuration, même si vous ne fournissez pas de chaînes de connexion pour certains d’entre eux.


## <a name="status-collecting-with-errors"></a>État : Collecte avec des erreurs
L’état de la machine d’analyse est **Collecte avec des erreurs** s’il existe au moins un journal *InsightsMetrics*, mais qu’il existe également des erreurs dans la table *Operation*.

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
> Si vous ne voyez pas de données dans le type de données `WorkloadDiagnosticLogs`, vous devez peut-être mettre à jour votre profil de surveillance pour stocker ces données.  Dans l’interface utilisateur de SQL Insights, sélectionnez **Gérer le profil** > **Modifier le profil** > **Mettre à jour le profil de surveillance**.

Dans les cas courants, nous fournissons des informations de résolution des problèmes dans notre vue des journaux : 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Vue des journaux d’activité de résolution des problèmes.":::

## <a name="next-steps"></a>Étapes suivantes

- Obtenez plus d’informations sur l’[activation de SQL Insights](sql-insights-enable.md).
