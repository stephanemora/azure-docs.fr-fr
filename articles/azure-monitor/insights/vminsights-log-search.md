---
title: Guide pratique pour interroger des journaux à partir d’Azure Monitor pour machines virtuelles (préversion) | Microsoft Docs
description: La solution Azure Monitor pour les machines virtuelles transfère des métriques et des données de journal à Log Analytics. Cet article décrit les enregistrements correspondants et inclut des exemples de requêtes.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: 676baa6947eaf8b3842b0100657f42a1e6438061
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184880"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Comment interroger des journaux à partir d’Azure Monitor pour machines virtuelles (préversion)
Azure Monitor pour les machines virtuelles collecte des métriques de performances et de connexion, les données d’inventaire des ordinateurs et processus et des informations concernant l’état d’intégrité, puis les transfère au magasin de données Log Analytics dans Azure Monitor.  Ces données peuvent faire l’objet de [recherches](../../azure-monitor/log-query/log-query-overview.md) dans Log Analytics. Vous pouvez appliquer ces données à divers scénarios tels que la planification de la migration, l’analyse de la capacité, la détection et la résolution de problèmes de performances à la demande.

## <a name="map-records"></a>Mapper des enregistrements
Un enregistrement est généré par heure pour chaque ordinateur et processus, en plus des enregistrements générés lorsqu’un processus ou un ordinateur démarre ou est intégré à la fonctionnalité Map d’Azure Monitor pour les machines virtuelles. Les propriétés de ces enregistrements sont décrites dans les tableaux suivants. Les champs et les valeurs des événements ServiceMapComputer_CL sont mappés aux champs de la ressource Machine dans l’API Azure Resource Manager ServiceMap. Les champs et les valeurs des événements ServiceMapProcess_CL sont mappés aux champs de la ressource Processus dans l’API Azure Resource Manager ServiceMap. Le champ ResourceName_s correspond au champ de nom dans la ressource Azure Resource Manager correspondante. 

Il existe des propriétés générées en interne que vous pouvez utiliser pour identifier les ordinateurs et processus uniques :

- Ordinateur : Utilisez *ResourceId* ou *ResourceName_s* pour identifier de façon unique un ordinateur au sein d’un espace de travail Log Analytics.
- Processus : Utilisez *ResourceId* pour identifier de façon unique un processus au sein d’un espace de travail Log Analytics. *ResourceName_s* est unique dans le contexte de l’ordinateur sur lequel le processus est en cours d’exécution (MachineResourceName_s). 

Étant donné que plusieurs enregistrements peuvent exister pour un processus et un ordinateur donnés au cours d’une période spécifique, les requêtes peuvent renvoyer plusieurs enregistrements pour un même ordinateur ou processus. Pour inclure uniquement l’enregistrement le plus récent, ajoutez "| dedup ResourceId" à la requête.

### <a name="connections"></a>connexions
Les métriques de connexion sont écrites dans une nouvelle table dans Log Analytics (VMConnection). Cette table fournit des informations sur les connexions relatives à une machine (entrantes et sortantes). Les métriques de connexion sont également exposées avec des API offrant le moyen d’obtenir une métrique spécifique dans une fenêtre de temps.  Les connexions TCP résultant de l’acceptation (*accept*) sur un socket d’écoute sont des connexions entrantes, tandis que celles créées par le biais d’une connexion (*connect*) à une adresse IP et un port spécifiques sont des connexions sortantes. La direction d’une connexion est représentée par la propriété Direction, qui peut être définie avec la valeur **inbound** ou **outbound**. 

Les enregistrements inclus dans ces tables sont générés à partir des données rapportées par Dependency Agent. Chaque enregistrement représente une observation sur un intervalle de temps d’une minute. La propriété TimeGenerated indique le début de l’intervalle de temps. Chaque enregistrement contient des informations identifiant l’entité respective (à savoir la connexion ou le port), ainsi que des métriques associées à cette entité. Actuellement, seule l’activité réseau utilisant TCP sur IPv4 est rapportée.

