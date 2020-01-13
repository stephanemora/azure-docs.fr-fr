---
title: Utiliser PowerShell pour créer et configurer un espace de travail Log Analytics | Microsoft Docs
description: Les espaces de travail Log Analytics dans Azure Monitor stockent les données des serveurs dans votre infrastructure locale ou dans le cloud. Vous pouvez collecter des données de la machine à partir du stockage Azure lorsqu’elles sont générées par les diagnostics Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 68cd0d51c16ecd63a1446c284f81c5dea07b8c06
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75363521"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Gérer un espace de travail Log Analytics dans Azure Monitor à l’aide de PowerShell

Vous pouvez utiliser les [cmdlets PowerShell Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) pour exécuter diverses fonctions au sein d’un espace de travail Log Analytics dans Azure Monitor à partir d’une ligne de commande ou dans le cadre d’un script.  Voici quelques exemples des tâches que vous pouvez effectuer avec PowerShell :

* Créer un espace de travail
* Ajouter ou supprimer une solution
* Importer et exporter des recherches enregistrées
* Créer un groupe d’ordinateurs
* Activer la collecte de journaux d’activité IIS à partir d’ordinateurs sur lesquels l’agent Windows est installé
* Collecter les compteurs de performances d’ordinateurs Linux et Windows
* Collecter les événements de Syslog sur des ordinateurs Linux
* Collecter les événements des journaux des événements Windows
* Collecter des journaux des événements personnalisés
* Ajouter l’agent Log Analytics à une machine virtuelle Azure
* Configurer Log Analytics pour indexer les données collectées à l’aide des diagnostics Azure

Cet article fournit deux exemples de code qui illustrent quelques-unes des fonctions que vous pouvez effectuer à partir de PowerShell.  Pour obtenir des informations sur d’autres fonctions, consultez [Informations de référence sur les applets de commande PowerShell Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) .

> [!NOTE]
> Avant, Log Analytics s’appelait Operational Insights, ce qui explique pourquoi ce nom est présent dans les applets de commande.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables requises
Ces exemples fonctionnent avec la version 1.0.0 ou ultérieure du module Az.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Créer et configurer un espace de travail Log Analytics
L’exemple de script suivant illustre comment :

1. Créer un espace de travail
2. Répertorier les solutions disponibles
3. Ajouter des solutions à l’espace de travail
4. Importer des recherches enregistrées
5. Exporter des recherches enregistrées
6. Créer un groupe d’ordinateurs
7. Activer la collecte de journaux d’activité IIS à partir d’ordinateurs sur lesquels l’agent Windows est installé
8. Collecter les compteurs de performances de disque logique d’ordinateurs Linux (% d’Inodes utilisés, Mo libres, % d’espace utilisé, Transferts disque/s, Lectures disque/s, Écritures disque/s)
9. Collecter les événements Syslog d’ordinateurs Linux
10. Collecter les événements d’erreur et d’avertissement du journal des événements d’application d’ordinateurs Windows
11. Collecter le compteur de performances Mo de mémoire disponible d’ordinateurs Windows
12. Collecter un journal personnalisé

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

> [!NOTE]
> Le format du paramètre **CustomLogRawJson** qui définit la configuration d’un journal personnalisé peut être complexe. Utilisez [AzOperationalInsightsDataSource](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.2.0) pour récupérer la configuration d’un journal personnalisé existant. La propriété **Properties** correspond à la configuration requise pour le paramètre **CustomLogRawJson**.

Dans l’exemple ci-dessus regexDelimiter a été défini comme « \\n » pour le saut de ligne. Le délimiteur de journal peut également être un horodateur.  Voici les formats pris en charge :

| Format | Le format RegEx JSON utilise deux \\ pour chaque \ dans une expression régulière standard, par conséquent si les tests dans une application RegEx réduit \\ à \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> deux espaces après MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> sachant que + est le signe + ou le signe - <br> sachant que zzzz est le décalage de temps | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> Le T est la lettre T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Configuration de Log Analytics pour envoyer les diagnostics Azure
Pour une analyse sans agent des ressources Azure, celles-ci doivent avoir les diagnostics Azure activés et configurés pour écrire dans un espace de travail Log Analytics. Cette approche a pour effet d’envoyer des données directement dans l’espace de travail et ne nécessite pas d’écriture de données dans un compte de stockage. Les ressources prises en charge sont les suivantes :

| Type de ressource | Journaux d’activité | Mesures |
| --- | --- | --- |
| Passerelles d’application    | Oui | Oui |
| Comptes Automation     | Oui | |
| Comptes Batch          | Oui | Oui |
| Data Lake analytics     | Oui | |
| Data Lake Store         | Oui | |
| Pool élastique SQL        |     | Oui |
| Espace de noms Event Hub     |     | Oui |
| IoT Hubs                |     | Oui |
| Key Vault               | Oui | |
| Équilibreurs de charge          | Oui | |
| Logic Apps              | Oui | Oui |
| Network Security Group | Oui | |
| Cache Azure pour Redis             |     | Oui |
| Services de recherche         | Oui | Oui |
| Espace de noms Service Bus   |     | Oui |
| SQL (v12)               |     | Oui |
| Sites web               |     | Oui |
| Batteries de serveurs web        |     | Oui |

Pour plus d’informations sur les métriques disponibles, voir [Mesures prises en charge avec Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Pour plus d’informations sur les journaux d’activité disponibles, voir [Schéma et services pris en charge pour les journaux de ressources](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Vous pouvez également utiliser l’applet de commande précédente pour collecter des journaux d’activité à partir de ressources qui se trouvent dans différents abonnements. La cmdlet peut fonctionner sur différents abonnements dans la mesure où vous fournissez les ID de la ressource qui crée les journaux d’activité et de l’espace de travail auquel les journaux d’activité sont envoyés.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Configuration de l’espace de travail Log Analytics pour collecter les diagnostics Azure à partir du stockage
Pour collecter des données de journal à partir d’une instance en cours d’exécution d’un service cloud classique ou d’un cluster Service Fabric, vous devez commencer par écrire les données dans le Stockage Azure. Vous pouvez ensuite configurer un espace de travail Log Analytics pour collecter les journaux d’activité du compte de stockage. Les ressources prises en charge sont les suivantes :

* Services cloud classiques (rôles de travail et web)
* Clusters Service Fabric

L’exemple suivant montre comment :

1. Répertorier les emplacements et les comptes de stockage existants à partir desquels l’espace de travail indexe les données
2. Créer une configuration pour lire à partir d’un compte de stockage
3. Mettre à jour la configuration créée pour indexer les données à partir d’emplacements supplémentaires
4. Supprimer la configuration créée

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Vous pouvez également utiliser le script précédent pour collecter les journaux d’activité de comptes de stockage dans différents abonnements. Le script peut fonctionner sur différents abonnements dans la mesure où vous fournissez l'ID de ressource du compte de stockage et une clé d'accès correspondante. Lorsque vous modifiez la clé d’accès, vous devez mettre à jour Storage Insight pour avoir la nouvelle clé.


## <a name="next-steps"></a>Étapes suivantes
* [Passez en revue les applets de commande PowerShell de Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) pour obtenir plus d’informations sur l’utilisation de PowerShell pour configurer Log Analytics.

