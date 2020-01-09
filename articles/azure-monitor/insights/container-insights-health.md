---
title: Surveiller l’intégrité du cluster Kubernetes avec Azure Monitor pour les conteneurs | Microsoft Docs
description: Cet article décrit comment afficher et analyser l’intégrité de vos clusters AKS et non AKS avec Azure Monitor pour les conteneurs.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 9ee710eb916923756633e65f3287751ba9a9dde3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405091"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Comprendre l’intégrité des clusters Kubernetes avec Azure Monitor pour les conteneurs

Azure Monitor pour les conteneurs permet de surveiller et signaler l’état d’intégrité des composants de l’infrastructure managée et de tous les nœuds exécutés sur n’importe quel cluster Kubernetes pris en charge par Azure Monitor pour les conteneurs. Cette expérience fournit davantage d’informations par rapport à l’état d’intégrité du cluster calculé et signalé sur l’[affichage multi-cluster](container-insights-analyze.md#multi-cluster-view-from-azure-monitor). À présent, vous pouvez déterminer si des nœuds du cluster présentent des contraintes de ressources, ou si un nœud ou un pod non disponible peut affecter une application en cours d’exécution dans le cluster grâce à des mesures organisées.

>[!NOTE]
>La fonctionnalité Intégrité est actuellement une fonctionnalité d’évaluation publique.
>

Pour plus d’informations sur la procédure d’activation d’Azure Monitor pour les conteneurs, consultez [Onboard Azure Monitor for containers](container-insights-onboard.md) (Intégrer Azure Monitor pour les conteneurs).

>[!NOTE]
>Pour prendre en charge les clusters du moteur AKS, vérifiez qu’elle répond aux critères suivants :
>- Elle utilise la version la plus récente du [client HELM](https://helm.sh/docs/using_helm/).
>- La version de l’agent en conteneur est *microsoft/oms:ciprod11012019*. Pour mettre à niveau l’agent, consultez [Mise à niveau de l’agent sur le cluster Kubernetes](container-insights-manage-agent.md#upgrading-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Vue d’ensemble

Dans Azure Monitor pour les conteneurs, la fonctionnalité (d’évaluation) Intégrité surveille de manière proactive l’intégrité de votre cluster Kubernetes pour vous aider à identifier et à diagnostiquer les problèmes. Il vous permet de voir les problèmes importants détectés. Les moniteurs qui évaluent l’intégrité de votre cluster s’exécutent sur l’agent conteneurisé dans votre cluster, tandis que les données d’intégrité sont écrites dans la table **KubeHealth** de votre espace de travail Log Analytics. 

L’intégrité du cluster Kubernetes est basée sur un certain nombre de scénarios de supervision organisés en fonction des abstractions et des objets Kubernetes suivants :

- Infrastructure Kubernetes : fournit un moniteur Rollup sur le serveur d’API Kubernetes, les ReplicaSet et les DaemonSet en cours d’exécution sur des nœuds déployés dans votre cluster en évaluant l’utilisation de l’UC et de la mémoire ainsi que la disponibilité d’un pod.

    ![Affichage du moniteur Rollup sur l’intégrité de l’infrastructure Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Nœuds : fournit un moniteur Rollup sur les pools de nœuds et de l’état des nœuds individuels dans chaque pool, en évaluant l’utilisation de l’UC et de la mémoire, ainsi que l’état d’un nœud comme indiqué par Kubernetes.

    ![Affichage du moniteur Rollup sur l’intégrité des nœuds](./media/container-insights-health/health-view-nodes-01.png)

Actuellement, seul l’état d’un kubelet virtuel est pris en charge. L’état d’intégrité relatif à l’utilisation de l’UC et de la mémoire des nœuds de kubelet virtuel est signalé comme **Inconnu**, car ces derniers ne reçoivent aucun signal.

Tous les moniteurs sont affichés selon une disposition hiérarchique dans le volet Hiérarchie d’intégrité où vous trouverez un moniteur agrégé représentant l’abstraction ou l’objet Kubernetes (c’est-à-dire, les nœuds ou l’infrastructure Kubernetes). Ce moniteur de niveau supérieur reflète l’intégrité combinée de tous les moniteurs enfants dépendants. Voici les principaux scénarios de supervision utilisés pour déterminer l’intégrité :

* Évaluer l’utilisation de l’UC à partir du nœud et du conteneur.
* Évaluer l’utilisation de la mémoire à partir du nœud et du conteneur.
* État des pods et des nœuds en fonction du calcul de leur état Prêt signalé par Kubernetes.

Les icônes suivants sont utilisés pour indiquer l’état :

|Icône|Signification|  
|--------|-----------|  
|![L’icône de coche verte indique un état sain](./media/container-insights-health/healthyicon.png)|Réussite, l’intégrité est normale (vert)|  
|![Un triangle jaune avec un point d’exclamation correspond à un avertissement](./media/container-insights-health/warningicon.png)|Avertissement (jaune)|  
|![Le bouton rouge avec un X blanc indique un état critique](./media/container-insights-health/criticalicon.png)|Critique (rouge)|  
|![Icône grisé](./media/container-insights-health/grayicon.png)|Inconnu (gris)|  

## <a name="monitor-configuration"></a>Configuration des moniteurs

Pour comprendre le comportement et la configuration de chaque moniteur prenant en charge la fonctionnalité Intégrité d’Azure Monitor pour les conteneurs, consultez [Guide de configuration des moniteurs d’intégrité](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Afficher l’intégrité d’un cluster AKS ou non AKS

Il est possible d’accéder à la fonctionnalité (d’évaluation) Intégrité d’Azure Monitor pour les conteneurs directement à partir d’un cluster AKS en sélectionnant **Insights** dans le volet gauche du Portail Azure. Sous la section **Insights**, sélectionnez **Conteneurs**. 

Pour afficher l’intégrité à partir d’un cluster non AKS (un cluster AKS Engine hébergé localement ou sur Azure Stack), sélectionnez **Azure Monitor** dans le volet gauche du portail Azure. Sous la section **Insights**, sélectionnez **Conteneurs**.  Sur la page des multi-clusters, sélectionnez le cluster non AKS dans la liste.

Dans Azure Monitor pour les conteneurs, sur la page **Cluster**, sélectionnez **Intégrité**.

![Exemple de tableau de bord sur l’intégrité du cluster](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Évaluer l’intégrité des clusters

À l’ouverture de la page Intégrité, l’option **Infrastructure Kubernetes** est sélectionnée par défaut pour la grille **Aspect de l’intégrité**.  La grille résume l’état actuel du moniteur Rollup sur l’intégrité des nœuds de cluster et de l’infrastructure Kubernetes. Si vous sélectionnez l’un des aspects d’intégrité, cela mettra à jour les résultats dans le volet Hiérarchie d’intégrité (volet du milieu) et affichera toutes les analyses enfants selon une disposition hiérarchique avec leur état d’intégrité actuel. Pour afficher plus d’informations concernant un moniteur dépendant, sélectionnez-en un, puis un volet des propriétés s’ouvrira automatiquement sur le côté droit de la page. 

![Volet des propriétés de l’intégrité du cluster](./media/container-insights-health/health-view-property-pane.png)

Dans le volet des propriétés, vous trouverez les informations suivantes :

- L’onglet **Vue d’ensemble** indique l’état actuel du moniteur sélectionné ainsi que la date du dernier calcul du moniteur et du dernier changement d’état. Des informations supplémentaires seront affichées en fonction du type de moniteur sélectionné dans la hiérarchie.

    Si vous sélectionnez un moniteur agrégé dans le volet Hiérarchie d’intégrité, l’onglet **Vue d’ensemble** du volet de propriétés affiche un moniteur Rollup comprenant le nombre total de moniteurs enfants dans la hiérarchie et le nombre de moniteurs agrégés en état critique, avertissement et sain. 

    ![Onglet Vue d’ensemble du volet des propriétés de l’intégrité pour un moniteur agrégé](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Si vous sélectionnez un moniteur d’unités dans le volet Hiérarchie d’intégrité, celui-ci indique également, sous **Dernier changement d’état**, les précédents échantillons calculés et signalés par l’agent conteneurisé au cours des quatre dernières heures. Ces résultats sont basés sur un calcul des moniteurs d’unités permettant de comparer plusieurs valeurs consécutives afin de déterminer l’état. Par exemple, si vous sélectionnez le moniteur d’unités *Pods en état Prêt*, vous obtiendrez les deux derniers échantillons contrôlés par le paramètre *ConsecutiveSamplesForStateTransition*. Pour en savoir plus, consultez la description détaillée des [moniteurs d’unités](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Onglet Vue d’ensemble du volet des propriétés de l’intégrité](./media/container-insights-health/health-overview-unit-monitor.png)

    Si l’heure de **Dernier changement d’état** indique un jour ou une date antérieure, cela signifie que l’état du moniteur n’a pas changé. Toutefois, si le dernier échantillon reçu pour un moniteur d’unités a plus de quatre heures, cela signifie probablement que l’agent conteneurisé n’a pas envoyé de données. Si l’agent sait qu’une ressource particulière existe, par exemple un nœud, mais qu’il n’a pas reçu de données des moniteurs surveillant l’utilisation de l’UC ou de la mémoire du nœud (à titre d’exemple), alors l’état d’intégrité du moniteur est défini sur **Inconnu**.  

- L’onglet **Configuration** affiche les paramètres de configuration par défaut (uniquement pour les moniteurs d’unités, pas les moniteurs agrégés) et leurs valeurs.
- L’onglet **Connaissance** contient des informations expliquant le comportement du moniteur et sa méthode pour évaluer si la condition est non saine.

Les données de supervision de cette page ne s’actualisent pas automatiquement et vous devez sélectionner**Actualiser** en haut de la page pour afficher l’état d’intégrité le plus récent envoyé par le cluster.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [exemples de requêtes de journal](container-insights-log-search.md#search-logs-to-analyze-data) pour voir les requêtes prédéfinies et des exemples permettant d’évaluer ou de personnaliser la génération d’alertes, la visualisation ou l’analyse de vos clusters.
