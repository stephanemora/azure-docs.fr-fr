---
title: Surveiller les performances du cluster AKS avec Azure Monitor pour les conteneurs | Microsoft Docs
description: Cet article décrit comment afficher et analyser les données de performances et de journal avec Azure Monitor pour les conteneurs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: bbfc8cc61571de8b76ef1f7f0216501ef6d2cdee
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377471"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Comprendre les performances du cluster AKS avec Azure Monitor pour les conteneurs 
Avec Azure Monitor pour les conteneurs, vous pouvez utiliser les graphiques de performances et l’état d’intégrité pour surveiller la charge de travail de vos clusters Azure Kubernetes Service (AKS) selon deux perspectives, directement à partir d’un cluster AKS ou tous les clusters AKS d’un abonnement à partir d’Azure Monitor. L’affichage d’ACI (Azure Container Instances) est également possible lorsque vous surveillez un cluster ACS spécifique.

Cet article vous aidera à comprendre l’expérience entre les deux perspectives et à rapidement évaluer, examiner et résoudre les problèmes détectés.

Pour plus d’informations sur l’activation d’Azure Monitor pour les conteneurs, consultez [Onboard Azure Monitor for containers](container-insights-onboard.md) (Intégrer Azure Monitor pour les conteneurs).

Azure Monitor fournit une vue multicluster affichant l’état d’intégrité de tous les clusters AKS surveillés exécutant Linux et Windows Server 2019, déployés dans les groupes de ressources de vos abonnements.  Il montre les clusters AKS découverts et qui ne sont pas surveillés par la solution. Immédiatement, vous pouvez comprendre l’intégrité du cluster et à partir de là, vous pouvez explorer le nœud et la page de performances du contrôleur, ou naviguer pour voir les graphiques de performances du cluster.  Pour les clusters AKS détectés et identifiés comme non surveillés, vous pouvez activer la surveillance de ces clusters à tout moment.  

Les principales différences entre la surveillance d’un cluster Windows Server avec Azure Monitor pour conteneurs et celle d’un cluster Linux sont les suivantes :

- La métrique Mémoire RSS n’est pas disponible pour le nœud et les conteneurs Windows.
- Les informations de capacité de stockage des disques ne sont pas disponibles pour les nœuds Windows.
- La prise en charge des journaux d’activité dynamiques est disponible à l’exception des fichiers journaux de conteneurs Windows.
- Seuls les environnements de pod sont surveillés, pas les environnements Docker.
- Avec la préversion, un maximum de 30 conteneurs Windows Server sont pris en charge. Cette limitation ne s’applique pas aux conteneurs Linux.  

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Vue multicluster à partir d’Azure Monitor

Pour afficher l’état d’intégrité de tous les clusters AKS déployés, sélectionnez **Surveiller** dans le volet de gauche du portail Azure.  Sous la section **Insights**, sélectionnez **Conteneurs**.  

