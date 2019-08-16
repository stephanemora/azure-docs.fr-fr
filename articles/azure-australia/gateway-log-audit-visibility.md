---
title: Journalisation, audit et visibilité des passerelles dans Azure Australie
description: Configuration de la journalisation, des audits et de la visibilité dans les régions australiennes pour répondre aux exigences particulières de la politique, de la réglementation et de la législation du gouvernement australien.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 153b9d503dfece404455fbb7e8cb51c51686ec57
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824302"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Journalisation, audit et visibilité des passerelles dans Azure Australie

La détection et la résolution des menaces de cybersécurité reposent sur la création, la collecte et l’analyse des données relatives au fonctionnement d’un système.

Microsoft dispose d’outils intégrés dans Azure pour vous aider à mettre en œuvre la journalisation, l’audit et la visibilité afin que vous puissiez gérer la sécurité de vos systèmes déployés dans Azure. Il existe également une architecture de référence qui s’aligne sur l’aide des consommateurs australien Cyber Security Center (ACSC) et l’intention du Manuel de sécurité des informations (ISM).

Les passerelles servent de mécanismes de contrôle de flux de l’information au niveau de la couche réseau et peuvent également contrôler les informations au niveau des couches supérieures du modèle OSI (Open System Interconnect). Les passerelles sont nécessaires pour contrôler les flux de données entre les domaines de sécurité et empêcher tout accès non autorisé depuis des réseaux externes. Étant donné l’importance des passerelles dans le contrôle du flux des informations entre les domaines de sécurité, tout échec, en particulier pour des classifications plus élevées, peut avoir des conséquences graves. Ainsi, la mise en place de mécanismes solides permettant d’alerter le personnel en cas de situations pouvant entraîner des incidents de cybersécurité est extrêmement importante pour les passerelles.

L’implémentation des fonctionnalités de journalisation et d’alerte pour les passerelles peut aider à détecter les incidents de cybersécurité, les tentatives d’intrusion et les modèles d’utilisation inhabituels. De plus, le stockage des journaux d’événements sur un serveur de journalisation sécurisé distinct complique la tâche de l’adversaire souhaitant supprimer les informations de journalisation afin de détruire toute preuve d’une intrusion dans le système informatique ciblée.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Exigences de l’ASCS (Australian Cyber Security Centre)

Les exigences de sécurité globales pour les systèmes du Commonwealth sont définies dans le Manuel de sécurité des informations (ISM) de l’ACSC. Pour permettre aux entités du Commonwealth à répondre à ces exigences dans Azure, les publications du *GUIDE DU CONSOMMATEUR DE L’ACSC – Microsoft Azure catégorie PROTECTED* et du *RAPPORT DE CERTFIFICATIO DE L’ACSC – Microsoft Azure-détails* détaillent les exigences particulières relatives à la journalisation, à l’audit et à la visibilité suivantes :

1. Pour réduire les risques liés à l’utilisation de ressources cloud sous-jacentes partagées, les entités du Commonwealth doivent opter pour les fonctionnalités fournies par Microsoft Azure, notamment Azure Security Center, Azure Monitor, Azure Policy et Azure Advisor pour aider les entités à effectuer la surveillance en temps réel de leurs charges de travail Azure

2. L’ACSC recommande également aux entités du Commonwealth de lui transférer tous les journaux de sécurité pour l’ensemble du gouvernement australien aux fins de surveillance.

3. Pour réduire les risques, les entités du Commonwealth doivent configurer dans leur abonnement Azure :

    * Activer Azure Security Center
    * Passer au niveau Standard
    * Activer l’approvisionnement automatique de Microsoft Monitoring Agent pour les machines virtuelles prises en charge
    * Examiner, hiérarchiser et atténuer régulièrement les suggestions et les alertes de sécurité sur le tableau de bord du centre de sécurité

4. Les entités gouvernementales doivent activer le transfert des journaux et des événements de leur abonnement Azure vers ACSC pour lui offrir une visibilité sur la non conformité à ce guide. Azure Event Hubs offre la possibilité d’effectuer des flux de journalisation externes sur les systèmes ACSC ou locaux appartenant à l’entité du Commonwealth

5. Les entités du Commonwealth doivent adapter l’enregistrement qu’elles activent dans Azure aux exigences indiquées dans l’ISM

6. Microsoft conserve les journaux dans Azure pendant 90 jours. Les entités du client doivent implémenter un régime d’archivage des journaux pour s’assurer qu’ils peuvent être conservés pendant sept ans, tel qu’exigé par NAA AFDA

