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
ms.date: 04/09/2019
ms.author: magoedte
ms.openlocfilehash: 3261c2389a9706537366bcd60e00517bbcfb5f48
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426390"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Comprendre les performances du cluster AKS avec Azure Monitor pour les conteneurs 
Avec Azure Monitor pour les conteneurs, vous pouvez utiliser les graphiques de performances et l’état d’intégrité pour surveiller la charge de travail de vos clusters Azure Kubernetes Service (AKS) selon deux perspectives, directement à partir d’un cluster AKS ou tous les clusters AKS d’un abonnement à partir d’Azure Monitor. L’affichage d’ACI (Azure Container Instances) est également possible lorsque vous surveillez un cluster ACS spécifique.

Cet article vous aidera à comprendre l’expérience entre les deux perspectives et à rapidement évaluer, examiner et résoudre les problèmes détectés.

Pour plus d’informations sur l’activation d’Azure Monitor pour les conteneurs, consultez [Onboard Azure Monitor for containers](container-insights-onboard.md) (Intégrer Azure Monitor pour les conteneurs).

Azure Monitor fournit une vue multicluster affichant l’état d’intégrité de tous les clusters AKS surveillés déployés dans les groupes de ressources de vos abonnements.  Il montre les clusters AKS découverts et qui ne sont pas surveillés par la solution. Immédiatement, vous pouvez comprendre l’intégrité du cluster et à partir de là, vous pouvez explorer le nœud et la page de performances du contrôleur, ou naviguer pour voir les graphiques de performances du cluster.  Pour les clusters AKS détectés et identifiés comme non surveillés, vous pouvez activer la surveillance de ces clusters à tout moment.  

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [Portail Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Vue multicluster à partir d’Azure Monitor 
Pour afficher l’état d’intégrité de tous les clusters AKS déployés, sélectionnez **Surveiller** dans le volet de gauche du portail Azure.  Sous la section **Insights**, sélectionnez **Conteneurs**.  

![Exemple de tableau de bord multicluster dans Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview-1018.png)

Dans l’onglet **Monitored clusters** (Clusters surveillés), vous découvrez les informations suivantes :

1. Combien de clusters se trouvent dans un état critique ou non intègre, et combien sont sains ou ne rapportent aucune donnée (état inconnu).
1. Est-ce que tous mes déploiements [Azure Kubernetes Engine (AKS-engine)](https://github.com/Azure/aks-engine) sont intègres  ?
1. Combien de nœuds, et de pods utilisateur et système sont déployés par cluster.  

Les états d’intégrité inclus sont : 

* **Sain** : aucun problème détecté pour la machine virtuelle et le fonctionnement répond aux attentes. 
* **Critique** : un ou plusieurs problèmes critiques ont été détectés et doivent être corrigés afin de restaurer l’état opérationnel normal attendu.
* **Avertissement** : un ou plusieurs problèmes ont été détectés et doivent être corrigés pour éviter que la condition d’intégrité ne devienne critique.
* **Inconnu** : si le service n’a pas pu établir de connexion avec le nœud ou le pod, l’état passe à Inconnu.
* **Introuvable** : l’espace de travail, le groupe de ressources ou l’abonnement contenant l’espace de travail pour cette solution a été supprimé.
* **Non autorisé** : l’utilisateur ne dispose pas des autorisations requises pour lire les données dans l’espace de travail.
* **Erreur** : une erreur s’est produite lors de la tentative de lecture à partir de l’espace de travail.
* **Mal configuré** : Azure Monitor pour les conteneurs n’a pas été configuré correctement dans l’espace de travail spécifié.
* **Aucune donnée** : aucune donnée n’a été signalée à l’espace de travail au cours des 30 dernières minutes.

L’état d’intégrité calcule l’état global des clusters en fonction du *pire des* trois états à une exception près : si l’un des trois états est *inconnu*, l’état global des clusters affiche **Inconnu**.  

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
| |Avertissement |S.O. |
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

Vous pouvez utiliser les touches de direction gauche/droite pour parcourir chaque point de données sur le graphe, et haut/bas pour faire défiler les lignes de centile.

Azure Monitor pour les conteneurs prend également en charge Azure Monitor [Explorateur de mesures](../platform/metrics-getting-started.md), où vous pouvez créer votre propre traçage des graphiques, mettre en corrélation et examiner les tendances et épingler aux tableaux de bord. Dans metrics explorer, vous pouvez également utiliser les critères que vous avez défini pour visualiser vos mesures comme base d’un [mesure en fonction de règle d’alerte](../platform/alerts-metric.md).  

## <a name="view-container-metrics-in-metrics-explorer"></a>Afficher les métriques de conteneur dans metrics explorer
Dans metrics explorer, vous pouvez afficher les nœud agrégée et pod des métriques d’utilisation d’Azure Monitor pour les conteneurs. Le tableau suivant récapitule les détails pour vous aider à comprendre comment utiliser les graphiques de métrique pour visualiser les métriques de conteneur.

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

Vous pouvez appliquer [fractionnement](../platform/metrics-charts.md#apply-splitting-to-a-chart) d’une métrique à afficher par dimension et de visualiser comment les différents segments de celui-ci comparer entre eux. Pour un nœud, vous pouvez segmenter le graphique par le *hôte* dimension, et à partir d’un pod vous pouvez le segmenter par les dimensions suivantes :

* Controller
* Espace de noms Kubernetes
* Nœud
* Phase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analyser l’intégrité des conteneurs, les contrôleurs et les nœuds

Quand vous passez à l’onglet **Nodes** (Nœuds), **Controllers** (Contrôleurs) et **Containers** (Conteneurs), le volet des propriétés s’affiche automatiquement à droite de la page.  Il montre les propriétés de l’élément sélectionné, notamment les étiquettes que vous définissez pour organiser les objets Kubernetes. Cliquez sur le lien **>>** dans le volet pour afficher ou masquer le volet.  

![Exemple du volet de propriétés des perspectives Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Quand vous développez les objets dans la hiérarchie, le volet des propriétés est mis à jour en fonction de l’objet sélectionné. Dans le volet, vous pouvez également afficher les événements Kubernetes avec des recherches dans les journaux d’activité prédéfinies en cliquant sur le lien **Afficher les journaux des événements Kubernetes** en haut du volet. Pour plus d’informations sur l’affichage des données des journaux d’activité Kubernetes, consultez [Rechercher dans les journaux d’activité pour analyser les données](#search-logs-to-analyze-data). Lorsque vous consultez vos conteneurs dans la vue **Conteneurs**, vous pouvez voir les journaux d’activité de conteneur en temps réel. Pour plus d’informations sur cette fonctionnalité et la configuration requise pour accorder et contrôler les accès, consultez [Comment afficher les journaux d’activité de conteneur en temps réel avec Azure Monitor pour les conteneurs](container-insights-live-logs.md). 

Utilisez l’option **+ Ajouter un filtre** en haut de la page pour filtrer les résultats de la vue par **Service**, **Nœud** ou **Espace de noms**, et après la sélection de l’étendue de filtre, sélectionnez l’une des valeurs indiquées dans le champ **Sélectionner une ou plusieurs valeurs**.  Une fois que le filtre est configuré, il est appliqué globalement lors de l’affichage de n’importe quel point de vue du cluster AKS.  La formule prend uniquement en charge le signe égal.  Vous pouvez ajouter des filtres supplémentaires au-dessus du premier pour affiner davantage vos résultats.  Par exemple, si vous avez spécifié un filtre par **Nœud**, votre deuxième filtre vous permet uniquement de sélectionner **Service** ou **Espace de noms**.  

![Exemple utilisant le filtre pour affiner les résultats](./media/container-insights-analyze/add-filter-option-01.png)

La spécification d’un filtre dans un onglet continue d’être appliquée quand vous en sélectionnez un autre, et il est supprimé lorsque vous cliquez sur le symbole **x** en regard du filtre spécifié.   

Sous l’onglet **Nodes** (Nœuds), la hiérarchie de ligne suit le modèle d’objet Kubernetes commençant par un nœud dans votre cluster. Développez le nœud pour voir les pods qui y sont actuellement exécutés. Si plusieurs conteneurs sont regroupés dans un pod, ils sont affichés comme la dernière ligne de la hiérarchie. Vous pouvez également voir le nombre de charges de travail non associées à un pod qui sont actuellement exécutées sur l’ordinateur hôte si le processeur ou la mémoire de l’hôte sont très sollicités.

![Exemple de hiérarchie de nœud Kubernetes dans l’affichage des performances](./media/container-insights-analyze/containers-nodes-view.png)

Les nœuds virtuels Azure Container Instances exécutant le système d’exploitation Linux sont affichés après le dernier nœud de cluster AKS sur la liste.  Lorsque vous développez un nœud virtuel ACI, vous pouvez afficher un ou plusieurs pods et conteneurs d’ACI en cours d’exécution sur le nœud.  Les métriques ne sont pas collectées et signalées pour les nœuds, uniquement pour les pods.

![Exemple de hiérarchie de nœud avec les instances de conteneur répertoriées](./media/container-insights-analyze/nodes-view-aci.png)

À partir d’un nœud développé, vous pouvez explorer le pod ou le conteneur exécuté sur le nœud jusqu’au contrôleur pour afficher les données de performances filtrées pour ce contrôleur. Cliquez sur la valeur sous la colonne **Contrôleur** du nœud spécifique.   
![Exemple de zoom depuis le nœud jusqu’au contrôleur dans l’affichage des performances](./media/container-insights-analyze/drill-down-node-controller.png)

Vous pouvez sélectionner des contrôleurs ou des conteneurs en haut de la page, puis examiner l’état et l’utilisation des ressources pour ces objets.  Si vous préférez afficher l’utilisation de la mémoire, dans la liste déroulante **Metric** (Métrique), sélectionnez **Memory RSS** (Mémoire RSS) ou **Memory working set** (Plage de travail de la mémoire). L’option **Memory RSS** (Mémoire RSS) est uniquement prise en charge par Kubernetes 1.8 et versions ultérieures. Sinon, vous pouvez afficher les valeurs **Min&nbsp;%** sous la forme *NaN&nbsp;%*, qui est une valeur de type données numérique représentant une valeur non définie ou non représentable. 

![Affichage des performances des nœuds d’un conteneur](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Par défaut, les données de performances sont basées sur les six dernières heures, mais vous pouvez modifier la fenêtre à l’aide de l’option **Plage horaire** située en haut à gauche. Vous pouvez également filtrer les résultats dans l’intervalle de temps en sélectionnant les options du sélecteur de centiles **Avg** (Moy), **Min**, **Max**, **50th** (50e), **90th** (90e) et **95th** (95e). 

![Sélection de centile pour le filtrage des données](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Lorsque vous placez la souris sur le graphique à barres sous la colonne **Tendance**, chaque barre indique l’utilisation du processeur ou de la mémoire, selon la métrique sélectionnée, au sein d’une période d’échantillonnage de 15 minutes. Après avoir sélectionné le graphique de tendances à l’aide d’un clavier, vous pouvez utiliser les touches Alt + Pg. préc ou Alt + Pg. suiv pour parcourir chaque barre individuellement et obtenir les mêmes détails, comme vous le feriez sur mouseover.

![Pointage de graphique à barres des tendances au fil de l’exemple](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

Dans l’exemple suivant, remarquez que, pour le premier nœud de la liste *aks-nodepool1-*, la valeur de **Containers** (Conteneurs) est 9, soit le cumul du nombre de conteneurs déployés.

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

![Affichage des performances des contrôleurs <nom>](./media/container-insights-analyze/containers-controllers-view.png)

La hiérarchie de lignes commence par un contrôleur que vous développez pour afficher un ou plusieurs pods.  Développez un pod, et la dernière ligne affiche le conteneur lié au pod. À partir d’un contrôleur développé, vous pouvez explorer jusqu’au nœud en cours d’exécution pour afficher les données de performances filtrées pour ce nœud. Les pods ACI non connectés à un contrôleur sont répertoriés à la fin de la liste.

![Exemple de hiérarchie de contrôleurs avec les pods d’instances de conteneur répertoriés](./media/container-insights-analyze/controllers-view-aci.png)

Cliquez sur la valeur sous la colonne **Nœud** du contrôleur spécifique.   

![Exemple de zoom depuis le nœud jusqu’au contrôleur dans l’affichage des performances](./media/container-insights-analyze/drill-down-controller-node.png)

Les informations qui sont affichées dans les contrôleurs sont décrites dans le tableau suivant :

| Colonne | Description | 
|--------|-------------|
| NOM | Nom du contrôleur.|
| Statut | Statut du cumul des conteneurs lorsque l’exécution est terminée, par exemple, *OK*, *Terminated* (Terminé), *Failed* (Échec), *Stopped* (Arrêt) ou *Paused* (Pause). Si le conteneur est en cours d’exécution, mais que l’état n’est pas correctement affiché, ou n’a pas été identifié par l’agent et n’a pas répondu dans un délai de 30 minutes, l’état est *Unknown* (Inconnu). Des détails supplémentaires sur l’icône d’état sont fournies dans le tableau ci-dessous.|
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% | Moyenne du pourcentage moyen de chaque entité pour la métrique sélectionnée et le centile de cumul. |
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

L’icône d’état affiche un nombre basé sur ce que fournit le pod. Elle montre les deux pires états. Lorsque vous pointez sur un état, elle affiche l’état cumulé de tous les pods du conteneur. Si l’état n’est pas Prêt, il affiche **(0)**. 

Dans le sélecteur, sélectionnez **Containers** (Conteneurs).

![Sélectionner l’affichage des conteneurs](./media/container-insights-analyze/containers-containers-tab.png)

Ici, vous pouvez voir l’intégrité des performances de vos conteneurs Azure Kubernetes et Azure Container Instances.  

![Affichage des performances des contrôleurs <nom>](./media/container-insights-analyze/containers-containers-view.png)

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


## <a name="container-data-collection-details"></a>Détails sur la collecte de données des conteneurs
Container Insights collecte diverses métriques de performances et données de journaux à partir des hôtes de conteneur et des conteneurs. Les données sont collectées toutes les trois minutes.

### <a name="container-records"></a>Enregistrements de conteneur

Le tableau suivant présente des exemples d’enregistrements collectés par Azure Monitor pour les conteneurs et les types de données qui s’affichent dans les résultats de recherche dans les journaux :

| Type de données | Type de données dans Recherche de journaux | Champs |
| --- | --- | --- |
| Performances des hôtes et des conteneurs | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventaire de conteneur | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventaire d’image de conteneur | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Journal de conteneur | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Journal de service de conteneur | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Inventaire du nœud de conteneur | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Processus de conteneur | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventaire des pods dans un cluster Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventaire de la partie des nœuds d’un cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Événements Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Services dans le cluster Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métriques de performances de la partie des nœuds du cluster Kubernetes | Perf &#124; where ObjectName == “K8SNode” | Computer, ObjectName, CounterName &#40;cpuUsageNanoCores, , memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métriques de performances de la partie des conteneurs du cluster Kubernetes | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Rechercher des journaux d’activité pour analyser les données
Log Analytics peut vous aider à rechercher des tendances, à diagnostiquer les goulets d’étranglement, à effectuer des prévisions, ou à mettre en corrélation des données afin de déterminer si la configuration actuelle du cluster garantit des performances optimales. Les recherches prédéfinies dans les journaux peuvent être utilisées immédiatement. Elles peuvent également être personnalisées afin de retourner les informations comme vous le souhaitez. 

Vous pouvez effectuer une analyse interactive des données dans l’espace de travail en sélectionnant l’option **View Kubernetes event logs** (Afficher les journaux des événements Kubernetes) ou **View container logs** (Afficher les journaux d’activité de conteneur) dans le volet de visualisation. La page **Recherche dans les journaux** s’affiche à droite de la page du portail Azure.

![Analyser des données dans Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Les sorties de journaux d’activité de conteneur transmises à Log Analytics sont STDOUT et STDERR. Étant donné qu’Azure Monitor surveille des Kubernetes managés Azure (AKS), le système Kube n’est pas collecté aujourd’hui en raison de l’important volume de données générées. 

### <a name="example-log-search-queries"></a>Exemples de requêtes de recherche de journal
Il est souvent utile de créer des requêtes en commençant par un exemple ou deux, puis en les modifiant afin de les adapter à vos besoins. Pour créer des requêtes plus avancées, vous pouvez utiliser les exemples de requêtes suivants :

| Requête | Description | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Liste de toutes les informations sur le cycle de vie d’un conteneur| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Événements Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventaire des images | 
| Sélectionnez l’option d’affichage de graphique **Courbes** :<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Processeur du conteneur | 
| Sélectionnez l’option d’affichage de graphique **Courbes** :<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Mémoire du conteneur |

## <a name="next-steps"></a>Étapes suivantes
Azure Monitor pour les conteneurs n’inclut pas d’ensemble prédéfini d’alertes à copier et modifier en fonction de vos processus et procédures de prise en charge. Pour apprendre à créer les alertes recommandées pour une utilisation élevée du processeur et de la mémoire, voir [Créer des alertes de performances avec Azure Monitor pour conteneurs](container-insights-alerts.md).  
