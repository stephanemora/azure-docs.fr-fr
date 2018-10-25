---
title: Azure Monitor pour machines virtuelles Forum aux questions | Microsoft Docs
description: Azure Monitor pour machines virtuelles est une solution d’Azure qui supervise à la fois l’intégrité et les performances du système d’exploitation de la machine virtuelle Azure. En outre, elle identifie automatiquement les composants des applications et leurs dépendances avec les autres ressources, et effectue le mappage de leurs communications respectives. Cet article apporte des réponses aux questions les plus fréquentes.
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
ms.date: 09/19/2018
ms.author: magoedte
ms.openlocfilehash: 282620342d2348868ceab5257de7415a9cb2147c
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388355"
---
# <a name="azure-monitor-for-vms-frequently-asked-questions"></a>Azure Monitor pour machines virtuelles Forum aux questions
Cette FAQ Microsoft consiste en une liste de questions fréquemment posées au sujet d’Azure Monitor pour machines virtuelles dans Microsoft Azure. Si vous avez d’autres questions sur la solution, rendez-vous sur le [forum de discussion](https://feedback.azure.com/forums/34192--general-feedback) et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Puis-je intégrer un espace de travail existant ?
Si vos machines virtuelles sont déjà connectées à un espace de travail Log Analytics, vous pouvez continuer à l’utiliser lors de l’intégration à Azure Monitor pour machines virtuelles, à condition qu’il se trouve dans l’une des régions prises en charge énumérées [ici](monitoring-vminsights-onboard.md#prerequisites).

Lors de l’intégration, nous configurons des compteurs de performance de l’espace de travail pour que toutes les machines virtuelles puissent commencer à collecter ces données afin de les afficher et de les analyser dans Azure Monitor pour machines virtuelles.  En conséquence, les données de performance de toutes les machines virtuelles connectées à l’espace de travail sélectionné s’affichent.  Les fonctionnalités Intégrité (Health) et Mappage (Map) ne sont activées que pour les machines virtuelles que vous avez choisi d’intégrer.

Pour en savoir plus sur les compteurs de performances activés, consultez notre article sur l’[intégration](monitoring-vminsights-onboard.md).

## <a name="can-i-onboard-to-a-new-workspace"></a>Puis-je intégrer un nouvel espace de travail ? 
Si vos machines virtuelles ne sont pas actuellement connectées à un espace de travail Log Analytics existant, vous devez créer un nouvel espace de travail pour stocker vos données.  La création d’un nouvel espace de travail par défaut se fait automatiquement lorsque vous configurez une seule machine virtuelle Azure pour Azure Monitor pour machines virtuelles via le portail Azure.

Si vous choisissez d’utiliser la méthode par script, ces étapes sont expliquées dans l’article sur l’[intégration](monitoring-vminsights-onboard.md). 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Que dois-je faire si ma machine virtuelle envoie déjà des rapports à un espace de travail existant ?
Si vous collectez déjà des données à partir de vos machines virtuelles, il se peut que vous l’ayez déjà configuré de façon à ce que les rapports des données soient transmis à un espace de travail Log Analytics existant.  Si cet espace de travail se trouve dans l’une des régions que nous prenons en charge, vous pouvez y activer Azure Monitor pour machines virtuelles.  Si l’espace de travail que vous utilisez déjà ne se trouve pas dans l’une des régions que nous prenons en charge, vous ne pouvez effectuer l’intégration à Azure Monitor pour machines virtuelles pour l’instant.  Nous mettons tout en œuvre pour prendre en charge de nouvelles régions.

>[!NOTE]
>Nous configurons les compteurs de performance de l’espace de travail qui concerne toutes les machines virtuelles liées à l’espace de travail, que vous ayez choisi ou non de les intégrer à Azure Monitor pour machines virtuelles. Pour en savoir plus sur la configuration des compteurs de performance de l’espace de travail, consultez notre [documentation](../log-analytics/log-analytics-data-sources-performance-counters.md). Pour en savoir plus sur les compteurs configurés d’Azure Monitor pour machines virtuelles, consultez notre [documentation sur l’intégration](monitoring-vminsights-onboard.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>Pourquoi l’intégration de ma machine virtuelle a-t-elle échoué ?
Voici les étapes effectuées lors de l’intégration d’une machine virtuelle Azure depuis le portail Azure :

* Un espace de travail Log Analytics par défaut est créé, si cette option a été sélectionnée.
* Les compteurs de performance sont configurés pour l’espace de travail sélectionné. Si cette étape échoue, certains graphiques et tableaux de performances n’affichent pas les données de la machine virtuelle que vous avez intégrée. Vous pouvez résoudre ce problème en exécutant le script PowerShell présenté [ici](monitoring-vminsights-onboard.md#enable-with-powershell).
* L’agent Log Analytics est installé sur les machines virtuelles Azure à l’aide d’une extension machine virtuelle, au besoin.  
* L’agent Map Dependency d’Azure Monitor pour machines virtuelles est installé sur les machines virtuelles Azure à l’aide d’une extension, au besoin.  
* Les composants d’Azure Monitor prenant en charge la fonctionnalité Intégrité (Health) sont configurés, le cas échéant, et la machine virtuelle est configurée de façon à transmettre les rapports sur les données d’intégrité.

Pendant le processus d’intégration, l’état d’avancement de chacun des éléments ci-dessus est vérifié afin de vous renvoyer un état de notification sur le portail.  La configuration de l’espace de travail et l’installation de l’agent prennent généralement de 5 à 10 minutes.  L’affichage des données de supervision et d’intégrité sur le portail prend de 5 à 10 minutes supplémentaires.  

Si vous avez commencé l’intégration et que des messages indiquant que la machine virtuelle doit être intégrée s’affichent, attendez jusqu’à 30 minutes pour que la machine virtuelle puisse terminer le processus. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Aucune donnée ne figure dans les tableaux de performances de ma machine virtuelle, ou certaines ne s’affichent pas
Si les données de performance ne s’affichent pas dans le tableau du disque ou dans certains graphiques de performance, vos compteurs de performance ne sont peut-être pas configurés dans l’espace de travail. Pour résoudre ce problème, exécutez le [script PowerShell](monitoring-vminsights-onboard.md#enable-with-powershell) suivant.

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Quelle est la différence entre la fonctionnalité de mappage d’Azure Monitor pour machines virtuelles et celle de Service Map ?
La fonctionnalité de mappage d’Azure Monitor pour machines virtuelles est basée sur celle de Service Map, mais présente les différences suivantes :

* La fenêtre Mappage (Map) est accessible depuis le panneau de la machine virtuelle et depuis Azure Monitor pour machines virtuelles sous Azure Monitor.
* Il est maintenant possible de cliquer sur les connexions dans la fenêtre Mappage pour afficher les données métriques de connexion dans le panneau latéral de la connexion sélectionnée.
* Une nouvelle API permet de créer les mappages afin de mieux prendre en charge les plus complexes d’entre eux.
* Les machines virtuelles supervisées font maintenant partie du nœud du groupe de clients, et le graphique en secteurs montre la répartition entre les machines virtuelles supervisées et non supervisées dans ce groupe.  Il permet également de filtrer la liste des machines lorsque le groupe est développé.
* Les machines virtuelles supervisées font désormais partie des nœuds du groupe de ports du serveur, et le graphique en secteurs montre la répartition entre les machines supervisées et non supervisées dans ce groupe.  Il permet également de filtrer la liste des machines lorsque le groupe est développé.
* Le style de mappage a été mis à jour pour assurer une meilleure cohérence avec le mappage des applications (App Map) dans les insights des applications (Application insights).
* Les panneaux latéraux ont été mis à jour, mais ne disposent pas encore de tout l’ensemble d’intégration pris en charge dans Service Map : Update Management, Change Tracking, Security et Service Desk. 
* L’option de sélection des groupes et des machines à mapper a été mise à jour et prend désormais en charge les abonnements, les groupes de ressources, les groupes de machines virtuelles identiques Azure et les services cloud.
* Vous ne pouvez pas créer de nouveaux groupes de machines Service Map dans la fonctionnalité de mappage d’Azure Monitor pour machines virtuelles.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Pourquoi des lignes pointillées apparaissent sur mes graphiques de performances ?

Cela peut se produire pour plusieurs raisons.  Lorsqu’il manque des données, les lignes sont marquées en pointillés.  Si vous avez modifié la fréquence d’échantillonnage des données sur les compteurs de performance activés (le réglage par défaut correspond à une collecte de données toutes les 60 secondes), des lignes en pointillés peuvent apparaître sur le graphique si vous choisissez une plage horaire courte et une fréquence inférieure à celle utilisée sur le graphique (par exemple, la fréquence d’échantillonnage est de 10 minutes et chacun de ses compartiments correspond à 5 minutes).  Si vous choisissez un plus grand intervalle de temps pour la visualisation, les lignes du graphique devraient apparaître sous forme de lignes pleines plutôt que de points dans ce cas.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Les groupes sont-ils pris en charge avec Azure Monitor pour machines virtuelles ?
Oui, une fois que vous installez Dependency Agent, nous collectons des informations depuis les machines virtuelles pour afficher les groupes en fonction de l’abonnement, du groupe de ressources, des groupes de machines virtuelles identiques et des services cloud.  Si vous utilisez Service Map et que vous avez créé des groupes d’ordinateurs, ceux-ci sont également affichés.  Les groupes d’ordinateurs apparaissent également dans le filtre de groupes si vous les avez créés pour l’espace de travail que vous affichez. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Comment faire pour voir les détails de ce qui détermine la ligne du 95e percentile dans les graphiques de performance globale ?
Par défaut, la liste est triée de façon à afficher les machines virtuelles ayant la valeur la plus élevée pour le 95e percentile de la métrique sélectionnée, à l’exception du graphique de la mémoire disponible qui affiche les machines ayant la valeur la plus faible pour le 5e percentile.  Si vous cliquez sur le graphique, la fenêtre **Top N List** s’ouvre avec la métrique appropriée sélectionnée.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Comment la fonctionnalité Map (Mappage) gère-t-elle les adresses IP dupliquées sur différents réseaux et sous-réseaux ?
Si vous dupliquez des plages d’adresses IP avec des machines virtuelles ou des groupes d’échelles de machines virtuelles identiques Azure sur des sous-réseaux et des réseaux virtuels, il se peut que la mappage d’Azure Monitor pour machines virtuelles affiche des informations erronées à la place. Il s’agit d’un problème connu et nous étudions des options pour offrir une meilleure expérience.

## <a name="does-map-feature-support-ipv6"></a>La fonctionnalité de mappage prend-elle en charge le protocole IPv6 ?
La fonctionnalité de mappage ne prend actuellement en charge que le protocole IPv4, mais nous étudions actuellement la prise en charge du protocole IPv6. Nous prenons également en charge le protocole IPv4 tunnelisé dans le protocole IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Lorsque je charge un mappage pour un groupe de ressources ou un autre grand groupe, celui-ci est difficile à visualiser
Bien que nous ayons apporté des améliorations au mappage pour la gestion de configurations grandes et complexes, nous savons qu’il peut comporter un grand nombre de nœuds, de connexions et de nœuds fonctionnant en cluster.  Nous continuons d’accroître notre soutien afin de renforcer la mise à l’échelle.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Pourquoi le graphique du réseau dans l’onglet Performance est-il différent de celui de la page d’aperçu de la machine virtuelle Azure ?

La page d’aperçu d’une machine virtuelle Azure affiche des graphiques basés sur la mesure de l’activité de l’hôte dans la machine virtuelle hôte.  Dans le cas du graphique du réseau sur l’aperçu de la machine virtuelle Azure, seul le trafic réseau qui sera facturé est affiché.  Le trafic entre réseaux virtuels n’est pas compris.  Les données et les graphiques présentés dans Azure Monitor pour les machines virtuelles sont basés sur les données de la machine virtuelle invitée et le graphique du réseau affiche tout le trafic TCP/IP entrant et sortant vers cette machine virtuelle, y compris le trafic entre réseaux virtuels.

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir les exigences et les méthodes relatives à l’activation de la supervision de vos machines virtuelles, consultez l’article [Intégrer Azure Monitor pour machines virtuelles](monitoring-vminsights-onboard.md).