7. Les entités du Commonwealth possédant des fonctionnalités Security Information and Event Management (SIEM) locales ou sur Azure peuvent également transférer les journaux vers ces systèmes

8. Les entités du Commonwealth doivent implémenter les journaux d’activité de flux Network Watcher pour les groupes de sécurité réseau (NGS) et les machines virtuelles. Ces journaux d’activité doivent être stockés dans un compte de stockage prévu à cet effet où seuls les journaux d’activité de sécurité sont stockés. L’accès au compte de stockage doit être sécurisé par des contrôles d’accès en fonction du rôle.

9. Les entités Commonwealth doivent implémenter le Guide des consommateurs ACSC pour s’assurer que les charges de travail Azure répondent à l’objectif d’ISM pour la journalisation et la surveillance. Les entités du Commonwealth doivent également accepter les fonctionnalités Azure qui aident ACSC à bénéficier en temps réel d’une surveillance, d’alertes et des journaux associés à l’utilisation d’Azure par le gouvernement australien.

## <a name="architecture"></a>Architecture

Pour bien comprendre le trafic entrant et sortant de votre environnement Azure, vous devez activer la journalisation nécessaire sur un ensemble de composants précis. Cela garantit une visibilité complète de l’environnement et fournit les données nécessaires permettant d’effectuer l’analyse.

![Architecture de surveillance Azure](media/visibility.png)

## <a name="components"></a>Composants

L’architecture présentée ci-dessus est constituée de composants discrets qui fournissent les fonctionnalités de source, de collecte, de rétention et d’analyse des journaux ou de réponse aux incidents. Cette architecture comprend des composants individuels qui sont généralement utilisés dans les déploiements Azure accessibles par Internet.

|Fonctions|Composants|
|---|---|
|Sources de journaux|<ul><li>Application Gateway</li><li>Passerelle VPN</li><li>Pare-feu Azure</li><li>Appliances virtuelles réseau</li><li>Azure Load Balancer</li><li>Virtual Machines</li><li>Serveurs DNS (Domain Naming System)</li><li>Syslog et/ou serveurs de collecte de journaux</li><li>Groupes de sécurité réseau</li><li>Journaux d’activité</li><li>Journal de diagnostic Azure</li><li>Azure Policy</li></ul>|
|Collecte de journaux|<ul><li>Event Hubs</li><li>Network Watcher</li><li>Log Analytics</li></ul>|
|Rétention des journaux|<ul><li>Stockage Azure</li></ul>|
|Analyse des journaux|<ul><li>Azure Security Center (ASC)</li><li>Azure Advisor</li><li>Solutions Log Analytics<ul><li>Traffic Analytics</li><li>DNS Analytics (préversion)</li><li>Activity Log Analytics</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|Réponse aux incidents|<ul><li>Alertes Azure</li><li>Azure Automation</li></ul>|
|

L’architecture fonctionne en générant d’abord les journaux à partir des sources nécessaires, puis en les collectant dans des référentiels centralisés. Une fois que vous avez collecté les journaux, ils peuvent être :

* utilisés par Azure Analysis Services pour obtenir des informations,
* transférés vers des systèmes externes, ou
* archivés dans le stockage pour une conservation à long terme.

Pour répondre aux événements clés ou aux incidents détectés par les outils d’analyse, les alertes peuvent être configurées et l’automatisation développée pour prendre les mesures nécessaires en matière de gestion et de réponse proactives.

## <a name="general-guidance"></a>Règle générale

Lors de l’implémentation des composants énumérés dans cet article, les recommandations générales suivantes s’appliquent :

* Validez la disponibilité de la région des services, en veillant à ce que toutes les données restent dans les emplacements autorisés et soient déployées vers Australie Centre ou Australie Centre 2 comme première préférence pour les charges de travail PROTÉGÉES

* Consultez la publication *Azure – Rapport de certification ACSC – Protégé 2018* pour connaître l’état de certification des services individuels et effectuer des auto-évaluations sur les composants pertinents non inclus dans le rapport conformément au *GUIDE DU CONSOMMATEUR DE L’ACSC – Microsoft Azure catégorie PROTECTED*

* Pour les composants qui ne sont pas référencés dans cet article, les entités du Commonwealth doivent respecter les principes de création, de capture, d’analyse et de conservation des journaux d’activité

* Détectez et hiérarchisez la journalisation, l’audit et la visibilité sur les systèmes à valeur élevée, ainsi que tous les points d’entrée et de sortie réseau des systèmes hébergés dans Azure

