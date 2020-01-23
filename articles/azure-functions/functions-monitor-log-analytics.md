---
title: Monitorage d’Azure Functions avec Azure Monitor Logs
description: Découvrez comment utiliser Azure Monitor Logs avec Azure Functions pour surveiller les exécutions de fonctions.
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: f4af646569edc8a9274af752e7e4f2a36585ae4d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769096"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitorage d’Azure Functions avec Azure Monitor Logs

Azure Functions s’intègre à [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md) pour effectuer le monitorage des fonctions. Cet article explique comment configurer Azure Functions de façon à envoyer des journaux générés par le système et par l’utilisateur à Azure Monitor Logs.

Azure Monitor Logs offre la possibilité de consolider les journaux de différentes ressources dans le même espace de travail, où ils peuvent être analysés avec des [requêtes](../azure-monitor/log-query/log-query-overview.md) pour récupérer, consolider et analyser rapidement les données collectées.  Vous pouvez créer et tester des requêtes à l’aide de [Log Analytics](../azure-monitor/log-query/portals.md) dans le Portail Azure, avant d’analyser directement les données à l’aide de ces outils ou d’enregistrer les requêtes pour les utiliser pour les [visualisations](../azure-monitor/visualizations.md) ou les [règles d’alerte](../azure-monitor/platform/alerts-overview.md).

Azure Monitor utilise une version du [langage de requête Kusto](/azure/kusto/query/) utilisé par Azure Data Explorer qui est adapté aux requêtes simples dans les journaux, mais inclut également des fonctionnalités avancées telles que les agrégations, les jointures et les analyses intelligentes. Il existe [plusieurs leçons](../azure-monitor/log-query/get-started-queries.md) pour vous aider à apprendre le langage de requête.

> [!NOTE]
> L’intégration avec Azure Monitor Logs est actuellement en préversion publique pour les applications de fonction qui s’exécutent sur des plans d’hébergement Windows Consumption, Premium et Dedicated.

## <a name="setting-up"></a>Configuration

Dans la section **Supervision**, sélectionnez **Paramètres de diagnostic**, puis cliquez sur **Ajouter un paramètre de diagnostic**.

![Ajouter un paramètre de diagnostic](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Dans la page **Paramètres de diagnostic**, choisissez **Envoyer à Log Analytics**, puis sélectionnez votre espace de travail Log Analytics. Sous **journal**, choisissez **FunctionAppLogs** ; cette table contient les journaux souhaités.

![Ajouter un paramètre de diagnostic](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Journaux générés par l’utilisateur

Pour générer des journaux personnalisés, vous pouvez utiliser l’instruction de journalisation propre à votre langage. Voici des exemples d’extraits de code :


# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Interroger les journaux

Pour interroger les journaux générés, accédez à l’espace de travail Log Analytics que vous avez configuré pour envoyer les journaux de fonction, puis cliquez sur **Journaux**.

![Fenêtre Requête dans l’espace de travail LA](media/functions-monitor-log-analytics/querying.png)

Azure Functions écrit tous les journaux dans la table **FunctionAppLogs**. Voici quelques exemples de requêtes.

### <a name="all-logs"></a>Tous les journaux d’activité

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Journaux d’une fonction spécifique

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Exceptions

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Étapes suivantes

- Consulter la [Vue d’ensemble d’Azure Functions](functions-overview.md)
- En savoir plus sur [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md)
- Découvrez plus en détails le [langage de requête](../azure-monitor/log-query/get-started-queries.md).
