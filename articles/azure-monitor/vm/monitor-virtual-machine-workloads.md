---
title: 'Superviser des machines virtuelles avec Azure Monitor : Charges de travail'
description: Découvrez comment surveiller les charges de travail d’invité des machines virtuelles dans Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: e411f7fd6f8d8a21b2016cbe16d0c7a947313331
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674861"
---
# <a name="monitor-virtual-machines-with-azure-monitor-workloads"></a>Superviser des machines virtuelles avec Azure Monitor : Charges de travail
Cet article fait partie du scénario [Surveiller les machines virtuelles et leurs charges de travail dans Azure Monitor](monitor-virtual-machine.md). Il décrit comment surveiller les charges de travail qui s’exécutent sur les systèmes d’exploitation invités de vos machines virtuelles. Cet article contient des informations sur l’analyse et la génération d’alertes sur différentes sources de données sur vos machines virtuelles.

## <a name="configure-additional-data-collection"></a>Configurer la collecte de données supplémentaires
VM Insights collecte uniquement les données de performance du système d’exploitation invité des machines activées. Vous pouvez activer la collecte de données de performances et d’événements supplémentaires, et d’autres données de supervision à partir de l’agent en configurant l’espace de travail Log Analytics. Il n’est configuré qu’une seule fois, car tout agent qui se connecte à l’espace de travail télécharge automatiquement la configuration et commence immédiatement à collecter les données définies.

Pour obtenir la liste des sources de données disponibles et savoir comment les configurer, consultez [Sources de données d’agent dans Azure Monitor](../agents/agent-data-sources.md).

> [!NOTE]
> Vous ne pouvez pas configurer la collecte de données de manière sélective pour différentes machines. Toutes les machines connectées à l’espace de travail utilisent la configuration de cet espace de travail.

> [!IMPORTANT]
> Veillez à collecter uniquement les données dont vous avez besoin. Des frais sont associés à toutes les données collectées dans votre espace de travail. Les données que vous collectez ne doivent servir qu’à des scénarios d’analyse et d’alerte particuliers.

## <a name="convert-management-pack-logic"></a>Convertir la logique du pack d’administration
Un nombre important de clients qui implémentent Azure Monitor surveillent actuellement leurs charges de travail de machines virtuelles à l’aide de packs d’administration dans Operations Manager. Il n’existe aucun outil de migration permettant de convertir des ressources Operations Manager en ressources Azure Monitor, car les plateformes sont fondamentalement différentes. Votre migration constitue plutôt une implémentation standard d’Azure Monitor pendant que vous continuerez à utiliser Operations Manager. Au fur et à mesure que vous personnalisez Azure Monitor pour répondre à vos besoins en matière d’applications et de composants et que la plateforme gagne en fonctionnalités, vous pouvez commencer à mettre hors service différents agents et packs d’administration dans Operations Manager.

Au lieu de tenter de répliquer l’ensemble des fonctionnalités d’un pack d’administration, analysez les données de supervision fournies par le pack d’administration. Déterminez si vous pouvez répliquer ces exigences en matière de surveillance à l’aide des méthodes décrites dans les sections précédentes. Dans de nombreux cas, vous pouvez configurer la collecte de données et les règles d’alerte dans Azure Monitor de manière à répliquer suffisamment de fonctionnalités pour pouvoir mettre hors service un pack d’administration particulier. Les packs d’administration peuvent souvent inclure des centaines, voire des milliers, de règles et de moniteurs.

Dans la plupart des scénarios, Operations Manager combine des conditions de collecte de données et d’alerte dans la même règle ou le même moniteur. Dans Azure Monitor, vous devez configurer la collecte de données et une règle d’alerte pour tous les scénarios d’alerte.

Une stratégie consiste à se concentrer sur les moniteurs et les règles qui ont déclenché des alertes dans votre environnement. Reportez-vous aux [rapports existants disponibles dans Operations Manager](/system-center/scom/manage-reports-installed-during-setup), comme **Alertes** et **Alertes les plus courantes**, qui peuvent vous aider à identifier les alertes au fil du temps. Vous pouvez également exécuter la requête suivante sur la base de données Operations pour évaluer les alertes récentes les plus courantes.