* Consolidez les journaux d’activité et réduisez le nombre d’instances d’outils de journalisation, telles que les comptes de stockage, les espaces de travail Log Analytics et les Event Hubs

* Restreignez les privilèges Administrateur grâce aux contrôles d’accès en fonction du rôle

* Utiliser l’authentification multifacteur (MFA) pour l’administration ou la configuration de comptes de ressources dans Azure

* Lorsque vous centralisez la collecte des journaux sur plusieurs abonnements, assurez-vous que les administrateurs disposent des privilèges nécessaires dans chaque abonnement

* Vérifiez la connectivité réseau et les configurations de proxy nécessaire pour les machines Virtuelles, y compris les appliances virtuelles réseau (NVA), les serveurs de collecte de journaux et les serveurs DNS, pour vous connecter aux services Azure requis, tels que les espaces de travail Log Analytics, Event Hubs et stockage

* Configurez Microsoft Monitoring Agent (MMA) pour utiliser la version TLS 1.2

* Utiliser Azure Policy pour surveiller et faire et assurer la conformité des exigences

* Appliquer le chiffrement à tous les référentiels de données tels que le stockage et les bases de données

* Utiliser le stockage localement redondant (LRS) et des captures instantanées pour la disponibilité des comptes de stockage et des données associées

* Envisagez un stockage géo-redondant (GRS) ou un stockage hors site à aligner sur les stratégies de récupération d’urgence

|Ressource|URL|
|---|---|
|Documents de conformité à la réglementation et aux politiques australiennes|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Produits Azure – Régions australiennes et non régionales|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Australie Centre, Australie Centre 2, Australie Est, Australie Sud-Est](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Livre blanc Sécurité Microsoft Azure et gestion des journaux d’audit|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Configuration de Microsoft Monitoring Agent|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)|
|

## <a name="component-guidance"></a>Guide sur les composants

Cette section fournit des informations sur la finalité de chaque composant et sur son rôle dans l’architecture globale de la journalisation, de l’audit et de la visibilité. Des liens supplémentaires permettent d’accéder à des ressources utiles, telles que la documentation de référence, des guides et des didacticiels.

## <a name="log-sources"></a>Sources de journaux

Avant d’effectuer des analyses, des alertes ou des rapports, vous devez générer les journaux d’activité nécessaires. Les journaux d’activité Azure sont classés dans les journaux de contrôle/gestion, les journaux des plans de données et les événements traités.

|Type|Description|
|---|---|
|Journaux d’activité de contrôle/gestion|Fournissez des d’informations sur les opérations Azure Resource Manager|
|Journaux d’activité de plan de données|Fournissez des informations sur les événements déclenchés dans le cadre de l’utilisation des ressources Azure, telles que les journaux d’activités d’une machine virtuelle et les journaux de diagnostic disponibles sir Azure Monitor|
|Événements traités|Fournissez des informations sur les alertes/événements analysés qui ont été traités par Azure, par exemple l’endroit où Azure Security Center a traité et analysé les abonnements pour fournir des alertes de sécurité|
|

### <a name="application-gateway"></a>Application Gateway

Azure Application passerelle est l’un des points d’entrée possibles dans un environnement Azure. Vous devez donc capturer les informations relatives aux connexions entrantes communiquant avec les applications Web. Application Gateway peut fournir des informations cruciales sur l’utilisation des applications web et permet de détecter des incidents de cybersécurité. Application Gateway envoie des métadonnées au journal d’activité et aux journaux de diagnostic dans Azure Monitor où elles peuvent être utilisées dans Log Analytics ou distribuées à un Event Hub ou à un compte de stockage.

|Ressources|Lien|
|---|---|
|Documentation Application Gateway|[https://docs.microsoft.com/azure/application-gateway/](https://docs.microsoft.com/azure/application-gateway/)|
|Guide de démarrage rapide Application Gateway|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)|
|

### <a name="vpn-gateway"></a>Passerelle VPN

La passerelle VPN est un point d’entrée potentiel pour un large éventail de communications dans l’environnement Azure, comme la connexion à un environnement local et le trafic administratif. La journalisation sur les passerelles VPN fournit des informations et une traçabilité pour les connexions établies dans l’environnement Azure. La journalisation permet de fournir des audits et des analyses et de détecter ou d’analyser les connexions malveillantes ou anormales. Les journaux de la passerelle VPN sont envoyés au journal d’activité d’Azure Monitor où ils peuvent être utilisés dans Log Analytics ou distribués à un Event Hub ou à un compte de stockage.

