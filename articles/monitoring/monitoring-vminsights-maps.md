---
title: Guide pratique pour visualiser les dépendances d’application avec Azure Monitor pour machines virtuelles (préversion) | Microsoft Docs
description: Map est une fonctionnalité d’Azure Monitor pour les machines virtuelles qui détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Cet article fournit des informations sur son utilisation dans divers scénarios.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: ac96de2669a75a5966650afc0b49cc2f926537fa
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614206"
---
# <a name="using-azure-monitor-for-vms-preview-map-to-understand-application-components"></a>Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application
Lorsque vous visualisez les composants d’application détectés sur des machines virtuelles Windows et Linux s’exécutant dans Azure, vous pouvez observer votre environnement de deux manières avec Azure Monitor pour machines virtuelles : directement à partir d’une machine virtuelle ou à l’échelle des groupes de machines virtuelles à partir d’Azure Monitor. 

Cet article vous aide à comprendre l’expérience selon les deux perspectives et à utiliser la fonctionnalité Map. Pour plus d’informations sur la configuration d’Azure Monitor pour les machines virtuelles, consultez [Enable Azure Monitor for VMs ](monitoring-vminsights-onboard.md)(Activer Azure Monitor pour les machines virtuelles).

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="introduction-to-map-experience"></a>Présentation de l’expérience avec Map
Avant d’aborder l’affichage de Map pour une machine virtuelle unique ou un groupe de machines virtuelles, il est essentiel de procéder à une brève présentation de cette fonctionnalité, qui vous permettra de comprendre comment les informations sont présentées et ce que représentent les visualisations.  

Que vous sélectionniez la fonctionnalité Map directement à partir d’une machine virtuelle ou à partir d’Azure Monitor, elle offre une expérience similaire.  À partir d’Azure Monitor cependant, vous pouvez voir tous les membres d’un cluster ou d’une application multiniveau sur une seule carte.

Maps affiche les dépendances de machines virtuelles en détectant les processus en cours d’exécution avec des connexions réseau actives entre serveurs, la latence des connexions entrantes et sortantes et les ports sur n’importe quelle architecture connectée par TCP sur un intervalle de temps spécifié.  Le développement d’une machine virtuelle permet de visualiser les détails des processus. Seuls les processus qui communiquent avec la machine virtuelle sont affichés. Le nombre de clients frontaux qui se connectent à la machine virtuelle est indiqué avec le groupe de clients. Le nombre de serveurs principaux auxquels se connecte la machine virtuelle est indiqué avec les groupes de ports de serveur. Développez un groupe de ports de serveur pour afficher la liste détaillée des serveurs connectés sur ce port.  

Lorsque vous cliquez sur la machine virtuelle, le volet **Propriétés** se développe à droite. Il affiche les propriétés de l’élément sélectionné, par exemple les informations système rapportées par le système d’exploitation, les propriétés de la machine virtuelle Azure et un graphique en anneau synthétisant les connexions détectées. 

![Propriétés système de l’ordinateur](./media/monitoring-vminsights-maps/properties-pane-01.png)

Dans la partie droite du volet, cliquez sur l’icône **Journaux d’événements** pour afficher, dans le volet, une liste des tables envoyées à Log Analytics par les données collectées à partir de la machine virtuelle, disponible pour interrogation.  Lorsque vous cliquez sur l’un des types d’enregistrement répertoriés, la page **Journaux** s’affiche. Elle présente les résultats correspondant à ce type avec une requête préconfigurée filtrée selon la machine virtuelle concernée.  

![Liste de recherche dans les journaux dans le volet Propriétés](./media/monitoring-vminsights-maps/properties-pane-logs-01.png)

Fermez la page *Journaux** et revenez au volet **Propriétés**. Sélectionnez **Alertes** pour afficher les alertes déclenchées pour la machine virtuelle à partir de critères d’intégrité. Map intègre les alertes Azure pour afficher les alertes déclenchées pour le serveur sélectionné pendant l’intervalle de temps sélectionné. Le serveur affiche une icône s’il existe des alertes, et le volet Alertes de la machine répertorie les alertes. 

![Alertes de la machine dans le volet Propriétés](./media/monitoring-vminsights-maps/properties-pane-alerts-01.png)

Pour permettre à la fonctionnalité Map d’afficher les alertes pertinentes, créez une règle d’alerte qui se déclenche pour un ordinateur spécifique. Pour créer des alertes appropriées :
- Incluez une clause de regroupement par ordinateur (par exemple, **by Computer interval 1minute**).
- Choisissez d’alerter des mesures de métriques.

