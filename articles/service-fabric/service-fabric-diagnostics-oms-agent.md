---
title: Azure Service Fabric - Analyse des performances avec Log Analytics | Microsoft Docs
description: Découvrez comment configurer l’agent Log Analytics de façon à effectuer le monitoring des conteneurs et des compteurs de performances de vos clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 3d7cf50bd2c845764aa2c3ac3b3db6891ff5c8d8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993848"
---
# <a name="performance-monitoring-with-log-analytics"></a>Analyse des performances avec Log Analytics

Cet article explique comment ajouter l’agent Log Analytics à un cluster sous la forme d’une extension du groupe de machines virtuelles identiques, puis le connecter à un espace de travail Azure Log Analytics existant. Cela permet de collecter des données de diagnostic sur les conteneurs, les applications et les performances. Si vous l’ajoutez en tant qu’extension à la ressource de groupe de machines virtuelles identiques, Azure Resource Manager veille à l’installer sur tous les nœuds, même lors de la mise à l’échelle du cluster.

> [!NOTE]
> Cet article suppose que vous disposez d’un espace de travail Azure Log Analytics déjà configuré. Si ce n’est pas le cas, consultez [Configurer Azure Log Analytics](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Ajouter l’extension d’agent via Azure CLI

La meilleure façon d’ajouter l’agent Log Analytics à un cluster est d’utiliser les API des groupes de machines virtuelles identiques d’Azure CLI. Si vous n’avez pas encore configuré Azure CLI, accédez au portail Azure, puis ouvrez une instance [Cloud Shell](../cloud-shell/overview.md) ou [installez Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Lorsque votre instance de Cloud Shell est demandée, veillez à vous trouver dans le même abonnement que votre ressource. Utilisez la commande `az account show` pour vérifier que la valeur « name » correspond à celle de l’abonnement de votre cluster.

2. Dans le Portail, accédez au groupe de ressources où se trouve votre espace de travail Log Analytics. Cliquez sur la ressource Log Analytics (le type de la ressource sera Log Analytics). Une fois que vous vous trouvez sur la page de vue d’ensemble des ressources, cliquez sur **Paramètres avancés** dans la section Paramètres du menu de gauche.

    ![Page des propriétés Log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Cliquez sur **Serveurs Windows** si vous créez un cluster Windows, ou sur **Serveurs Linux** si vous créez un cluster Linux. Cette page affiche les éléments `workspace ID` et `workspace key` (répertoriés en tant que clé primaire dans le portail). Vous aurez besoin de ces éléments pour l’étape suivante.

4. Exécutez la commande pour installer l’agent Log Analytics sur votre cluster en utilisant l’API `vmss extension set` de votre service Cloud Shell :

    Pour un cluster Windows :
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Pour un cluster Linux :

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Voici un exemple montrant l’ajout de l’agent Log Analytics à un cluster Windows.

    ![Commande CLI de l’agent Log Analytics](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Normalement, l’ajout de l’agent aux nœuds prend moins de 15 minutes. Vous pouvez vérifier que les agents ont bien été ajoutés à l’aide de l’API `az vmss extension list` :

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Ajouter l’agent en utilisant un modèle Resource Manager

Des exemples de modèles Gestionnaire des ressources qui déploient un espace de travail Azure Log Analytics et ajoutent un agent à chacun de vos nœuds sont disponibles pour [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) ou [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Vous pouvez télécharger et modifier ces modèles pour déployer un cluster qui correspond mieux à vos besoins.

## <a name="view-performance-counters"></a>Afficher les compteurs de performances

Maintenant que vous avez ajouté l’agent Log Analytics, accédez au portail Log Analytics pour choisir les compteurs de performances à collecter. 

1. Dans le portail Azure, accédez au groupe de ressources dans lequel vous avez créé la solution Service Fabric Analytics. Sélectionnez **ServiceFabric\<nomEspaceDeTravailLogAnalytics\>**.

2. Cliquez sur **Log Analytics**.

3. Cliquez sur **Paramètres avancés**.

4. Cliquez sur **Données**, puis sur **Compteurs de performances Windows ou Linux**. Une liste des compteurs par défaut que vous pouvez choisir d’activer s’affiche, et vous pouvez également définir l’intervalle de collecte. En outre, vous pouvez ajouter [d’autres compteurs de performances](service-fabric-diagnostics-event-generation-perf.md) à collecter. Le format approprié est référencé dans cet [article](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Cliquez sur **Enregistrer**, puis sur **OK**.

6. Fermez le panneau Paramètres avancés.

7. Sous le titre Général, cliquez sur **Vue d’ensemble**.

8. Des vignettes représentant un graphique s’affichent pour chacune des solutions activées, y compris pour Service Fabric. Cliquez sur le graphique **Service Fabric** pour accéder à la solution Service Fabric Analytics.

9. Vous pouvez voir quelques vignettes avec des graphiques sur le canal opérationnel et les événements de services fiables. La représentation graphique du flux de données correspondant aux compteurs que vous avez sélectionnés s’affichent dans Métriques de nœud. 

10. Cliquez sur un graphique de métrique de conteneur pour afficher les détails supplémentaires. Vous pouvez également interroger les données du compteur de performances de la même façon que les événements de cluster et appliquer des filtres sur les nœuds, le nom du compteur de performances et les valeurs à l’aide du langage de requête Kusto.

![Interroger le compteur de performances Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Étapes suivantes

* Collectez les [compteurs de performances](service-fabric-diagnostics-event-generation-perf.md) dont vous avez besoin. Pour configurer l’agent Log Analytics de façon à collecter certains compteurs de performances, voir [Configurer les sources de données](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Configurez Log Analytics pour paramétrer [l’alerte automatisée](../log-analytics/log-analytics-alerts.md) afin de faciliter la détection et les diagnostics
* En guise d’alternative, vous pouvez collecter des compteurs de performances via [l’extension Azure Diagnostics et les envoyer à Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)