|Ressources|Lien|
|---|---|
|Documentation de la passerelle VPN|[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway)|
|Guide précis sur la passerelle VPN du gouvernement australien.|[Configuration de la passerelle VPN Azure](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Pare-feu Azure

Le pare-feu Azure fournit un point de sortie contrôlé à partir d’un environnement Azure et les journaux générés, qui incluent des informations sur les connexions sortantes et les tentatives de connexions sortantes. C’est un élément important de votre stratégie de journalisation. Ces journaux d’activité peuvent confirmer que les systèmes fonctionnent comme prévu et permettre de détecter des codes malveillants ou des acteurs tentant de se connecter à des systèmes externes non autorisés. Le pare-feu Azure envoie des métadonnées au journal d’activité et aux journaux de diagnostic dans Azure Monitor où il peut être utilisé dans Log Analytics ou distribués à un Event Hub ou à un compte de stockage.

|Ressources|Lien|
|---|---|
|Documentation sur le pare-feu Azure|[https://docs.microsoft.com/azure/firewall/](https://docs.microsoft.com/azure/firewall)|
|Didacticiel : Superviser les journaux d’activité et les métriques du Pare-feu Azure|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)|
|

### <a name="network-virtual-appliances-nva"></a>Appliances virtuelles réseau

Les appliances virtuelles réseau peuvent servir de complément aux fonctionnalités de sécurité disponibles en mode natif dans Azure. Les journaux d’activité générés sur les appliances virtuelles réseau peuvent être des ressources précieuses permettant de détecter des incidents de cyber-sécurité et sont un élément clé d’une stratégie globale de journalisation, d’audit et de visibilité. Pour capturer les journaux à partir d’appliances virtuelles réseau, vous pouvez utiliser Microsoft Monitoring Agent (MMA). Pour les appliances virtuelles réseau qui ne prennent pas en charge l’installation de MMA, utilisez un journal Syslog ou un autre serveur de collecte de journaux pour relayer les journaux.

|Ressources|Lien|
|---|---|
|Vue d’ensemble des appliances virtuelles réseau|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|Documentation sur les appliances virtuelles réseau|Consultez la documentation du fournisseur sur l’implémentation des appliances virtuelles réseau pertinente dans Azure|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

Les journaux d’Azure Load Balancer sont utilisés pour obtenir des informations utiles sur les connexions et l’utilisation relatives aux systèmes déployés dans Azure. Ils peuvent être utilisés pour la surveillance de l’intégrité et de la disponibilité, mais constituent également un autre composant clé permettant d’obtenir les informations nécessaires sur le trafic des communications et de détecter les modèles de trafic malveillants ou anormaux. Azure Load Balancer se connecte au journal d’activité et aux journaux de diagnostic dans Azure Monitor où il peut être utilisé dans Log Analytics ou distribués à un Event Hub ou à un compte de stockage.

|Ressources|Lien|
|---|---|
|Documentation sur Azure Load Balancer|[https://docs.microsoft.com/azure/load-balancer](https://docs.microsoft.com/azure/load-balancer)|
|Métriques et diagnostics d’intégrité du Load Balancer Standard|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)|
|

### <a name="virtual-machines"></a>Virtual Machines

Les machines virtuelles sont des points de terminaison qui envoient et reçoivent des communications réseau, traitent les données et fournissent des services. Comme les machines virtuelles peuvent héberger des données ou des services système importants, il est essentiel de s’assurer qu’ils fonctionnent correctement et de détecter les incidents de cybersécurité. Les machines virtuelles collectent divers journaux d’événements et d’audit qui peuvent suivre le fonctionnement du système et les actions effectuées sur ce dernier. Les journaux d’activité collectés sur les machines virtuelles peuvent être transférés vers un espace de travail Log Analytics à l’aide de Microsoft Monitoring Agent où ils peuvent être analysés par Azure Security Center et les solutions Log Analytics applicables. Les machines virtuelles peuvent également s’intégrer directement à Azure Event Hubs ou à une SIEM, soit directement, soit via un serveur de collecte de journaux.

|Ressources|Lien|
|---|---|
|Virtual Machines|[https://docs.microsoft.com/azure/virtual-machines](https://docs.microsoft.com/azure/virtual-machines)|
|Collecter des données à partir de machines virtuelles|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|
|Diffuser les journaux des machines virtuelles vers Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs)|
|

### <a name="domain-name-services-dns-servers"></a>Serveurs DNS (Domain Name Services)

Les journaux d’activités de serveur DNS fournissent des informations clés sur les services auxquels les systèmes essaient d’accéder en interne ou en externe. La capture des journaux d’activité DNS peut permettre de détecter un incident de cybersécurité et fournir des informations sur le type d’incident et les systèmes pouvant être concernés. Microsoft Management Agent (MMA) peut être utilisé sur des serveurs DNS pour transférer les journaux d’activité par le biais de Log Analytics pour une utilisation dans DNS Analytics (préversion).

|Ressources|Lien|
|---|---|
|Résolution de noms Azure sur plusieurs réseaux virtuels|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)|
|