```sql
select AlertName, COUNT(AlertName) as 'Total Alerts' from
Alert.vAlertResolutionState ars
inner join Alert.vAlertDetail adt on ars.AlertGuid = adt.AlertGuid
inner join Alert.vAlert alt on ars.AlertGuid = alt.AlertGuid
group by AlertName
order by 'Total Alerts' DESC
```

Évaluez la sortie pour identifier les alertes spécifiques à la migration. Ignorez toutes les alertes qui ont été mises de côté ou qui sont connues pour être problématiques. Examinez vos packs d’administration pour identifier toutes les alertes critiques intéressantes qui n’ont jamais été déclenchées.

## <a name="windows-or-syslog-event"></a>Événement Windows ou Syslog
Dans ce scénario de surveillance courant, le système d’exploitation et les applications écrivent dans les événements Windows ou Syslog. Créez une alerte dès qu’un seul événement est trouvé. Vous pouvez également attendre une série d’événements correspondants dans une fenêtre de temps particulière.

Pour collecter ces événements, configurez un espace de travail Log Analytics pour collecter les [événements Windows](../agents/data-sources-windows-events.md) ou les [événements Syslog](../agents/data-sources-windows-events.md). L’ingestion et la conservation de ces données dans l’espace de travail ont un coût.

Les événements Windows sont stockés dans la table [Event](/azure/azure-monitor/reference/tables/event) et les événements [Syslog](/azure/azure-monitor/reference/tables/syslog) sont stockés dans la table Syslog de l’espace de travail Log Analytics.

### <a name="sample-log-queries"></a>Exemples de requêtes de journal

- **Comptez le nombre d’événements par journal d’événements de l’ordinateur et par type d’événement.**

    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

- **Comptez le nombre d’événements par journal d’événements de l’ordinateur et par ID d’événement.**
    
    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

### <a name="sample-alert-rules"></a>Exemples de règles d’alerte
L’exemple suivant crée une alerte lorsqu’un événement Windows spécifique est créé. Il utilise une règle d’alerte de mesure de métrique pour créer une alerte distincte pour chaque ordinateur.

- **Créez une règle d’alerte sur un événement Windows spécifique.**

   Cet exemple montre un événement dans le journal des applications. Spécifiez un seuil de 0 et un nombre de violations consécutives supérieur à 0.

    ```kusto
    Event 
    | where EventLog == "Application"
    | where EventID == 123 
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Créez une règle d’alerte sur les événements Syslog présentant une gravité particulière.**

   L’exemple suivant montre des événements d’autorisation ayant rencontré une erreur. Spécifiez un seuil de 0 et un nombre de violations consécutives supérieur à 0.

    ```kusto
    Syslog
    | where Facility == "auth"
    | where SeverityLevel == "err"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="custom-performance-counters"></a>Compteurs de performances personnalisés
Vous pouvez avoir besoin de compteurs de performances créés par des applications ou le système d’exploitation invité qui ne sont pas collectés par VM Insights. Configurez l’espace de travail Log Analytics pour collecter ces [données de performances](../agents/data-sources-windows-events.md). L’ingestion et la conservation de ces données dans l’espace de travail ont un coût. Veillez à ne pas collecter des données de performances qui sont déjà collectées par VM Insights.

Les données de performances configurées par l’espace de travail sont stockées dans la table [Perf](/azure/azure-monitor/reference/tables/perf). Cette table a une structure différente de celle de la table [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) utilisée par VM Insights.

### <a name="sample-log-queries"></a>Exemples de requêtes de journal