À des fins de gestion des coûts et de la complexité, les enregistrements de connexion ne représentent pas des connexions réseau physiques individuelles. Plusieurs connexions réseau physiques sont groupées dans une connexion logique, qui est ensuite reflétée dans la table concernée.  Ainsi, les enregistrements de la table *VMConnection* représentent un regroupement logique et non les connexions physiques individuelles observées. Les connexions réseau physiques dont les attributs suivants présentent la même valeur au cours d’un intervalle d’une minute donné sont agrégées au sein d’un unique enregistrement logique dans *VMConnection*. 

| Propriété | Description |
|:--|:--|
|Direction |Direction de la connexion (valeur *inbound* ou *outbound*) |
|Ordinateur |Nom de domaine complet (FQDN) de l’ordinateur |
|Process |Identité du processus ou de groupes de processus initialisant/acceptant la connexion |
|SourceIp |Adresse IP de la source |
|DestinationIp |Adresse IP de la destination |
|DestinationPort |Numéro de port de la destination |
|Protocole |Protocole utilisé pour la connexion.  La valeur est *tcp*. |

Pour prendre en compte l’impact du regroupement, les informations sur le nombre de connexions physiques groupées sont fournies dans les propriétés suivantes de l’enregistrement :

| Propriété | Description |
|:--|:--|
|LinksEstablished |Nombre de connexions réseau physiques qui ont été établies dans la fenêtre de temps de rapport |
|LinksTerminated |Nombre de connexions réseau physiques qui ont pris fin dans la fenêtre de temps de rapport |
|LinksFailed |Nombre de connexions réseau physiques qui ont échoué dans la fenêtre de temps de rapport. Actuellement, ces informations sont disponibles pour les connexions sortantes uniquement. |
|LinksLive |Nombre de connexions réseau physiques qui ont été ouvertes à la fin de la fenêtre de temps de rapport|

#### <a name="metrics"></a>Mesures

En plus des métriques concernant le nombre de connexions, des informations sur le volume de données envoyées et reçues sur une connexion logique ou un port réseau donné sont également incluses dans les propriétés suivantes de l’enregistrement :

| Propriété | Description |
|:--|:--|
|BytesSent |Nombre total d’octets qui ont été envoyés dans la fenêtre de temps de rapport |
|BytesReceived |Nombre total d’octets qui ont été reçus dans la fenêtre de temps de rapport |
|Réponses |Nombre de réponses observées dans la fenêtre de temps de rapport. 
|ResponseTimeMax |Temps de réponse le plus long (en millisecondes) observé dans la fenêtre de temps de rapport. En l’absence de valeur, la propriété est vide.|
|ResponseTimeMin |Temps de réponse le plus court (en millisecondes) observé dans la fenêtre de temps de rapport. En l’absence de valeur, la propriété est vide.|
|ResponseTimeSum |Somme de tous les temps de réponse (en millisecondes) observés dans la fenêtre de temps de rapport. En l’absence de valeur, la propriété est vide.|

Le troisième type de données rapportées est le temps de réponse : le temps passé par un appelant à attendre qu’une requête envoyée sur une connexion soit traitée par le point de terminaison distant et que ce dernier y réponde. Le temps de réponse rapporté est une estimation du temps de réponse réel du protocole d’application sous-jacent. Il est calculé à l’aide d’une méthode heuristique basée sur l’observation du flux de données entre la source et la destination d’une connexion réseau physique. Sur le plan conceptuel, il s’agit de la différence entre le moment auquel le dernier octet d’une requête quitte l’expéditeur et celui auquel le dernier octet de la réponse lui revient. Ces deux timestamps sont utilisés pour délimiter les événements de requête et de réponse sur une connexion physique donnée. La différence entre eux représente le temps de réponse d’une requête unique. 

Dans le cadre de la première version de cette fonctionnalité, notre algorithme est une approximation qui peut fonctionner de façon plus ou moins efficace selon le protocole d’application réel utilisé pour une connexion réseau donnée. Par exemple, l’approche actuelle fonctionne bien pour les protocoles de requête-réponse comme HTTP(S), mais ne fonctionne pas avec les protocoles unidirectionnels ou basés sur des files d’attente de messages.