### <a name="syslog-and-log-collection-servers"></a>Syslog et serveurs de collecte de journaux

Pour recevoir des journaux à partir d’appliances virtuelles réseau ou des journaux de sécurité personnalisés à partir d’autres systèmes pour une utilisation dans SIEM, les serveurs dédiés peuvent être déployés dans les réseaux virtuels Azure. Les journaux syslog peuvent être collectés sur un serveur Syslog et relayés vers Log Analytics à des fins d’analyse. Un serveur de collecte de journaux est un terme générique pour toutes les fonctionnalités d’agrégation et de distribution de journaux utilisées par les systèmes de surveillance centralisée ou les SIEM. Ils peuvent être utilisés pour simplifier l’architecture réseau et la sécurité, ainsi que pour filtrer et agréger des journaux d’activités avant d’être distribués à la fonctionnalité centralisée.

|Ressources|Lien|
|---|---|
|Sources de données Syslog dans Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)|
|Serveur de collecte de journaux|Consultez la documentation du fournisseur pour plus d’informations sur la surveillance et l’architecture SIEM|
|

### <a name="network-security-groups-nsgs"></a>Groupes de sécurité réseau (NSG)

Les groupes de sécurité réseau contrôlent le trafic à l’intérieur et à l’extérieur des réseaux virtuels dans Azure. Les groupes de sécurité réseau appliquent des règles pour les flux de trafic autorisés ou refusés, qui incluent le trafic dans et entre Azure et les réseaux externes, tels qu’un réseau local ou Internet. Les groupes de sécurité réseau sont appliqués aux sous-réseaux au sein d’un réseau virtuel ou à des interfaces réseau individuelles. Pour capturer des informations sur le trafic entrant et sortant des systèmes dans Azure, les journaux des groupes de sécurité réseau peuvent être activés par le biais de la fonctionnalité des journaux de flux NSG Network Watcher. Ces journaux d’activité sont utilisés pour former une ligne de base pour le fonctionnement standard d’un système et sont la source de données pour Traffic Analytics, qui fournit des informations détaillées sur les modèles de trafic les systèmes hébergés dans Azure.

|Ressources|Lien|
|---|---|
|Documentation sur les groupes de sécurité réseau|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Présentation de la journalisation des flux pour les groupes de sécurité réseau|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|
|Didacticiel : journaliser le trafic réseau à destination et en provenance d’une machine virtuelle à l’aide du portail Azure|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)|
|

### <a name="azure-activity-log"></a>Journaux d’activité

Le journal d’activité Azure, qui fait partie d’Azure Monitor, est un journal d’abonnement qui fournit un aperçu de tous les événements relatifs aux abonnements qui se sont produits dans Azure. Le journal d’activité permet de répondre aux questions « quoi, qui et quand » concernant toutes les opérations d’écriture (PUT, POST, DELETE) effectuées ***on*** les ressources dans un abonnement. Le journal d’activité est essentiel pour le suivi des modifications de configuration effectuées dans l’environnement Azure. Les journaux d’activité Azure sont automatiquement utilisables dans les solutions Log Analytics et peuvent être envoyés à Event Hubs ou au stockage Azure à des fins de traitement ou de rétention.

|Ressources|Lien|
|---|---|
|Documentation sur le journal d’activité d’Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|Diffuser en continu le journal des activités Azure sur les Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)|
|

### <a name="azure-diagnostic-log"></a>Journal de diagnostic Azure

Les journaux de diagnostic Azure Monitor sont des journaux d’activité émis par un service Azure qui fournissent des informations riches et fréquentes sur le fonctionnement de ce service. Les journaux de diagnostic fournissent des informations détaillées sur le fonctionnement d’une ressource et peuvent être utilisés pour une série d’exigences telles que l’audit ou la résolution des problèmes. Les journaux d’activité d’Azure sont automatiquement utilisables dans les solutions Log Analytics et peuvent être envoyés à Event Hubs ou au stockage Azure à des fins de traitement ou de rétention.