Pour des exemples de requêtes de journal qui utilisent des compteurs de performances personnalisés, consultez [Requêtes de journal avec des enregistrements de performances](../agents/data-sources-performance-counters.md#log-queries-with-performance-records).

### <a name="sample-alerts"></a>Exemples d’alertes

- **Créez une alerte sur la valeur maximale d’un compteur.**
    
    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = max(CounterValue) by Computer
    ```

- **Créez une alerte sur la valeur moyenne d’un compteur.**

    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = avg(CounterValue) by Computer
    ```

## <a name="text-logs"></a>Journaux texte
Certaines applications écrivent des événements écrits dans un journal texte stocké sur la machine virtuelle. Définissez un [journal personnalisé](../agents/data-sources-custom-logs.md) dans l’espace de travail Log Analytics pour collecter ces événements. Vous définissez l’emplacement du journal texte et sa configuration détaillée. L’ingestion et la conservation de ces données dans l’espace de travail ont un coût.

Les événements du journal texte sont stockés dans une table dont le nom est similaire à **MyTable_CL**. Vous définissez le nom et la structure du journal lorsque vous le configurez.

### <a name="sample-log-queries"></a>Exemples de requêtes de journal
Les noms de colonnes utilisés ici sont donnés à titre d’exemple uniquement. Vous définissez les noms de colonnes pour votre journal particulier lorsque vous le définissez. Les noms de colonnes de votre journal seront probablement différents.

- **Comptez le nombre d’événements par code.**
    
    ```kusto
    MyApp_CL
    | summarize count() by code
    ```

### <a name="sample-alert-rule"></a>Exemple de règle d’alerte

- **Créez une règle d’alerte pour tous les événements d’erreur.**
    
    ```kusto
    MyApp_CL
    | where status == "Error"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```
## <a name="iis-logs"></a>Journaux d’activité IIS
IIS s’exécutant sur les machines Windows écrit les journaux dans un fichier texte. Configurez un espace de travail Log Analytics pour collecter les [journaux IIS](../agents/data-sources-iis-logs.md). L’ingestion et la conservation de ces données dans l’espace de travail ont un coût.

Les enregistrements du journal IIS sont stockés dans la table [W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) de l’espace de travail Log Analytics.

### <a name="sample-log-queries"></a>Exemples de requêtes de journal

- **Comptez les entrées du journal IIS par URL pour l’hôte www.contoso.com.**
    
    ```kusto
    W3CIISLog 
    | where csHost=="www.contoso.com" 
    | summarize count() by csUriStem
    ```

- **Examinez le nombre total d’octets reçus par chaque machine IIS.**

    ```kusto
    W3CIISLog 
    | summarize sum(csBytes) by Computer
    ```

### <a name="sample-alert-rule"></a>Exemple de règle d’alerte

- **Créez une règle d’alerte sur tout enregistrement dont l’état de retour est 500.**
    
    ```kusto
    W3CIISLog 
    | where scStatus==500
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="service-or-daemon"></a>Service ou démon
Pour surveiller l’état d’un service Windows ou d’un démon Linux, activez la solution [Suivi des modifications et inventaire](../../automation/change-tracking/overview.md) dans [Azure Automation](../../automation/automation-intro.md). Azure Monitor n’a pas la capacité de surveiller l’état d’un service ou d’un démon. Quelques méthodes peuvent être utilisées, comme la recherche d’événements dans le journal des événements de Windows, mais cette méthode n’est pas fiable. Vous pouvez également rechercher le processus associé au service en cours d’exécution sur la machine à partir de la table [VMProcess](/azure/azure-monitor/reference/tables/vmprocess). Cette table ne se met à jour que toutes les heures, ce qui n’est généralement pas suffisant pour les alertes.

> [!NOTE]
> La solution Suivi des modifications et analyse est différente de la fonctionnalité [Analyse des changements](vminsights-change-analysis.md) de VM Insights. Cette fonctionnalité est en préversion publique et n’est pas encore incluse dans ce scénario.

Pour connaître les différentes options permettant d’activer la solution Change Tracking sur vos machines virtuelles, consultez [Activer Suivi des modifications et inventaire](../../automation/change-tracking/overview.md#enable-change-tracking-and-inventory). Cette solution comprend des méthodes permettant de configurer des machines virtuelles à grande échelle. Vous devrez [créer un compte Azure Automation](../../automation/automation-quickstart-create-account.md) pour prendre en charge cette solution.

Lorsque vous activez Suivi des modifications et inventaire, deux nouvelles tables sont créées dans votre espace de travail Log Analytics. Utilisez ces tables pour les règles d’alerte de requête de journal.

| Table de charge de travail | Description |
|:---|:---|
| [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationdata) | Modifications apportées aux données de configuration dans l’invité |
| [ConfigurationData](/azure/azure-monitor/reference/tables/configurationdata) | Dernier état signalé pour les données de configuration dans l’invité |


### <a name="sample-log-queries"></a>Exemples de requêtes de journal

- **Répertoriez tous les services et démons récemment démarrés.**
    
    ```kusto
    ConfigurationChange
    | where ConfigChangeType == "Daemons" or ConfigChangeType == "WindowsServices"
    | where SvcState == "Running"
    | sort by Computer, SvcName
    ```

### <a name="alert-rule-samples"></a>Exemples de règles d’alerte

- **Créez une règle d’alerte basée sur le moment où un service spécifique s’arrête.**

    
    ```kusto
    ConfigurationData
    | where SvcName == "W3SVC" 
    | where SvcState == "Stopped"
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

- **Créez une règle d’alerte basée sur le moment où un ensemble de services s’arrête.**
    
    ```kusto
    let services = dynamic(["omskd","cshost","schedule","wuauserv","heathservice","efs","wsusservice","SrmSvc","CertSvc","wmsvc","vpxd","winmgmt","netman","smsexec","w3svc","sms_site_vss_writer","ccmexe","spooler","eventsystem","netlogon","kdc","ntds","lsmserv","gpsvc","dns","dfsr","dfs","dhcp","DNSCache","dmserver","messenger","w32time","plugplay","rpcss","lanmanserver","lmhosts","eventlog","lanmanworkstation","wnirm","mpssvc","dhcpserver","VSS","ClusSvc","MSExchangeTransport","MSExchangeIS"]);
    ConfigurationData
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | where SvcName in (services)
    | where SvcState == "Stopped"
    | project TimeGenerated, Computer, SvcName, SvcDisplayName, SvcState
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

## <a name="port-monitoring"></a>Surveillance des ports
La surveillance des ports permet de vérifier qu’une machine écoute sur un port spécifique. Deux stratégies potentielles de surveillance des ports sont décrites ici.

### <a name="dependency-agent-tables"></a>Tables d’agents de dépendances
Utilisez [VMConnection](/azure/azure-monitor/reference/tables/vmconnection) et [VMBoundPort](/azure/azure-monitor/reference/tables/vmboundport) pour analyser les connexions et les ports sur la machine. La table VMBoundPort est mise à jour toutes les minutes avec chaque processus en cours d’exécution sur l’ordinateur et le port qu’il écoute. Vous pouvez créer une alerte de requête de journal similaire à l’alerte de pulsation manquante pour rechercher les processus qui se sont arrêtés ou pour alerter lorsque la machine n’écoute pas sur un port spécifique.

### <a name="sample-log-queries"></a>Exemples de requêtes de journal

- **Examinez le nombre de ports ouverts sur vos machines virtuelles, ce qui est utile pour évaluer quelles machines virtuelles présentent des vulnérabilités de configuration et de sécurité.**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize by Computer, Machine, Port, Protocol
    | summarize OpenPorts=count() by Computer, Machine
    | order by OpenPorts desc
    ```

- **Répertoriez les ports liés sur vos machines virtuelles, ce qui est utile pour évaluer quelles machines virtuelles présentent des vulnérabilités de configuration et de sécurité.**

    ```kusto
    VMBoundPort
    | distinct Computer, Port, ProcessName
    ```


- **Analysez l’activité réseau par port pour déterminer la façon dont votre application ou service est configuré.**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
    | project-away TimeGenerated
    | order by Machine, Computer, Port, Ip, ProcessName
    ```

- **Examinez les tendances des octets envoyés et reçus pour vos machines virtuelles.**

    ```kusto
    VMConnection
    | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer
    | order by Computer desc
    | render timechart
    ```

- **Utilisez les échecs de connexion dans le temps pour déterminer si le taux d’échec est stable ou évolue.**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | extend bythehour = datetime_part("hour", TimeGenerated)
    | project bythehour, LinksFailed
    | summarize failCount = count() by bythehour
    | sort by bythehour asc
    | render timechart
    ```

- **Liez les tendances d’état pour analyser le comportement et l’état de connexion d’une machine.**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h)
    | render timechart
    ```

### <a name="connection-manager"></a>Gestionnaire de connexions
La fonctionnalité [Moniteur de connexion](../../network-watcher/connection-monitor-overview.md) de [Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) est utilisée pour tester les connexions à un port sur une machine virtuelle. Un test permet de vérifier que la machine écoute sur le port et qu’elle est accessible sur le réseau.
Gestionnaire des connexions nécessite l’extension de Network Watcher sur la machine cliente qui lance le test. Il n’est pas nécessaire qu’elle soit installée sur la machine testée. Pour plus d’informations, consultez [Tutoriel : Superviser la communication réseau à l’aide du portail Azure](../../network-watcher/connection-monitor.md).

Gestionnaire des connexions présente un coût supplémentaire. Pour plus d’informations, consultez [Tarification Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/).

## <a name="run-a-process-on-a-local-machine"></a>Exécuter un processus sur un ordinateur local
La surveillance de certaines charges de travail nécessite un processus local. Il s’agit par exemple d’un script PowerShell qui s’exécute sur l’ordinateur local pour se connecter à une application et collecter ou traiter des données. Vous pouvez utiliser [Runbook Worker hybride](../../automation/automation-hybrid-runbook-worker.md), qui fait partie d’[Azure Automation](../../automation/automation-intro.md), pour exécuter un script PowerShell local. Runbook Worker hybride n’est pas directement facturé, mais il y a un coût pour chaque runbook qu’il utilise.

Le runbook peut accéder à toutes les ressources sur l’ordinateur local pour collecter les données requises. Il ne peut pas envoyer de données directement à Azure Monitor ni créer une alerte. Pour créer une alerte, demandez au runbook d’écrire une entrée dans un journal personnalisé, puis configurez ce journal pour qu’il soit collecté par Azure Monitor. Créez une règle d’alerte de requête de journal qui se déclenche sur cette entrée de journal.

## <a name="synthetic-transactions"></a>Transactions synthétiques
Une transaction synthétique se connecte à une application ou à un service exécuté sur une machine pour simuler une connexion utilisateur ou le trafic utilisateur réel. Si l’application est disponible, vous pouvez supposer que la machine fonctionne correctement. [Application Insights](../app/app-insights-overview.md) dans Azure Monitor fournit cette fonctionnalité. Elle fonctionne uniquement pour les applications accessibles à partir d’Internet. Pour les applications internes, vous devez ouvrir un pare-feu pour autoriser l’accès à partir d’URL Microsoft spécifiques qui effectuent le test. Vous pouvez également utiliser une autre solution de surveillance, comme Operations Manager.

|Méthode | Description |
|:---|:---|
| [Test d’URL](../app/monitor-web-app-availability.md) | S’assure que le protocole HTTP est disponible et renvoie une page web |
| [Test multiétape](../app/availability-multistep.md) | Simule une session utilisateur |

## <a name="sql-server"></a>SQL Server

Utilisez [SQL Insights](../insights/sql-insights-overview.md) pour surveiller SQL Server exécuté sur vos machines virtuelles.

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment analyser des données dans des journaux d’activité Azure Monitor à l’aide de requêtes de journal.](../logs/get-started-queries.md)
* [Découvrez plus d’informations sur les alertes à l’aide des métriques et des journaux d’activité dans Azure Monitor.](../alerts/alerts-overview.md)