Voici quelques points importants à prendre en compte :

1. Si un processus accepte des connexions sur la même adresse IP mais sur plusieurs interfaces réseau, un enregistrement distinct pour chaque interface est rapporté. 
2. Les enregistrements avec une adresse IP générique ne contiennent aucune activité. Ils sont inclus pour représenter le fait qu’un port de la machine est ouvert pour le trafic entrant.
3. Pour réduire le niveau de détail et le volume de données, les enregistrements avec IP générique sont omis en présence d’un enregistrement correspondant (pour le même processus, le même port et le même protocole) avec une adresse IP spécifique. Lorsqu’un enregistrement IP générique est omis, la propriété d’enregistrement IsWildcardBind avec l’adresse IP spécifique a la valeur « True » pour indiquer que le port est exposé sur toutes les interfaces de la machine à l’origine du rapport.
4. Pour les ports liés à une interface spécifique uniquement, IsWildcardBind a la valeur « False ».

#### <a name="naming-and-classification"></a>Affectation de noms et classification
Pour plus de commodité, l’adresse IP de l’extrémité distante d’une connexion est incluse dans la propriété RemoteIp. Pour les connexions entrantes, RemoteIp est identique à SourceIp, tandis que pour les connexions sortantes, elle est identique à DestinationIp. La propriété RemoteDnsCanonicalNames représente les noms canoniques DNS rapportés par la machine pour RemoteIp. Les propriétés RemoteDnsQuestions et RemoteClassification sont réservées en vue d’une utilisation ultérieure. 

#### <a name="geolocation"></a>Géolocalisation
*VMConnection* inclut également des informations de géolocalisation pour l’extrémité distante de chaque enregistrement de connexion dans les propriétés suivantes de l’enregistrement : 

| Propriété | Description |
|:--|:--|
|RemoteCountry |Nom du pays hébergeant RemoteIp.  Par exemple, *États-Unis* |
|RemoteLatitude |Latitude de géolocalisation. Par exemple, *47,68*. |
|RemoteLongitude |Longitude de géolocalisation. Par exemple, *-122,12*. |

#### <a name="malicious-ip"></a>Adresses IP malveillantes
Chaque propriété RemoteIp de la table *VMConnection* est comparée à un ensemble d’adresses IP associées à une activité malveillante connue. Si la propriété RemoteIp est identifiée comme malveillante, les propriétés suivantes de l’enregistrement sont renseignées (elles sont vides lorsque l’adresse IP n’est pas considérée comme malveillante) :

| Propriété | Description |
|:--|:--|
|MaliciousIP |Adresse RemoteIp |
|IndicatorThreadType |L’indicateur de menace détecté est l’une des valeurs suivantes :  *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA* ou *Watchlist*.   |
|Description |Description de la menace observée. |
|TLPLevel |Niveau de protocole TLP (Traffic Light Protocol) est réglé sur l’une des valeurs définies, *Blanc*, *Vert*, *Orange*, *Rouge*. |
|Confiance |Les valeurs sont comprises dans la fourchette *0 – 100*. |
|Severity |Les valeurs sont comprises dans la fourchette *0 – 5*,  dans laquelle *5* correspond à la gravité maximale et *0* à l’absence de gravité. La valeur par défaut est *3*.  |
|FirstReportedDateTime |La première fois que le fournisseur a déclaré l’indicateur. |
|LastReportedDateTime |La dernière fois que l’indicateur a été vu par Interflow. |
|IsActive |Indique les indicateurs sont désactivés avec la valeur *True* ou la valeur *False*. |
|ReportReferenceLink |Liens vers des rapports relatifs à un observable donné. |
|AdditionalInformation |Fournit des informations supplémentaires, s’il y a lieu, sur la menace observée. |

### <a name="servicemapcomputercl-records"></a>Enregistrements ServiceMapComputer_CL
Les enregistrements de type *ServiceMapComputer_CL* ont des données d’inventaire pour les serveurs avec Dependency Agent. Les propriétés de ces enregistrements sont décrites dans le tableau suivant :