|Ressources|Lien|
|---|---|
|Documentation sur le journal de diagnostic d’Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|Services de support pour les journaux de diagnostic|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)|
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy applique des règles sur la façon dont les ressources peuvent être déployées, telles que le type, l’emplacement et la configuration. Azure Policy peut être configuré pour s’assurer que les ressources ne peuvent être déployées que si elles sont conformes aux exigences. Azure Policy est un composant fondamental permettant de maintenir l’intégrité d’un environnement Azure. Les événements relatifs à Azure Policy sont enregistrés dans le journal d’activité d’Azure et sont automatiquement utilisables dans les solutions Log Analytics et peuvent être envoyés à Event Hubs ou au stockage Azure à des fins de traitement ou de rétention.

|Ressources|Lien|
|---|---|
|Documentation Azure Policy|[https://docs.microsoft.com/azure/governance/policy](https://docs.microsoft.com/azure/governance/policy)|
|Exploitation des modèles d’Azure Policy et de Gestionnaire des ressources à l’aide d’Azure Blueprints|[https://docs.microsoft.com/azure/governance/blueprints/overview](https://docs.microsoft.com/azure/governance/blueprints/overview)|
|

## <a name="log-collection"></a>Collection de journaux

Une fois générés à partir des différentes sources de journaux, les journaux d’activité doivent être stockés dans un emplacement centralisé pour un accès et une analyse continue. Azure fournit plusieurs méthodes et options pour la collecte des journaux qui peuvent être utilisées en fonction du type de journal et des exigences.

### <a name="event-hubs"></a>Event Hubs

L’objectif d’un Event Hub est de regrouper les données du journal pour les différentes sources à distribuer. À partir du Event Hub, les données de journal peuvent être envoyées à SIEM et ACSC pour la conformité et au stockage pour une rétention à long terme.

|Ressources|Lien|
|---|---|
|Documentation Event Hubs|[https://docs.microsoft.com/azure/event-hubs](https://docs.microsoft.com/azure/event-hubs)|
|Guide sur les Event Hubs et les outils externes|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics fait partie d’Azure Monitor et est utilisé pour l’analyse des journaux. Log Analytics utilise un espace de travail comme mécanisme de stockage où les données de journalisation peuvent être mises à disposition pour un large éventail d’outils d’analyse et de solutions disponibles dans Azure. Log Analytics s’intègre directement à une large gamme de composants Azure, ainsi qu’à des machines virtuelles via Microsoft Monitoring Agent.

|Ressources|Lien|
|---|---|
|Documentation Log Analytics|[https://docs.microsoft.com/azure/azure-monitor](https://docs.microsoft.com/azure/azure-monitor)|
|Didacticiel : Analyser des données dans Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)|
|

### <a name="network-watcher"></a>Network Watcher

L’utilisation de Network Watcher est recommandée par ACSC pour vous aider à comprendre et à capturer le trafic réseau dans un abonnement Azure. Les journaux de flux des groupe de sécurité réseau fournissent l’entrée à la solution Traffic Analytics dans Log Analytics, qui offre une visibilité, une analyse et des rapports accrus en mode natif dans Azure. Network Watcher fournit également une fonctionnalité de capture de paquets directement à partir du Portail Azure sans avoir besoin de se connecter à la machine virtuelle. La fonctionnalité de capture des paquets Network Watcher vous permet de créer des sessions de capture afin d’effectuer le suivi du trafic à partir de la machine virtuelle et vers celle-ci.

|Ressources|Lien|
|---|---|
|Network Watcher|[https://docs.microsoft.com/azure/network-watcher](https://docs.microsoft.com/azure/network-watcher)|
|Vue d’ensemble des captures de paquets|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
|

## <a name="log-retention"></a>Rétention des journaux

Pour les organismes gouvernementaux australiens, les journaux d’activité capturés dans Azure doivent être conservés conformément aux Archives nationales d’Australie [Administrative Functions Disposal Authority (AFDA)](http://www.naa.gov.au/information-management/records-authorities/types-of-records-authorities/AFDA/index.aspx), qui indique que les journaux doivent être conservés pendant sept ans.

|Emplacement du journal|Période de rétention|
|---|---|
|Journaux d’activité|Jusqu’à 90 jours|
|Espace de travail Log Analytics|Jusqu’à deux ans|
|Event Hub|Jusqu’à sept jours|
|

Il vous incombe de vous assurer que les journaux d’activités sont archivés de manière appropriée pour respecter les exigences mises en place par AFDA et les autres exigences législatives.

### <a name="azure-storage"></a>Stockage Azure

Le stockage Azure est le référentiel des journaux d’activité pour la rétention à long terme dans Azure. Le stockage Azure peut être utilisé pour archiver les journaux d’activité d’Azure, y compris Event Hubs, le journal d’activité d’Azure et les journaux de diagnostic d’Azure. La période de rétention des données dans le stockage peut être définie sur zéro ou peut être indiquée sous la forme d’un nombre de jours. Une rétention de zéro jour signifie que les journaux sont conservés indéfiniment. Dans le cas contraire, la valeur peut être un nombre de jours compris entre 1 et 2147483647.

|Ressources|Lien|
|---|---|
|Documentation Stockage Azure|[https://docs.microsoft.com/azure/storage](https://docs.microsoft.com/azure/storage)|
|Capturer des événements avec Azure Event Hubs dans le stockage Blob Azure ou Azure Data Lake Storage|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Didacticiel : Archiver les métriques Azure et les journaux de données à l’aide du Stockage Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data)|
|Réplication du stockage Azure|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)|
|Création d’un instantané d’objet blob|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Analyse des journaux

Une fois généré et stocké dans un emplacement centralisé, les journaux doivent être analysés pour faciliter la détection des incidents de sécurité et les tentatives. Lorsque des incidents de sécurité sont détectés, une agence a besoin d’être en mesure de répondre à ces incidents et d’assurer le suivi, le contrôle et la correction des menaces.

### <a name="azure-security-center-asc"></a>Azure Security Center (ASC)

Azure Security Center fournit des fonctionnalités unifiées de gestion de la sécurité et de protection avancée contre les menaces. Azure Security Center peut appliquer des stratégies de sécurité sur l’ensemble des charges de travail, limiter l’exposition aux menaces, détecter et répondre aux attaques. Azure Security Center fournit des tableaux de bord et des analyses sur un large éventail de composants Azure. L’utilisation de Azure Security Center est exigée conformément au Guide du consommateur de l’ACSC.

|Ressources|Lien|
|---|---|
|Documentation Azure Security Center|[https://docs.microsoft.com/azure/security-center](https://docs.microsoft.com/azure/security-center)|
|Démarrage rapide : Intégrer un abonnement Azure à Security Center Standard|[https://docs.microsoft.com/azure/security-center/security-center-get-started](https://docs.microsoft.com/azure/security-center/security-center-get-started)|
|

### <a name="traffic-analytics"></a>Traffic Analytics

Traffic Analytics est une solution cloud qui offre une visibilité sur l’activité des utilisateurs et des applications dans Azure. Traffic Analytics analyse les journaux de flux des groupe de sécurité réseau Network Watcher pour fournir des informations sur le flux de trafic dans Azure. Traffic Analytics est utilisé pour fournir des tableaux de bord, des rapports, des analyses et des fonctionnalités de réponse aux événements liés au trafic sur des réseaux virtuels. Traffic Analytics fournit des informations significatives et permet d’identifier et de résoudre les incidents de cybersécurité.

|Ressources|Lien|
|---|---|
|Documentation sur Log Analytics|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)|
|

### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor analyse la configuration des ressources et d’autres données pour recommander des solutions pour améliorer les performances, la sécurité et la haute disponibilité des ressources tout en recherchant des moyens pour réduire les dépenses Azure globales. Azure Advisor est recommandé par l’ACSC et fournit des conseils facilement accessibles et détaillés sur la configuration de l’environnement Azure.

|Ressources|Lien|
|---|---|
|Documentation Azure Advisor|[https://docs.microsoft.com/azure/advisor](https://docs.microsoft.com/azure/advisor)|
|Prise en main d’Azure Advisor|[https://docs.microsoft.com/azure/advisor/advisor-get-started](https://docs.microsoft.com/azure/advisor/advisor-get-started)|
|

### <a name="dns-analytics-preview"></a>DNS Analytics (préversion)

DNS Analytics est une solution Log Analytics qui collecte, analyse et met en corrélation les journaux d’audit et d’analyse DNS Windows et d’autres données connexes. DNS Analytics détecte les clients qui tentent de résoudre les noms de domaines malveillants, les enregistrements de ressources obsolètes, les noms de domaine fréquemment interrogés et les clients DNS communiquant. DNS Analytics fournit également des informations sur la charge des demandes sur les serveurs DNS et les échecs d’inscription DNS dynamiques. Traffic Analytics est utilisé pour fournir des tableaux de bord, des rapports, des analyses et des fonctionnalités de réponse aux événements requête DNS effectuées dans l’environnement Azure. DNS Analytics fournit des informations significatives et permet de détecter et de résoudre les incidents de cybersécurité.

|Ressources|Lien|
|---|---|
|Documentation sur DNS Analytics|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)|
|

### <a name="activity-log-analytics"></a>Activity Log Analytics

Activity Log Analytics est une solution Log Analytics vous aide à analyser et a rechercher le journal d’activité Azure parmi tous les abonnements Azure. Activity Log Analytics est utilisé pour fournir des tableaux de bord, des rapports, des analyses et des fonctionnalités de réponse aux événements centralisés liés aux actions effectuées sur les ressources dans l’ensemble de l’environnement Azure. Activity Log Analytics peut vous aider à effectuer des audits et des investigations.

|Ressources|Lien|
|---|---|
|Collecter et analyser les journaux d’activités Azure dans Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)|
|

### <a name="security-information-and-event-management-siem"></a>Security Information and Event Management (SIEM)

SIEM est un système qui fournit un stockage, un audit et une analyse des journaux de sécurité centralisés grâce à des mécanismes définis pour la enquêter sur un large éventail de données de journal et d’outils intelligents pour l’analyse, la création de rapports et la détection d’incidents et de réponse. Vous pouvez utiliser les fonctionnalités SIEM qui incluent les informations de journalisation Azure pour compléter les fonctionnalités de sécurité fournies en mode natif dans Azure. Les entités du Commonwealth peuvent utiliser un SIEM hébergé sur des machines virtuelles dans Azure, localement ou en tant que Software as a Service (SaaS) selon des exigences particulières.

|Ressources|Lien|
|---|---|
|Azure Sentinel (préversion)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|Documentation SIEM|Consultez la documentation du fournisseur pour obtenir des conseils sur l’architecture SIEM|
|Utilisez Azure Monitor pour intégrer les outils SIEM|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>Australian Cyber Security Centre (centre de sécurité australien)

L’Australien Cyber Security Centre (ACSC) est le chef de file du gouvernement australien en matière de cybersécurité nationale. Il regroupe les fonctionnalités de cybersécurité du gouvernement australien pour améliorer la cyber-résistance de la communauté australienne et soutenir la prospérité économique et sociale de l’Australie à l’ère numérique. L’ACSC recommande également aux entités du Commonwealth de lui transférer tous les journaux de sécurité, les événements et les journaux mandatés générés par le système pour l’ensemble du gouvernement australien aux fins de surveillance.

|Ressources|Lien|
|---|---|
|Site web de l’Australian Cyber Security Centre|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>Réponse aux incidents

La génération des journaux appropriés, leur collecte dans des référentiels centralisés et l’exécution d’analyses, améliorent la compréhension des systèmes et fournissent des mécanismes permettant de détecter les incidents de cybersécurité. Une fois les incidents ou les événements détectés, l’étape suivante consiste à réagir à ces événements et à effectuer des actions pour maintenir l’intégrité du système et protéger les services et les données de toute compromission. Azure fournit une combinaison de services pour répondre efficacement à tous les événements qui se produisent.

### <a name="azure-alerts"></a>Alertes Azure

Les alertes Azure peuvent être utilisées pour notifier le personnel de soutien et de sécurité en réponse à des événements particuliers. Cela permet à une entité du Commonwealth de répondre de manière proactive à la détection des événements pertinents détectés par les services d’analyse mentionnés dans cet article.

|Ressources|Lien|
|---|---|
|Vue d’ensemble des alertes dans Microsoft Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)|
|Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)|
|Répondre aux événements avec les alertes Azure Monitor|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)|
|

### <a name="azure-automation"></a>Azure Automation

Azure Automation permet aux entités du Commonwealth de déclencher des actions en réponse à des événements. Il peut s’agir de démarrer une capture de paquets sur des machines virtuelles, d’exécuter un flux de travail, d’arrêter ou de démarrer des machines virtuelles ou des services, ou une série d’autres tâches. L’automation permet de répondre rapidement aux alertes sans intervention manuelle, réduisant ainsi le temps de réponse et la gravité d’un incident ou d’un événement.

|Ressources|Lien|
|---|---|
|Documentation Azure Automation|[https://docs.microsoft.com/azure/automation](https://docs.microsoft.com/azure/automation)|
|Guide pratique : Utiliser une alerte pour déclencher un runbook Azure Automation|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)|
|

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article sur [L’administration à distance sécurisée des passerelles](gateway-secure-remote-administration.md) pour plus d’informations sur la gestion sécurisée de votre environnement de passerelle dans Azure.