Pour plus d’informations sur les alertes Azure et la création de règles d’alerte, consultez [Alertes unifiées dans Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

L’option **Légende** dans le coin supérieur droit décrit les symboles et les rôles sur une carte.  Vous pouvez faire un zoom afin d’examiner une carte plus en détail et déplacer la carte. Les contrôles de zoom en bas à droite de la page définissent le niveau de zoom et permettent d’ajuster l’affichage selon la taille de page actuelle.  

## <a name="connection-metrics"></a>Métriques de connexion
Le volet **Connexions** affiche les métriques de connectivité standard pour la connexion sélectionnée de la machine virtuelle sur le port TCP. Les métriques comprennent le temps de réponse, le nombre de requêtes par minute, le débit du trafic et les liens.  

![Exemple de volet des graphiques de connectivité réseau](./media/monitoring-vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Connexions ayant échoué
Les connexions qui ont échoué sont affichées dans la carte des processus et machines, avec une ligne rouge en pointillés indiquant qu’un système client ne parvient pas à atteindre un processus ou un port. Les connexions ayant échoué sont signalées par n’importe quel système disposant de Dependency Agent si ce système est celui qui a tenté d’établir de telles connexions. Map mesure ce processus en observant les sockets TCP qui ne parviennent pas à établir une connexion. Cet échec peut être dû à un pare-feu, à une configuration incorrecte du client ou du serveur, ou à l’indisponibilité d’un service distant.

![Exemple de connexion ayant échoué sur la carte](./media/monitoring-vminsights-maps/map-group-failed-connection-01.png)

Le fait de comprendre les connexions ayant échoué peut faciliter le dépannage, la validation de la migration, l’analyse de la sécurité et la compréhension globale de l’architecture du service. Les échecs de connexion sont parfois sans incidence, mais ils dénotent souvent directement un problème tel qu’un environnement de basculement devenant subitement inaccessible ou deux couches Application incapables de communiquer entre elles après une migration vers le cloud.

### <a name="client-groups"></a>Groupes de clients
Les groupes de clients sur la carte représentent les machines clientes ayant des connexions avec la machine mappée. Un groupe de clients représente les clients d’un processus ou d’une machine.

![Exemples de groupes de clients sur la carte](./media/monitoring-vminsights-maps/map-group-client-groups-01.png)

Pour afficher les adresses IP et clients surveillés des systèmes d’un groupe de clients, sélectionnez le groupe. Le contenu du groupe est répertorié sous le groupe.  

![Exemple de liste d’adresses IP d’un groupe de clients sur la carte](./media/monitoring-vminsights-maps/map-group-client-group-iplist-01.png)

Si le groupe inclut des clients surveillés et non surveillés, vous pouvez sélectionner la section appropriée du graphique en anneau sur le groupe pour filtrer les clients.

### <a name="server-port-groups"></a>Groupes de ports du serveur
Les groupes de ports de serveur représentent les ports de serveur sur les serveurs ayant des connexions entrantes à partir de la machine mappée. Le groupe inclut le port du serveur ainsi que le nombre de serveurs connectés à ce port. Sélectionnez le groupe pour répertorier chaque serveur et chaque connexion. 

![Exemple de groupe de ports de serveur sur la carte](./media/monitoring-vminsights-maps/map-group-server-port-groups-01.png)  

Si le groupe inclut des serveurs surveillés et non surveillés, vous pouvez sélectionner la section appropriée du graphique en anneau sur le groupe pour filtrer les serveurs.

## <a name="view-map-directly-from-a-virtual-machine"></a>Afficher Map directement à partir d’une machine virtuelle 

Pour accéder à Azure Monitor pour les machines virtuelles directement à partir d’une machine virtuelle, procédez comme suit.

1. Dans le portail Azure, sélectionnez **Machines virtuelles**. 
2. Choisissez une machine virtuelle dans la liste, puis, dans la section **Surveillance**, sélectionnez **Insights (préversion)**.  
3. Sélectionnez l’onglet **Carte**.

Map affiche les dépendances de machines virtuelles, c’est-à-dire les processus et groupes de processus en cours d’exécution avec des connexions réseau actives, sur un intervalle de temps spécifié.  Par défaut, la carte affiche les 30 dernières minutes.  Le sélecteur **TimeRange** en haut à gauche permet d’effectuer des requêtes sur des intervalles de temps historiques jusqu’à une heure afin de voir l’aspect passé des dépendances (par exemple, pendant un incident ou avant une modification).  

![Vue d’ensemble de la carte de machine virtuelle directe](./media/monitoring-vminsights-maps/map-direct-vm-01.png)

## <a name="view-map-from-azure-monitor"></a>Afficher Map à partir d’Azure Monitor
À partir d’Azure Monitor, la fonctionnalité Map offre une vue globale de vos machines virtuelles et de leurs dépendances.  Pour accéder à la fonctionnalité Map à partir d’Azure Monitor, procédez comme suit. 

1. Dans le Portail Azure, sélectionnez **Surveiller**. 
2. Choisissez **Machines virtuelles (préversion)** dans la section **Insights**.
3. Sélectionnez l’onglet **Carte**.

![Vue d’ensemble de la carte de machines virtuelles multiples dans Azure Monitor](./media/monitoring-vminsights-maps/map-multivm-azure-monitor-01.png)

Dans le sélecteur **Espace de travail** en haut de la page, si vous avez plusieurs espaces de travail Log Analytics, choisissez celui qui est activé avec la solution et auquel des machines virtuelles envoient des rapports. Le sélecteur **Groupe** retourne les abonnements, les groupes de ressources, les [groupes d’ordinateurs](../log-analytics/log-analytics-computer-groups.md) et les groupes de machines virtuelles identiques associés à l’espace de travail sélectionné. Votre sélection s’applique seulement à la fonctionnalité Carte, et n’est pas reportée sur Performances ou Carte.

Par défaut, la carte affiche les 30 dernières minutes. Le sélecteur **TimeRange** permet d’effectuer des requêtes sur des intervalles de temps historiques jusqu’à une heure afin de voir l’aspect passé des dépendances (par exemple, pendant un incident ou avant une modification).   

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment utiliser la fonctionnalité d’intégrité, consultez [View Azure VM Health](monitoring-vminsights-health.md) (Afficher l’intégrité d’une machine virtuelle Azure). Pour identifier les goulots d’étranglement et les performances d’utilisation globales avec vos machines virtuelles, consultez [View Azure Monitor for VMs Performance](monitoring-vminsights-performance.md) (Examiner les performances avec Azure Monitor pour les machines virtuelles). 