| Propriété | Description |
|:--|:--|
| type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Identificateur unique d’une machine au sein de l’espace de travail |
| ResourceName_s | Identificateur unique d’une machine au sein de l’espace de travail |
| ComputerName_s | Nom de domaine complet (FQDN) de l’ordinateur |
| Ipv4Addresses_s | Liste des adresses IPv4 du serveur |
| Ipv6Addresses_s | Liste des adresses IPv6 du serveur |
| DnsNames_s | Tableau de noms DNS |
| OperatingSystemFamily_s | Windows ou Linux |
| OperatingSystemFullName_s | Nom complet du système d’exploitation  |
| Bitness_s | Nombre de bits de la machine (32 bits ou 64 bits)  |
| PhysicalMemory_d | Mémoire physique en Mo |
| Cpus_d | Nombre de processeurs |
| CpuSpeed_d | Vitesse du processeur en MHz|
| VirtualizationState_s | *inconnu*, *physique*, *virtuel*, *hyperviseur* |
| VirtualMachineType_s | *hyper-v*, *vmware*, etc. |
| VirtualMachineNativeMachineId_g | ID de machine virtuelle assigné par son hyperviseur |
| VirtualMachineName_s | Nom de la machine virtuelle |
| BootTime_t | Temps de démarrage |

### <a name="servicemapprocesscl-type-records"></a>Enregistrements de type ServiceMapProcess_CL
Les enregistrements de type *ServiceMapProcess_CL* ont des données d’inventaire pour les processus connectés à TCP sur les serveurs avec Dependency Agent. Les propriétés de ces enregistrements sont décrites dans le tableau suivant :

| Propriété | Description |
|:--|:--|
| type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Identificateur unique d’un processus au sein de l’espace de travail |
| ResourceName_s | identificateur unique d’un processus au sein de la machine sur laquelle il s’exécute|
| MachineResourceName_s | Nom de ressource de la machine |
| ExecutableName_s | Nom de l’exécutable du processus |
| StartTime_t | Heure de début du pool de processus |
| FirstPid_d | Premier PID dans le pool de processus |
| Description_s | Description du processus |
| CompanyName_s | Nom de la société |
| InternalName_s | Nom interne |
| ProductName_s | Nom du produit |
| ProductVersion_s | Version du produit |
| FileVersion_s | Version du fichier |
| CommandLine_s | Ligne de commande |
| ExecutablePath _s | Chemin d’accès du fichier exécutable |
| WorkingDirectory_s | Le répertoire de travail |
| Nom d’utilisateur | Compte sous lequel le processus s’exécute |
| UserDomain | Domaine sous lequel le processus s’exécute |

## <a name="sample-log-searches"></a>Exemples de recherches dans les journaux

### <a name="list-all-known-machines"></a>Liste de tous les ordinateurs connus
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Répertorier la capacité de mémoire physique de tous les ordinateurs gérés
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`

### <a name="list-computer-name-dns-ip-and-os"></a>Répertorier le nom de l’ordinateur, le DNS, l’adresse IP et le système d’exploitation.
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Rechercher tous les processus contenant "sql" dans la ligne de commande
`ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Rechercher un ordinateur (enregistrement le plus récent) par nom de ressource
`search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Rechercher une machine (enregistrement le plus récent) par adresse IP
`search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-known-processes-on-a-specified-machine"></a>Répertorier tous les processus sur une machine spécifiée
`ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-computers-running-sql"></a>Répertorier tous les ordinateurs exécutant SQL
`ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Répertorier toutes les versions de produit uniques de CURL dans mon centre de données
`ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Créer un groupe de tous les ordinateurs exécutant CentOS
`ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Synthétiser les connexions sortantes d’un groupe de machines
```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>Étapes suivantes
* Si vous débutez dans l’écriture de requêtes dans Log Analytics, voyez [comment utiliser la page Log Analytics](../../azure-monitor/log-query/get-started-portal.md) dans le Portail Azure pour écrire des requêtes Log Analytics.
* Découvrez [l’écriture de requêtes de recherche](../../azure-monitor/log-query/search-queries.md).