![Exemple de tableau de bord multicluster dans Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Dans l’onglet **Monitored clusters** (Clusters surveillés), vous découvrez les informations suivantes :

1. Combien de clusters se trouvent dans un état critique ou non intègre, et combien sont sains ou ne rapportent aucune donnée (état inconnu).
2. Est-ce que tous mes déploiements [Azure Kubernetes Engine (AKS-engine)](https://github.com/Azure/aks-engine) sont intègres  ?
3. Combien de nœuds et de pods utilisateur et système sont-ils déployés par cluster ?
4. Quelle quantité d’espace disque est-il disponible et y a-t-il un problème de capacité ?

Les états d’intégrité inclus sont : 

* **Sain** : aucun problème détecté pour la machine virtuelle et le fonctionnement répond aux attentes. 
* **Critique** : un ou plusieurs problèmes critiques ont été détectés et doivent être corrigés afin de restaurer l’état opérationnel normal attendu.
* **Avertissement** : un ou plusieurs problèmes ont été détectés et doivent être corrigés pour éviter que la condition d’intégrité ne devienne critique.
* **Inconnu** : si le service n’a pas pu établir de connexion avec le nœud ou le pod, l’état passe à Inconnu.
* **Introuvable** : l’espace de travail, le groupe de ressources ou l’abonnement contenant l’espace de travail pour cette solution a été supprimé.
* **Non autorisé** : l’utilisateur ne dispose pas des autorisations requises pour lire les données dans l’espace de travail.
* **Erreur** : une erreur s’est produite lors de la tentative de lecture à partir de l’espace de travail.
* **Mal configuré** : Azure Monitor pour les conteneurs n’a pas été configuré correctement dans l’espace de travail spécifié.
* **Aucune donnée** : aucune donnée n’a été signalée à l’espace de travail au cours des 30 dernières minutes.

L’état d’intégrité calcule l’état global des clusters en fonction du *pire des* trois états à une exception près : si l’un des trois états est *inconnu*, l’état global des clusters affiche **Inconnu**.  

Le tableau suivant fournit une répartition du calcul pour le contrôle des états d’intégrité pour un cluster surveillé sur la vue multicluster.

| |Statut |Disponibilité |  
|-------|-------|-----------------|  
|**Pod utilisateur**| | |  
| |Healthy |100 % |  
| |Avertissement |90 - 99 % |  
| |Critique |< 90 % |  
| |Unknown |Si non signalé dans les 30 dernières minutes |  
|**Pod système**| | |  
| |Healthy |100 % |
| |Avertissement |N/A |
| |Critique |< 100 % |
| |Unknown |Si non signalé dans les 30 dernières minutes |
|**Node** | | |
| |Healthy |> 85 % |
| |Avertissement |60 - 84 % |
| |Critique |< 60 % |
| |Unknown |Si non signalé dans les 30 dernières minutes |

Dans la liste des clusters, vous pouvez explorer la page **Cluster** en cliquant sur le nom du cluster, la page de performances **Nœuds** en cliquant sur le cumul des nœuds dans la colonne **Nœuds** de ce cluster spécifique, ou la page de performances **Contrôleurs** en cliquant sur le cumul de la colonne **Pods utilisateur** ou **Pods système**.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Afficher les performances directement à partir d’un cluster AKS

L’accès à Azure Monitor pour les conteneurs est disponible directement à partir d’un cluster AKS en sélectionnant **Insights** dans le volet gauche. L’affichage des informations sur votre cluster AKS se présente sous quatre perspectives :

- Cluster
- Nœuds 
- Controllers  
- Containers

La page qui s’ouvre par défaut lorsque vous cliquez sur **Insights** est **Cluster** : elle inclut quatre graphiques en courbes des performances affichant les mesures de performances clés de votre cluster. 

![Exemples de graphiques de performances sous l’onglet Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Le graphique de performances affiche quatre métriques de performance :

- **Utilisation de l’UC du nœud&nbsp;%** : vue agrégée de l’utilisation du processeur pour le cluster entier. Vous pouvez filtrer les résultats pour l’intervalle de temps en sélectionnant une ou plusieurs des options **Avg** (Moy), **Min**, **Max**, **50th** (50e), **90th** (90e) et **95th**  (95e) du sélecteur de centiles. 
- **Utilisation de mémoire du nœud&nbsp;%** : vue agrégée de l’utilisation de la mémoire pour le cluster entier. Vous pouvez filtrer les résultats pour l’intervalle de temps en sélectionnant une ou plusieurs des options **Avg** (Moy), **Min**, **Max**, **50th** (50e), **90th** (90e) et **95th**  (95e) du sélecteur de centiles. 
- **Nombre de nœuds** : nombre et état des nœuds fournis par Kubernetes. L’état des nœuds de cluster représentés sont *All* (Tous), *Ready* (Prêts) et *Not Ready* (Non prêts). Il est possible de les filtrer individuellement ou en les combinant à l’aide du sélecteur situé au-dessus du graphique. 
- **Nombre de pods d’activité** : nombre et état des pods fournis par Kubernetes. L’état des pods représentés sont *All* (Tous), *Pending* (En attente), *Running* (En cours d’exécution) et *Unknown* (Inconnus). Il est possible de les filtrer individuellement ou en les combinant à l’aide du sélecteur situé au-dessus du graphique. 

Vous pouvez utiliser les touches de direction gauche/droite pour parcourir chaque point de données sur le graphe, et haut/bas pour faire défiler les lignes de centile. En cliquant sur l’icône d’épingle dans le coin supérieur droit de l’un des graphiques, vous épinglez le graphique sélectionné au dernier tableau de bord Azure que vous avez consulté. À partir du tableau de bord, vous pouvez redimensionner et repositionner le graphique. En sélectionnant le graphique depuis le tableau de bord, vous allez être redirigé vers Azure Monitor pour conteneurs, et charger l’étendue et la vue appropriées.

Azure Monitor pour conteneurs prend également en charge Azure Monitor [Metrics Explorer](../platform/metrics-getting-started.md), où vous pouvez tracer vos propres graphiques, mettre en corrélation et examiner les tendances, et épingler aux tableaux de bord. Dans Metrics Explorer, vous pouvez également utiliser les critères que vous avez définis pour visualiser vos métriques en tant que base d’une [règle d’alerte basée sur une métrique](../platform/alerts-metric.md).  

## <a name="view-container-metrics-in-metrics-explorer"></a>Afficher les métriques du conteneur dans Metrics Explorer

Dans Metrics Explorer, vous pouvez afficher les métriques agrégées d’utilisation de pod et de nœud d’Azure Monitor pour conteneurs. Le tableau suivant récapitule les détails pour vous aider à comprendre comment utiliser les graphiques de métrique pour visualiser les métriques des conteneurs.

|Espace de noms | Métrique |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| insights.container/pods | |
| | PodCount |

Vous pouvez appliquer le [fractionnement](../platform/metrics-charts.md#apply-splitting-to-a-chart) d’une métrique pour l’afficher par dimension et comparer différents segments de celui-ci. Pour un nœud, vous pouvez segmenter le graphique par la dimension *hôte* et, à partir d’un pod, vous pouvez le segmenter par les dimensions suivantes :

* Controller
* Espace de noms Kubernetes
* Nœud
* Phase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analyser l’intégrité des conteneurs, des contrôleurs et des nœuds

Quand vous passez à l’onglet **Nodes** (Nœuds), **Controllers** (Contrôleurs) et **Containers** (Conteneurs), le volet des propriétés s’affiche automatiquement à droite de la page. Il présente les propriétés de l’élément sélectionné, notamment les étiquettes que vous définissez pour organiser les objets Kubernetes. Lorsqu’un nœud Linux est sélectionné, il apparaît également sous la section **Capacité du disque Local**, qui représente l’espace disque disponible et le pourcentage utilisé pour chaque disque présenté au nœud. Cliquez sur le lien **>>** dans le volet pour afficher ou masquer le volet. 

![Exemple du volet de propriétés des perspectives Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Quand vous développez les objets dans la hiérarchie, le volet des propriétés est mis à jour en fonction de l’objet sélectionné. Dans le volet, vous pouvez également afficher les événements Kubernetes avec des recherches dans les journaux d’activité prédéfinies en cliquant sur le lien **Afficher les journaux des événements Kubernetes** en haut du volet. Pour plus d’informations sur l’affichage des données des journaux d’activité Kubernetes, consultez [Rechercher dans les journaux d’activité pour analyser les données](container-insights-log-search.md). Lorsque vous consultez les ressources de cluster, vous pouvez voir les journaux d’activité de conteneurs et les événements en temps réel. Pour plus d’informations sur cette fonctionnalité et la configuration requise pour accorder et contrôler les accès, consultez [Comment afficher les journaux d’activité en temps réel avec Azure Monitor pour conteneurs](container-insights-live-logs.md). 

Utilisez l’option **+ Ajouter un filtre** en haut de la page pour filtrer les résultats de la vue par **Service**, **Nœud** ou **Espace de noms** ou **Pool de nœuds**, et après la sélection de l’étendue de filtre, sélectionnez l’une des valeurs indiquées dans le champ **Sélectionner une ou plusieurs valeurs**.  Une fois que le filtre est configuré, il est appliqué globalement lors de l’affichage de n’importe quel point de vue du cluster AKS.  La formule prend uniquement en charge le signe égal.  Vous pouvez ajouter des filtres supplémentaires au-dessus du premier pour affiner davantage vos résultats.  Par exemple, si vous avez spécifié un filtre par **Nœud**, votre deuxième filtre vous permet uniquement de sélectionner **Service** ou **Espace de noms**.  

![Exemple utilisant le filtre pour affiner les résultats](./media/container-insights-analyze/add-filter-option-01.png)

La spécification d’un filtre dans un onglet continue d’être appliquée quand vous en sélectionnez un autre, et il est supprimé lorsque vous cliquez sur le symbole **x** en regard du filtre spécifié.   

Sous l’onglet **Nodes** (Nœuds), la hiérarchie de ligne suit le modèle d’objet Kubernetes commençant par un nœud dans votre cluster. Développez le nœud pour voir les pods qui y sont actuellement exécutés. Si plusieurs conteneurs sont regroupés dans un pod, ils sont affichés comme la dernière ligne de la hiérarchie. Vous pouvez également voir le nombre de charges de travail non associées à un pod qui sont actuellement exécutées sur l’ordinateur hôte si le processeur ou la mémoire de l’hôte sont très sollicités.

![Exemple de hiérarchie de nœud Kubernetes dans l’affichage des performances](./media/container-insights-analyze/containers-nodes-view.png)

Les conteneurs Windows Server qui exécutent le système d’exploitation Windows Server 2019 sont affichés après tous les nœuds basés sur Linux dans la liste. Lorsque vous développez un nœud Windows Server, vous pouvez afficher un ou plusieurs pods et conteneurs en cours d’exécution sur le nœud. Lorsqu’un nœud est sélectionné, le volet Propriétés affiche les informations de version, à l’exclusion des informations d’agent, dans la mesure où les nœuds Windows Server n’ont pas d’agent installé.  

![Exemple de hiérarchie de nœud avec les nœuds Windows Server répertoriés](./media/container-insights-analyze/nodes-view-windows.png) 

Les nœuds virtuels Azure Container Instances exécutant le système d’exploitation Linux sont affichés après le dernier nœud de cluster AKS sur la liste.  Lorsque vous développez un nœud virtuel ACI, vous pouvez afficher un ou plusieurs pods et conteneurs d’ACI en cours d’exécution sur le nœud.  Les métriques ne sont pas collectées et signalées pour les nœuds, uniquement pour les pods.

![Exemple de hiérarchie de nœud avec les instances de conteneur répertoriées](./media/container-insights-analyze/nodes-view-aci.png)

À partir d’un nœud développé, vous pouvez explorer le pod ou le conteneur exécuté sur le nœud jusqu’au contrôleur pour afficher les données de performances filtrées pour ce contrôleur. Cliquez sur la valeur sous la colonne **Contrôleur** du nœud spécifique.   
![Exemple de zoom depuis le nœud jusqu’au contrôleur dans l’affichage des performances](./media/container-insights-analyze/drill-down-node-controller.png)

Vous pouvez sélectionner des contrôleurs ou des conteneurs en haut de la page, puis examiner l’état et l’utilisation des ressources pour ces objets.  Si vous préférez afficher l’utilisation de la mémoire, dans la liste déroulante **Metric** (Métrique), sélectionnez **Memory RSS** (Mémoire RSS) ou **Memory working set** (Plage de travail de la mémoire). L’option **Memory RSS** (Mémoire RSS) est uniquement prise en charge par Kubernetes 1.8 et versions ultérieures. Sinon, vous pouvez afficher les valeurs **Min&nbsp;%** sous la forme *NaN&nbsp;%* , qui est une valeur de type données numérique représentant une valeur non définie ou non représentable.

La plage de travail de la mémoire montre la mémoire résidente et la mémoire virtuelle (cache) incluses, et représente le total de ce que l’application utilise. Mémoire RSS montre seulement la mémoire principale, qui est la mémoire résidente. Cette métrique montre la capacité réelle de la mémoire disponible.

![Affichage des performances des nœuds d’un conteneur](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Par défaut, les données de performances sont basées sur les six dernières heures, mais vous pouvez modifier la fenêtre à l’aide de l’option **Plage horaire** située en haut à gauche. Vous pouvez également filtrer les résultats dans l’intervalle de temps en sélectionnant les options du sélecteur de centiles **Avg** (Moy), **Min**, **Max**, **50th** (50e), **90th** (90e) et **95th** (95e). 

![Sélection de centile pour le filtrage des données](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Lorsque vous placez la souris sur le graphique à barres sous la colonne **Tendance**, chaque barre indique l’utilisation du processeur ou de la mémoire, selon la métrique sélectionnée, au sein d’une période d’échantillonnage de 15 minutes. Après avoir sélectionné le graphique de tendances à l’aide d’un clavier, vous pouvez utiliser les touches Alt + Pg. préc ou Alt + Pg. suiv pour parcourir chaque barre individuellement et obtenir les mêmes détails, comme vous le feriez sur mouseover.

![Exemple de pointage sur le graphique à barres](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

Dans l’exemple suivant, remarquez que, pour le premier nœud de la liste *aks-nodepool1-* , la valeur de **Containers** (Conteneurs) est 9, soit le cumul du nombre de conteneurs déployés.

![Cumul des conteneurs pour l’exemple de nœud](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Cela peut vous aider à déterminer rapidement si le nombre de conteneurs est bien réparti entre les nœuds de votre cluster. 

Les informations qui sont affichées dans les nœuds sont décrites dans le tableau suivant :

| Colonne | Description | 
|--------|-------------|
| NOM | Nom de l’hôte. |
| Statut | Vue Kubernetes de l’état du nœud. |
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% | Pourcentage moyen du nœud basé sur le centile durant la période spécifiée. |
| Avg, Min, Max, 50th, 90th | Valeur réelle moyenne des nœuds basée sur le centile durant la période spécifiée. La valeur moyenne est mesurée à partir de la limite de processeur/mémoire définie pour un nœud. Pour les pods et les conteneurs, il s’agit de la valeur moyenne indiquée par l’hôte. |
| Containers | Nombre de conteneurs. |
| Uptime | Indique le temps écoulé depuis le démarrage ou le redémarrage d’un nœud. |
| Controllers | Uniquement pour les conteneurs et les pods. Indique le contrôleur dans lequel il réside. Les pods ne figurent pas tous dans un contrôleur, certains peuvent donc afficher la mention **N/A** (non applicable). | 
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% | La tendance du graphique à barres indique la métrique de centile en pourcentage du contrôleur. |

Dans le sélecteur, sélectionnez **Controllers** (Contrôleurs).

![Sélectionner l’affichage des contrôleurs](./media/container-insights-analyze/containers-controllers-tab.png)

Ici, vous pouvez afficher l’intégrité des performances de vos contrôleurs ainsi que les contrôleurs de nœuds virtuels ACI ou les pods de nœuds virtuels non connectés à un contrôleur.

![Vue des performances des contrôleurs de \<Nom>](./media/container-insights-analyze/containers-controllers-view.png)

La hiérarchie de lignes commence par un contrôleur que vous développez pour afficher un ou plusieurs pods.  Développez un pod, et la dernière ligne affiche le conteneur lié au pod. À partir d’un contrôleur développé, vous pouvez explorer jusqu’au nœud en cours d’exécution pour afficher les données de performances filtrées pour ce nœud. Les pods ACI non connectés à un contrôleur sont répertoriés à la fin de la liste.

![Exemple de hiérarchie de contrôleurs avec les pods d’instances de conteneur répertoriés](./media/container-insights-analyze/controllers-view-aci.png)

Cliquez sur la valeur sous la colonne **Nœud** du contrôleur spécifique.   

![Exemple de zoom depuis le nœud jusqu’au contrôleur dans l’affichage des performances](./media/container-insights-analyze/drill-down-controller-node.png)

Les informations qui sont affichées dans les contrôleurs sont décrites dans le tableau suivant :

| Colonne | Description | 
|--------|-------------|
| NOM | Nom du contrôleur.|
| Statut | Statut du cumul des conteneurs lorsque l’exécution est terminée, par exemple, *OK*, *Terminated* (Terminé), *Failed* (Échec), *Stopped* (Arrêt) ou *Paused* (Pause). Si le conteneur est en cours d’exécution, mais que l’état n’est pas correctement affiché, ou n’a pas été identifié par l’agent et n’a pas répondu dans un délai de 30 minutes, l’état est *Unknown* (Inconnu). Des détails supplémentaires sur l’icône d’état sont fournies dans le tableau ci-dessous.|
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% | Moyenne cumulée du pourcentage moyen de chaque entité pour la métrique et le centile sélectionnés. |
| Avg, Min, Max, 50th, 90th  | Cumul de la performance moyenne du processeur ou de la mémoire du conteneur pour le centile sélectionné. La valeur moyenne est mesurée à partir de la limite Processeur/Mémoire définie pour un pod. |
| Containers | Nombre total de conteneurs pour le contrôleur ou pod. |
| Restarts | Cumul du nombre de redémarrages à partir des conteneurs. |
| Uptime | Indique le temps écoulé depuis le démarrage d’un conteneur. |
| Nœud | Uniquement pour les conteneurs et les pods. Indique le contrôleur. | 
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;%| La tendance du graphique à barres indique la métrique de centile du contrôleur. |

Les icônes dans le champ d’état indiquent le statut en ligne des conteneurs :
 
| Icône | Statut | 
|--------|-------------|
| ![Icône de statut en cours d’exécution prêt](./media/container-insights-analyze/containers-ready-icon.png) | Running (Ready) (En cours d’exécution (Prêt))|
| ![Icône de statut en attente ou en pause](./media/container-insights-analyze/containers-waiting-icon.png) | Waiting or Paused (En attente ou en pause)|
| ![Icône de statut de dernière exécution signalée](./media/container-insights-analyze/containers-grey-icon.png) | Last reported running but hasn’t responded more than 30 minutes (Dernière exécution signalée, mais pas de réponse depuis plus de 30 minutes)|
| ![Icône d’état de réussite](./media/container-insights-analyze/containers-green-icon.png) | Successfully stopped or failed to stop (Réussite ou échec de l’arrêt)|

L’icône d’état affiche un nombre basé sur ce que fournit le pod. Elle montre les deux pires états. Lorsque vous pointez sur un état, elle affiche l’état cumulé de tous les pods du conteneur. Si l’état n’est pas Prêt, il affiche **(0)** . 

Dans le sélecteur, sélectionnez **Containers** (Conteneurs).

![Sélectionner l’affichage des conteneurs](./media/container-insights-analyze/containers-containers-tab.png)

Ici, vous pouvez voir l’intégrité des performances de vos conteneurs Azure Kubernetes et Azure Container Instances.  

![Vue des performances des contrôleurs de \<Nom>](./media/container-insights-analyze/containers-containers-view.png)

À partir d’un conteneur, vous pouvez accéder à un pod ou un nœud pour afficher les données de performance filtrées pour cet objet. Cliquez sur la valeur sous la colonne **Pod** ou **Node** pour le conteneur.   

![Exemple de zoom depuis le nœud jusqu’au contrôleur dans l’affichage des performances](./media/container-insights-analyze/drill-down-controller-node.png)

Les informations qui sont affichées dans les conteneurs sont décrites dans le tableau suivant :

| Colonne | Description | 
|--------|-------------|
| NOM | Nom du contrôleur.|
| Statut | Statut des conteneurs, le cas échéant. Des détails supplémentaires sur l’icône d’état sont fournis dans le tableau ci-dessous.|
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% | Cumul du pourcentage moyen de chaque entité pour la métrique et le centile sélectionnés. |
| Avg, Min, Max, 50th, 90th  | Cumul de la performance moyenne du processeur ou de la mémoire du conteneur pour le centile sélectionné. La valeur moyenne est mesurée à partir de la limite Processeur/Mémoire définie pour un pod. |
| Pod | Conteneur dans lequel se trouve le pod.| 
| Nœud |  Nœud sur lequel se trouve le conteneur. | 
| Restarts | Indique le temps écoulé depuis le démarrage d’un conteneur. |
| Uptime | Indique le temps écoulé depuis le démarrage ou le redémarrage d’un conteneur. |
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% | La tendance du graphique à barres indique la métrique de centile en pourcentage du conteneur. |

Les icônes du champ d’état indiquent les états en ligne des pods, comme expliqué dans le tableau suivant :
 
| Icône | Statut |  
|--------|-------------|  
| ![Icône de statut en cours d’exécution prêt](./media/container-insights-analyze/containers-ready-icon.png) | Running (Ready) (En cours d’exécution (Prêt))|  
| ![Icône de statut en attente ou en pause](./media/container-insights-analyze/containers-waiting-icon.png) | Waiting or Paused (En attente ou en pause)|  
| ![Icône de statut de dernière exécution signalée](./media/container-insights-analyze/containers-grey-icon.png) | Last reported running but hasn’t responded more than 30 minutes (Dernière exécution signalée, mais pas de réponse depuis plus de 30 minutes)|  
| ![Icône de statut Terminé](./media/container-insights-analyze/containers-terminated-icon.png) | Successfully stopped or failed to stop (Réussite ou échec de l’arrêt)|  
| ![Icône de statut Échec](./media/container-insights-analyze/containers-failed-icon.png) | Statut Échec |  

## <a name="workbooks"></a>Workbooks

Les classeurs regroupent du texte, des  [requêtes de journal](../log-query/query-language.md), des [métriques](../platform/data-platform-metrics.md) et des paramètres sous la forme de rapports interactifs complets. Les classeurs sont modifiables par tous les membres de l’équipe ayant accès aux mêmes ressources Azure.

Azure Monitor pour les conteneurs inclut quatre classeurs pour vous aider à démarrer :

- **Capacité du disque** : Présente des graphiques interactifs d’utilisation du disque pour chaque disque présenté au nœud au sein d’un conteneur selon les perspectives suivantes :

    - Pourcentage d’utilisation de disque pour tous les disques
    - Espace disque disponible pour tous les disques
    - Grille indiquant pour chaque disque des nœuds, son pourcentage d’espace utilisé, sa tendance du pourcentage d’espace utilisé, l’espace disque disponible (Gio) et la tendance de l’espace disque disponible (Gio). Lorsqu’une ligne est sélectionnée dans la table, le pourcentage d’espace utilisé et l’espace disque disponible (Gio) sont indiqués ci-dessous 

- **E/S disque** : Présente des graphiques interactifs d’utilisation du disque pour chaque disque présenté au nœud au sein d’un conteneur selon les perspectives suivantes :

    - E/S disque totalisées pour tous les disques par octets lus/s, octets écrits/s, et octets lus et écrits/s 
    - Huit graphiques de performances montrant des indicateurs de performance clés pour aider à mesurer et à identifier les goulots d’étranglement des E/S disque.

- **Kubelet** : Comprend deux grilles qui montrent les statistiques clés de fonctionnement des nœuds :

    - Grille de vue d’ensemble par nœud indiquant le nombre total d’opérations, le nombre total d’erreurs et les opérations réussies en pourcentage et en tendance pour chaque nœud.
    - Vue d’ensemble par type d’opération montrant pour chaque opération le nombre total d’opérations, le nombre total d’erreurs et les opérations réussies en pourcentage et en tendance.

- **Réseau** : Présente des graphiques interactifs d’utilisation du réseau pour chaque carte réseau des nœuds, et une grille présentant les indicateurs de performance clés pour vous aider à mesurer les performances de vos cartes réseau.  

Vous accédez à ces classeurs en les sélectionnant dans la liste déroulante **Voir les classeurs**.  

![Liste déroulante Voir les classeurs](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Créer des alertes de performances avec Azure Monitor pour conteneurs](container-insights-alerts.md) pour découvrir comment créer des alertes pour une utilisation élevée du processeur et de la mémoire, afin de prendre en charge vos procédures et processus opérationnels ou DevOps. 

- Consultez les [exemples de requêtes de journal](container-insights-log-search.md#search-logs-to-analyze-data) pour voir les requêtes prédéfinies et des exemples permettant d’évaluer ou de personnaliser la génération d’alertes, la visualisation ou l’analyse de vos clusters.
