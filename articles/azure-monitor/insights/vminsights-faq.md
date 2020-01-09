---
title: Questions fréquentes sur Azure Monitor pour machines virtuelles (préversion) | Microsoft Docs
description: Réponses aux questions fréquentes sur Azure Monitor pour machines virtuelles qui supervise l’intégrité et les performances des machines virtuelles Azure, en plus de découvrir et de mapper automatiquement les composants d’application et leurs dépendances.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.openlocfilehash: fa47606112c562402a42bd5ca503ed2d9a311268
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400378"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Questions fréquentes (FAQ) sur Azure Monitor pour machines virtuelles (préversion)
Cette FAQ Microsoft est une liste de questions fréquemment posées au sujet d’Azure Monitor pour machines virtuelles (préversion). Si vous avez d’autres questions sur la solution, rendez-vous sur le [forum de discussion](https://feedback.azure.com/forums/34192--general-feedback) et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Puis-je intégrer un espace de travail existant ?
Si vos machines virtuelles sont déjà connectées à un espace de travail Log Analytics, vous pouvez continuer à l’utiliser lors de l’intégration à Azure Monitor pour machines virtuelles, à condition qu’il se trouve dans l’une des régions prises en charge énumérées [ici](vminsights-enable-overview.md#prerequisites).

Lors de l’intégration, nous configurons des compteurs de performance de l’espace de travail pour que toutes les machines virtuelles puissent commencer à collecter ces données afin de les afficher et de les analyser dans Azure Monitor pour machines virtuelles.  En conséquence, les données de performance de toutes les machines virtuelles connectées à l’espace de travail sélectionné s’affichent.  Les fonctionnalités Intégrité (Health) et Mappage (Map) ne sont activées que pour les machines virtuelles que vous avez choisi d’intégrer.

Pour en savoir plus sur les compteurs de performances activés, consultez notre article de [présentation de l’activation](vminsights-enable-overview.md#performance-counters-enabled).

## <a name="can-i-onboard-to-a-new-workspace"></a>Puis-je intégrer un nouvel espace de travail ? 
Si vos machines virtuelles ne sont pas actuellement connectées à un espace de travail Log Analytics existant, vous devez créer un nouvel espace de travail pour stocker vos données. La création d’un nouvel espace de travail par défaut se fait automatiquement lorsque vous configurez une seule machine virtuelle Azure pour Azure Monitor pour machines virtuelles via le portail Azure.

Si vous choisissez d’utiliser la méthode basée sur un script, ces étapes sont décrites dans l’article [Enable Azure Monitor for VMs (preview) using Azure PowerShell or Resource Manager template](vminsights-enable-at-scale-powershell.md) (Activer Azure Monitor pour machines virtuelles (préversion) à l’aide d’un modèle Azure PowerShell ou Resource Manager). 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Que dois-je faire si ma machine virtuelle envoie déjà des rapports à un espace de travail existant ?
Si vous collectez déjà des données à partir de vos machines virtuelles, il se peut que vous l’ayez déjà configuré de façon à ce que les rapports des données soient transmis à un espace de travail Log Analytics existant.  Si cet espace de travail se trouve dans l’une des régions que nous prenons en charge, vous pouvez y activer Azure Monitor pour machines virtuelles.  Si l’espace de travail que vous utilisez déjà ne se trouve pas dans l’une des régions que nous prenons en charge, vous ne pouvez effectuer l’intégration à Azure Monitor pour machines virtuelles pour l’instant.  Nous mettons tout en œuvre pour prendre en charge de nouvelles régions.

>[!NOTE]
>Nous configurons les compteurs de performance de l’espace de travail qui concerne toutes les machines virtuelles liées à l’espace de travail, que vous ayez choisi ou non de les intégrer à Azure Monitor pour machines virtuelles. Pour en savoir plus sur la configuration des compteurs de performance de l’espace de travail, consultez notre [documentation](../../azure-monitor/platform/data-sources-performance-counters.md). Pour en savoir plus sur les compteurs configurés d’Azure Monitor pour machines virtuelles, consultez notre article sur [l’activation d’Azure Monitor pour machines virtuelles](vminsights-enable-overview.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>Pourquoi l’intégration de ma machine virtuelle a-t-elle échoué ?
Voici les étapes effectuées lors de l’intégration d’une machine virtuelle Azure depuis le portail Azure :

* Un espace de travail Log Analytics par défaut est créé, si cette option a été sélectionnée.
* Les compteurs de performance sont configurés pour l’espace de travail sélectionné. Si cette étape échoue, certains graphiques et tableaux de performances n’affichent pas les données de la machine virtuelle que vous avez intégrée. Vous pouvez résoudre ce problème en exécutant le script PowerShell présenté [ici](vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* L’agent Log Analytics est installé sur les machines virtuelles Azure à l’aide d’une extension machine virtuelle, au besoin.  
* L’agent Map Dependency d’Azure Monitor pour machines virtuelles est installé sur les machines virtuelles Azure à l’aide d’une extension, au besoin.  
* Les composants d’Azure Monitor prenant en charge la fonctionnalité Intégrité (Health) sont configurés, le cas échéant, et la machine virtuelle est configurée de façon à transmettre les rapports sur les données d’intégrité.

Pendant le processus d’intégration, l’état d’avancement de chacun des éléments ci-dessus est vérifié afin de vous renvoyer un état de notification sur le portail. La configuration de l’espace de travail et l’installation de l’agent prennent généralement de 5 à 10 minutes. L’affichage des données de supervision et d’intégrité sur le portail prend de 5 à 10 minutes supplémentaires.  

Si vous avez commencé l’intégration et que des messages indiquant que la machine virtuelle doit être intégrée s’affichent, attendez jusqu’à 30 minutes pour que la machine virtuelle puisse terminer le processus. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Pourquoi toutes mes machines virtuelles sont-elles supervisées par la fonctionnalité Intégrité alors que j'ai juste activé Azure Monitor pour machines virtuelles ?
La fonctionnalité Intégrité est activée pour toutes les machines virtuelles connectées à l’espace de travail Log Analytics, même si l’action est initiée pour une seule et même machine virtuelle.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Est-il possible de modifier le planning lorsque des critères d'intégrité évaluent une condition ?
Non, cette version ne permet pas de modifier la période et la fréquence des critères d'intégrité. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Puis-je désactiver les critères d'intégrité pour une condition que je n'ai pas besoin de superviser ?
Cette version ne permet pas de désactiver les critères d'intégrité.

## <a name="are-the-health-alert-severities-configurable"></a>La gravité d'une alerte d'intégrité est-elle configurable ?  
La gravité d’une alerte d’intégrité n’est pas modifiable, elle ne peut qu’être activée ou désactivée. De plus, les niveaux de gravité de certaines alertes sont mis à jour en fonction de l'état du critère d'intégrité. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Si je reconfigure les paramètres d'un critère d'intégrité particulier, peut-il être étendu à une instance spécifique ?  
Si vous modifiez un paramètre d'une instance de critère d'intégrité, toutes les instances de critère d'intégrité du même type sont modifiées sur la machine virtuelle Azure. Par exemple, si le seuil de l’instance de critère d’intégrité de l’espace disque libre correspondant au disque logique C : est modifié, alors il s’applique à tous les autres disques logiques idco trouvés et supervisés de la même machine virtuelle.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>La fonctionnalité Intégrité supervise-t-elle les processeurs logiques et les cœurs ?
Non, sous Windows, les critères d'intégrité ne sont pas inclus au niveau des processeurs individuels et des processeurs logiques. Par défaut, seule l'utilisation totale de l'UC est supervisée afin d'évaluer la pression effective de l'UC en fonction du nombre total d'UC logiques disponibles sur la machine virtuelle Azure. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Tous les seuils des critères d'intégrité sont-ils configurables ?  
Les seuils des critères d’intégrité ciblant une machine virtuelle Windows ne peuvent pas être modifiés, car leurs états d’intégrité sont déjà définis sur *en cours d’exécution* ou *disponible*. Dans le cas d’une requête émise à partir de l’[API de supervision de charge de travail](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), l’état d’intégrité présente la valeur **LessThan** ou **GreaterThan** pour *comparisonOperator* avec un seuil (*threshold*) de **4** pour le service ou l’entité si :
   - Le service DNS Client Service Health n’est pas en cours d’exécution. 
   - Le service DHCP Client Service Health n’est pas en cours d’exécution. 
   - Le service RPC Service Health n’est pas en cours d’exécution. 
   - Le service Windows Firewall Service Health n’est pas en cours d’exécution.
   - Le service Windows Event Log Service Health n’est pas en cours d’exécution. 
   - Le service Server Service Health n’est pas en cours d’exécution. 
   - Le service Windows Remote Management Service Health n’est pas en cours d’exécution. 
   - Le disque logique est indisponible du fait d’une erreur ou de l’altération du système de fichiers.

Les seuils des critères d’intégrité Linux suivants ne peuvent pas être modifiés, car leur état d’intégrité est déjà défini sur *true*. Pour l’état d’intégrité, *comparisonOperator* indique **LessThan** avec un seuil (*threshold*) de **1** dans le cas d’une requête émise à partir de l’API de supervision de charge de travail pour l’entité en fonction de son contexte :
   - État du disque logique – Le disque logique n’est pas en ligne/disponible
   - État du disque – Le disque n’est pas en ligne/disponible
   - État de la carte réseau - La carte réseau est désactivée

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Comment modifier les alertes incluses avec la fonctionnalité Intégrité ?
Les règles d’alerte définies pour chaque critère d’intégrité ne sont pas affichées dans le portail Azure. Elles ne peuvent être activées ou désactivées qu’à partir de l’[API de supervision de charge de travail](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). En outre, il est impossible d'affecter un [groupe d'actions Azure Monitor](../../azure-monitor/platform/action-groups.md) pour des alertes d'intégrité sur le portail Azure. Vous pouvez utiliser l’API de paramètre de notification uniquement pour configurer le déclenchement d’un groupe d’actions chaque fois qu’une alerte d’intégrité est déclenchée. Actuellement, il est possible d’affecter des groupes d’action pour une machine virtuelle, si bien que toutes les *alertes d’intégrité* déclenchées sur la machine virtuelle déclenchent les mêmes groupes d’actions. Contrairement aux alertes Azure classiques, il ne peut pas exister un groupe d’action distinct pour chaque règle d’alerte d’intégrité. De plus, seuls les groupes d’actions configurés pour adresser des notifications par e-mail ou SMS sont pris en charge quand des alertes d’intégrité sont déclenchées. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Aucune donnée ne figure dans les tableaux de performances de ma machine virtuelle, ou certaines ne s’affichent pas
Si les données de performance ne s’affichent pas dans le tableau du disque ou dans certains graphiques de performance, vos compteurs de performance ne sont peut-être pas configurés dans l’espace de travail. Pour résoudre ce problème, exécutez le [script PowerShell](vminsights-enable-at-scale-powershell.md#enable-with-powershell) suivant.

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Quelle est la différence entre la fonctionnalité de mappage d’Azure Monitor pour machines virtuelles et celle de Service Map ?
La fonctionnalité de mappage d’Azure Monitor pour machines virtuelles est basée sur celle de Service Map, mais présente les différences suivantes :

* La fenêtre Mappage (Map) est accessible depuis le panneau de la machine virtuelle et depuis Azure Monitor pour machines virtuelles sous Azure Monitor.
* Il est maintenant possible de cliquer sur les connexions dans la fenêtre Mappage pour afficher les données métriques de connexion dans le panneau latéral de la connexion sélectionnée.
* Une nouvelle API permet de créer les mappages afin de mieux prendre en charge les plus complexes d’entre eux.
* Les machines virtuelles supervisées font maintenant partie du nœud du groupe de clients, et le graphique en secteurs montre la répartition entre les machines virtuelles supervisées et non supervisées dans ce groupe.  Il permet également de filtrer la liste des machines lorsque le groupe est développé.
* Les machines virtuelles supervisées font désormais partie des nœuds du groupe de ports du serveur, et le graphique en secteurs montre la répartition entre les machines supervisées et non supervisées dans ce groupe.  Il permet également de filtrer la liste des machines lorsque le groupe est développé.
* Le style de mappage a été mis à jour pour assurer une meilleure cohérence avec le mappage des applications (App Map) dans les insights des applications (Application insights).
* Les panneaux latéraux ont été mis à jour, mais ne disposent pas de l’ensemble d’intégrations complet pris en charge dans Service Map : Update Management, Change Tracking, Security et Service Desk. 
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

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Comment le temps de réponse des données stockées dans VMConnection est -il mesuré et affiché dans le volet de connexion et dans les classeurs ?

Ce temps de réponse est une approximation. Comme nous n’instrumentons pas le code de l’application, nous ne savons pas réellement à quel moment une requête débute et quand la réponse nous parvient. Au lieu de cela, nous observons les données envoyées sur une connexion, puis celles qui sont renvoyées sur cette connexion. Notre agent effectue le suivi de ces envois et réceptions et tente de les associer : une séquence d’envois suivie d’une séquence de réceptions est interprétée comme une paire requête/réponse. Le délai entre ces opérations correspond au temps de réponse. Il inclut la latence du réseau et le temps de traitement par le serveur.

Cette approximation fonctionne bien pour les protocoles basés sur les paires requête/réponse : une seule requête est émise sur la connexion, et une seule réponse nous parvient. Cela se produit pour le protocole HTTP(S) (sans traitement « pipeline »), mais non pour les autres.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Existe-t-il des limites avec l'utilisation du plan tarifaire Gratuit de Log Analytics ?
Si vous avez configuré Azure Monitor avec un espace de travail Log Analytics en utilisant le niveau tarifaire *Gratuit*, la fonctionnalité de mappage d’Azure Monitor pour machines virtuelles ne prend en charge que cinq machines connectées à l’espace de travail. Ainsi, si cinq machines virtuelles sont connectées à un espace de travail gratuit et que vous déconnectez l'une d'elle, puis connectez une nouvelle machine virtuelle, la nouvelle machine virtuelle n'est ni surveillée ni reflétée sur la page dédiée aux mappages.  

Dans ce cas, l'option **Essayer maintenant** apparaîtra lorsque vous ouvrirez la machine virtuelle et sélectionnerez **Insights (préversion)** dans le volet de gauche, même après son installation sur la machine virtuelle.  Mais aucune option ne vous est proposée, contrairement à ce qui aurait eu lieu si cette machine virtuelle n'avait pas été intégrée à Azure Monitor pour machines virtuelles. 

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir les exigences et les méthodes relatives à l’activation de la supervision de vos machines virtuelles, consultez l’article [Activer Azure Monitor pour une vue d’ensemble de machines virtuelles (version préliminaire)](vminsights-enable-overview.md).
