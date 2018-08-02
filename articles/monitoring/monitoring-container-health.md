---
title: Surveiller l’intégrité d’Azure Kubernetes Service (AKS) (préversion) | Microsoft Docs
description: Cet article explique comment évaluer facilement les performances de votre conteneur AKS pour comprendre rapidement l’utilisation de votre environnement Kubernetes hébergé.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2018
ms.author: magoedte
ms.openlocfilehash: 806487ec731a1b7fe02ccdfe6b285f5b2e119787
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249095"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Surveiller l’intégrité des conteneurs Azure Kubernetes Service (AKS) (préversion)

Cet article explique comment configurer l’intégrité des conteneurs Azure Monitor pour surveiller les performances des charges de travail qui sont déployées dans un environnement Kubernetes et hébergées sur Azure Kubernetes Service (AKS). La surveillance de votre cluster Kubernetes et des conteneurs est cruciale, particulièrement lorsque vous exécutez un cluster de production à grande échelle, avec plusieurs applications.

L’intégrité des conteneurs vous permet de surveiller les performances en collectant des métriques sur la mémoire et le processeur à partir des contrôleurs, des nœuds et des conteneurs qui sont disponibles dans Kubernetes via l’API Metrics. Une fois que vous avez activé l’intégrité des conteneurs, ces métriques sont automatiquement collectées à l’aide d’une version conteneurisée de l’Agent Operations Management Suite pour Linux (OMS) pour Linux, puis stockées dans votre espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md). Les vues prédéfinies incluses montrent les charges de travail de conteneur résidantes et les éléments qui affectent l’intégrité des performances du cluster Kubernetes. Vous pouvez ainsi :  

* Identifier les conteneurs en cours d’exécution sur le nœud, ainsi que leur utilisation moyenne de la mémoire et du processeur. Cette information peut vous aider à identifier les goulots d’étranglement des ressources.
* Identifier l’emplacement du conteneur dans un contrôleur ou un pod. Cette information peut vous permettre de voir les performances globales du contrôleur ou du pod. 
* Voir la quantité de ressources utilisée par les charges de travail qui sont exécutées sur l’hôte et qui ne sont pas liées aux processus standard nécessaires à la prise en charge du pod
* Comprendre le comportement du cluster lorsqu’il subit des charges moyennes et très importantes. Cette information peut vous aider à identifier les besoins en capacité et à déterminer la charge maximale que le cluster peut supporter. 

Si vous souhaitez surveiller et gérer vos hôtes de conteneurs Docker et Windows pour afficher la configuration, un audit et l’utilisation des ressources, consultez la [solution de surveillance des conteneurs](../log-analytics/log-analytics-containers.md).

## <a name="prerequisites"></a>Prérequis 
Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un cluster AKS nouveau ou existant
- Un Agent OMS pour Linux conteneurisé microsoft/oms:ciprod04202018 et versions ultérieures. Le numéro de version est représenté par une date au format suivant : *mmjjaaaa*. Cet agent est installé automatiquement lors de l’intégration du contrôle d’intégrité des conteneurs. 
- Un espace de travail Log Analytics. Vous pouvez le créer lorsque vous activez la surveillance de votre nouveau cluster AKS, ou via [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../log-analytics/log-analytics-quick-create-workspace.md).
- Le rôle Contributeur Log Analytics, pour activer la surveillance des conteneurs. Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Composants 

Votre capacité à surveiller les performances s’appuie sur un Agent OMS pour Linux conteneurisé afin de collecter les données de performances et les événements à partir de tous les nœuds du cluster. L’agent est automatiquement déployé et inscrit dans l’espace de travail Log Analytics spécifié, une fois que vous activez la surveillance des conteneurs. 

>[!NOTE] 
>Si vous avez déjà déployé un cluster AKS, activez la surveillance à l’aide d’un modèle Azure Resource Manager fourni, comme indiqué plus loin dans cet article. Vous ne pouvez pas utiliser `kubectl` pour mettre à niveau, supprimer, redéployer ou déployer l’agent. 
>

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [Portail Azure](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Activer le contrôle d’intégrité du conteneur pour un nouveau cluster
Pendant le déploiement, vous pouvez activer la surveillance d’un nouveau cluster AKS à partir du portail Azure. Suivez les étapes de l’article de démarrage rapide [Déployer un cluster Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md). Dans la page **Surveillance**, pour l’option **Activer la surveillance**, sélectionnez **Oui**, puis sélectionnez un espace de travail Log Analytics existant ou créez-en un. 

Une fois la surveillance activée et toutes les tâches de configuration terminées correctement, vous pouvez contrôler les performances de votre cluster de deux manières :

* Directement dans le cluster AKS en sélectionnant **Intégrité** dans le volet gauche.
* En cliquant sur la vignette **Surveiller l’intégrité des conteneurs** dans la page du cluster AKS pour le cluster sélectionné. Dans Azure Monitor, dans le volet gauche, sélectionnez **Intégrité**. 

  ![Options pour sélectionner le contrôle d’intégrité des conteneurs dans AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous ne puissiez voir les données opérationnelles du cluster. 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Activer le contrôle d’intégrité du conteneur pour des clusters gérés existants
Vous pouvez activer la surveillance d’un cluster AKS déjà déployé, soit à partir du portail Azure ou à l’aide du modèle Azure Resource Manager fourni, en utilisant l’applet de commande PowerShell `New-AzureRmResourceGroupDeployment` ou Azure CLI. 

### <a name="enable-monitoring-in-the-azure-portal"></a>Activer la surveillance dans le portail Azure
Pour activer la surveillance de votre conteneur AKS à partir du portail Azure, effectuez les étapes suivantes :

1. Dans le portail Azure, sélectionnez **Tous les services**. 
2. Dans la liste des ressources, commencez à taper **Conteneurs**.  
    Au fur et à mesure de votre saisie, la liste est filtrée. 
3. Sélectionnez **Services Kubernetes**.  

    ![Lien vers les services Kubernetes](./media/monitoring-container-health/azure-portal-01.png)

4. Dans la liste des conteneurs, sélectionnez un conteneur.
5. Dans la vue d’ensemble du conteneur, sélectionnez **Surveiller l’intégrité des conteneurs**.  
6. Dans la page **Onboarding to Container Health and Logs** (Intégration à la surveillance de l’intégrité des conteneurs et aux journaux), si vous disposez d’un espace de travail Log Analytics appartenant au même abonnement que le cluster, sélectionnez-le dans la liste déroulante.  
    La liste présélectionne l’espace de travail par défaut et l’emplacement où le conteneur AKS est déployé dans l’abonnement. 

    ![Activer le contrôle d’intégrité des conteneurs AKS](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

>[!NOTE]
>Si vous souhaitez créer un espace de travail Log Analytics pour stocker les données de surveillance du cluster, suivez les instructions de [Créer un espace de travail Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Veillez à créer l’espace de travail dans le même abonnement que le conteneur AKS. 
 
Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous ne puissiez voir les données opérationnelles du cluster. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Activer la surveillance à l’aide d’un modèle Azure Resource Manager
Cette méthode inclut deux modèles JSON. Le premier modèle spécifie la configuration permettant d’activer la surveillance, tandis que l’autre modèle contient des valeurs de paramètre que vous configurez pour spécifier les éléments suivants :

* L’ID de ressource du conteneur AKS 
* Le groupe de ressources dans lequel le cluster est déployé
* L’espace de travail et la région Log Analytics dans lesquels créer l’espace de travail 

L’espace de travail Log Analytics doit être créé manuellement. Pour créer l’espace de travail, vous pouvez en configurer un via [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../log-analytics/log-analytics-quick-create-workspace.md).

Si vous n’êtes pas familiarisé avec le déploiement de ressources à l’aide d’un modèle, consultez les rubriques suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI version 2.0.27 ou ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Créer et exécuter un modèle

1. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. Enregistrez ce fichier en tant que **existingClusterOnboarding.json** dans un dossier local.
3. Collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. Modifiez **aksResourceId** et **aksResourceLocation** à l’aide des valeurs situées dans la page **Vue d’ensemble d’AKS** du cluster AKS. La valeur de **workspaceResourceId** est l’ID de ressource complet de votre espace de travail Log Analytics, qui inclut le nom de l’espace de travail. Spécifiez également l’emplacement de l’espace de travail pour **workspaceRegion**. 
5. Enregistrez ce fichier en tant que **existingClusterParam.json** dans un dossier local.
6. Vous êtes prêt à déployer ce modèle. 

    * Utilisez les commandes PowerShell suivantes dans le dossier qui contient le modèle :

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

        ```powershell
        provisioningState       : Succeeded
        ```

    * Pour exécuter la commande ci-dessous à l’aide d’Azure CLI sur Linux :
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

        ```azurecli
        provisioningState       : Succeeded
        ```
Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous ne puissiez voir les données opérationnelles du cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Vérifier le déploiement de l’agent et de la solution
Avec la version *06072018* de l’agent (ou ultérieure), vous pouvez vérifier que l’agent et la solution ont été déployés correctement. Avec les versions antérieures de l’agent, vous pouvez uniquement vérifier le déploiement de l’agent.

### <a name="agent-version-06072018-or-later"></a>Agent version 06072018 ou ultérieure
Pour vérifier que l’agent a été correctement déployé, exécutez la commande suivante : 

```
kubectl get ds omsagent --namespace=kube-system
```

La sortie doit ressembler à la suivante, qui indique que l’agent a été correctement déployé :

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Pour vérifier le déploiement de la solution, exécutez la commande suivante :

```
kubectl get deployment omsagent-rs -n=kube-system
```

La sortie doit ressembler à la suivante, qui indique que l’agent a été correctement déployé :

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versions de l’agent antérieures à la version 06072018

Pour vérifier que la version de l’agent OMS antérieure à la version *06072018* a été correctement déployée, exécutez la commande suivante :  

```
kubectl get ds omsagent --namespace=kube-system
```

La sortie doit ressembler à la suivante, qui indique que l’agent a été correctement déployé :  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Afficher l’utilisation des performances
Lorsque vous consultez l’intégrité du conteneur, la page affiche aussitôt l’utilisation des performances de votre cluster entier. L’affichage des informations sur votre cluster AKS se présente sous quatre perspectives :

- Cluster
- Nœuds 
- Controllers  
- Containers

Sous l’onglet **Cluster**, les quatre graphiques en courbes des performances affichent les métriques liées aux performances clés de votre cluster. 

![Exemples de graphiques de performances sous l’onglet Cluster](./media/monitoring-container-health/container-health-cluster-perfview.png)

Le graphique de performances affiche quatre métriques de performance :

- **Node CPU Utilization&nbsp;%** (Pourcentage d’utilisation du processeur par le nœud) : vue agrégée de l’utilisation du processeur pour le cluster entier. Vous pouvez filtrer les résultats pour l’intervalle de temps en sélectionnant une ou plusieurs des options **Avg** (Moy), **Min**, **Max**, **50th** (50e), **90th** (90e) et **95th**  (95e) du sélecteur de centiles. 
- **Node memory utilization&nbsp;%** (Pourcentage d’utilisation de la mémoire par le nœud) : vue agrégée de l’utilisation de la mémoire pour le cluster entier. Vous pouvez filtrer les résultats pour l’intervalle de temps en sélectionnant une ou plusieurs des options **Avg** (Moy), **Min**, **Max**, **50th** (50e), **90th** (90e) et **95th**  (95e) du sélecteur de centiles. 
- **Node count** (Nombre de nœuds) : nombre et état des nœuds fournis par Kubernetes. L’état des nœuds de cluster représentés sont *All* (Tous), *Ready* (Prêts) et *Not Ready* (Non prêts). Il est possible de les filtrer individuellement ou en les combinant à l’aide du sélecteur situé au-dessus du graphique. 
- **Activity pod count** (Nombre de pods d’activité) : nombre et état des pods fournis par Kubernetes. L’état des pods représentés sont *All* (Tous), *Pending* (En attente), *Running* (En cours d’exécution) et *Unknown* (Inconnus). Il est possible de les filtrer individuellement ou en les combinant à l’aide du sélecteur situé au-dessus du graphique. 

Sous l’onglet **Nodes** (Nœuds), la hiérarchie de ligne suit le modèle d’objet Kubernetes commençant par un nœud dans votre cluster. Développez le nœud pour voir les pods qui y sont actuellement exécutés. Si plusieurs conteneurs sont regroupés dans un pod, ils sont affichés comme la dernière ligne de la hiérarchie. Vous pouvez également voir le nombre de charges de travail non associées à un pod qui sont actuellement exécutées sur l’ordinateur hôte si le processeur ou la mémoire de l’hôte sont très sollicités.

![Exemple de hiérarchie de nœud Kubernetes dans l’affichage des performances](./media/monitoring-container-health/container-health-nodes-view.png)

Vous pouvez sélectionner des contrôleurs ou des conteneurs en haut de la page, puis examiner l’état et l’utilisation des ressources pour ces objets. Utilisez les zones de liste déroulante en haut de l’écran pour filtrer par espace de noms, par service ou par nœud. Si vous préférez afficher l’utilisation de la mémoire, dans la liste déroulante **Metric** (Métrique), sélectionnez **Memory RSS** (Mémoire RSS) ou **Memory working set** (Plage de travail de la mémoire). L’option **Memory RSS** (Mémoire RSS) est uniquement prise en charge par Kubernetes 1.8 et versions ultérieures. Sinon, vous pouvez afficher les valeurs **Min&nbsp;%** sous la forme *NaN&nbsp;%*, qui est une valeur de type données numérique représentant une valeur non définie ou non représentable. 

![Affichage des performances des nœuds d’un conteneur](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Par défaut, les données de performances sont basées sur les six dernières heures, mais vous pouvez modifier la fenêtre à l’aide de la liste déroulante **Période** située en haut à droite. À ce stade, la page ne s’actualise pas automatiquement : vous devez le faire manuellement. Vous pouvez également filtrer les résultats dans l’intervalle de temps en sélectionnant les options du sélecteur de centiles **Avg** (Moy), **Min**, **Max**, **50th** (50e), **90th** (90e) et **95th** (95e). 

![Sélection de centile pour le filtrage des données](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

Dans l’exemple suivant, remarquez que, pour le nœud *aks-nodepool-3977305*, la valeur de **Containers** (Conteneurs) est 5, soit le cumul du nombre de conteneurs déployés.

![Exemple : cumul des conteneurs par nœud](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

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
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% | Tendance du graphique à barres indiquant la métrique de centile en pourcentage du contrôleur. |


Dans le sélecteur, sélectionnez **Controllers** (Contrôleurs).

![Sélectionner l’affichage des contrôleurs](./media/monitoring-container-health/container-health-controllers-tab.png)

Ici, vous pouvez voir l’intégrité des performances de vos contrôleurs.

![Affichage des performances des contrôleurs <nom>](./media/monitoring-container-health/container-health-controllers-view.png)

La hiérarchie de ligne commence par un contrôleur et étend le contrôleur. Vous pouvez voir un ou plusieurs conteneurs. Développez un pod : vous voyez que la dernière ligne affiche le conteneur lié à un pod. 

Les informations qui sont affichées dans les contrôleurs sont décrites dans le tableau suivant :

| Colonne | Description | 
|--------|-------------|
| NOM | Nom du contrôleur.|
| Statut | État du cumul des conteneurs lorsque l’exécution est terminée, par exemple, *OK*, *Terminated* (Terminé), *Failed* (Échec), *Stopped* (Arrêt) ou *Paused* (Pause). Si le conteneur est en cours d’exécution, mais que l’état n’est pas correctement affiché, ou n’a pas été identifié par l’agent et n’a pas répondu dans un délai de 30 minutes, l’état est *Unknown* (Inconnu). Des détails supplémentaires sur l’icône d’état sont fournies dans le tableau ci-dessous.|
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% | Moyenne cumulée du pourcentage moyen de chaque entité pour la métrique et le centile sélectionnés. |
| Avg, Min, Max, 50th, 90th  | Cumul de la performance moyenne du processeur ou de la mémoire du conteneur pour le centile sélectionné. La valeur moyenne est mesurée à partir de la limite Processeur/Mémoire définie pour un pod. |
| Containers | Nombre total de conteneurs pour le contrôleur ou pod. |
| Restarts | Cumul du nombre de redémarrages à partir des conteneurs. |
| Uptime | Indique le temps écoulé depuis le démarrage d’un conteneur. |
| Nœud | Uniquement pour les conteneurs et les pods. Indique le contrôleur. | 
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;%| Graphique à barres indiquant la tendance de la métrique de centile du conteneur. |

Les icônes dans le champ d’état indiquent le statut en ligne des conteneurs :
 
| Icône | Statut | 
|--------|-------------|
| ![Icône de statut en cours d’exécution prêt](./media/monitoring-container-health/container-health-ready-icon.png) | Running (Ready) (En cours d’exécution (Prêt))|
| ![Icône de statut en attente ou en pause](./media/monitoring-container-health/container-health-waiting-icon.png) | Waiting or Paused (En attente ou en pause)|
| ![Icône de statut de dernière exécution signalée](./media/monitoring-container-health/container-health-grey-icon.png) | Last reported running but hasn’t responded more than 30 minutes (Dernière exécution signalée, mais pas de réponse depuis plus de 30 minutes)|
| ![Icône d’état de réussite](./media/monitoring-container-health/container-health-green-icon.png) | Successfully stopped or failed to stop (Réussite ou échec de l’arrêt)|

L’icône d’état affiche un nombre basé sur ce que fournit le pod. Elle montre les deux pires états. Lorsque vous pointez sur un état, elle affiche l’état cumulé de tous les pods du conteneur. Si l’état n’est pas Prêt, il affiche **(0)**. 

Dans le sélecteur, sélectionnez **Containers** (Conteneurs).

![Sélectionner l’affichage des conteneurs](./media/monitoring-container-health/container-health-containers-tab.png)

Ici, vous pouvez voir l’intégrité des performances de vos conteneurs.

![Affichage des performances des contrôleurs <nom>](./media/monitoring-container-health/container-health-containers-view.png)

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
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% | Tendance du graphique à barres qui représente le pourcentage moyen des métriques du conteneur. |

Les icônes du champ d’état indiquent les états en ligne des pods, comme expliqué dans le tableau suivant :
 
| Icône | Statut | 
|--------|-------------|
| ![Icône de statut en cours d’exécution prêt](./media/monitoring-container-health/container-health-ready-icon.png) | Running (Ready) (En cours d’exécution (Prêt))|
| ![Icône de statut en attente ou en pause](./media/monitoring-container-health/container-health-waiting-icon.png) | Waiting or Paused (En attente ou en pause)|
| ![Icône de statut de dernière exécution signalée](./media/monitoring-container-health/container-health-grey-icon.png) | Last reported running but hasn’t responded more than 30 minutes (Dernière exécution signalée, mais pas de réponse depuis plus de 30 minutes)|
| ![Icône de statut Terminé](./media/monitoring-container-health/container-health-terminated-icon.png) | Successfully stopped or failed to stop (Réussite ou échec de l’arrêt)|
| ![Icône de statut Échec](./media/monitoring-container-health/container-health-failed-icon.png) | Statut Échec |

## <a name="container-data-collection-details"></a>Détails sur la collecte de données des conteneurs
Le contrôle d’intégrité des conteneurs collecte diverses métriques de performances et données de journaux à partir des hôtes de conteneur et des conteneurs. Les données sont collectées toutes les trois minutes.

### <a name="container-records"></a>Enregistrements de conteneur

Le tableau suivant présente des exemples d’enregistrements collectés par le contrôle d’intégrité des conteneurs, ainsi que les types de données qui s’affichent dans les résultats de recherche des journaux :

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

## <a name="search-logs-to-analyze-data"></a>Rechercher des journaux pour analyser les données
Log Analytics peut vous aider à rechercher des tendances, à diagnostiquer les goulets d’étranglement, à effectuer des prévisions, ou à mettre en corrélation des données afin de déterminer si la configuration actuelle du cluster garantit des performances optimales. Les recherches prédéfinies dans les journaux peuvent être utilisées immédiatement. Elles peuvent également être personnalisées afin de retourner les informations comme vous le souhaitez. 

Vous pouvez effectuer une analyse interactive des données dans l’espace de travail en sélectionnant l’option **Afficher le journal**, qui est située à droite lorsque vous développez un contrôleur ou un conteneur. La page **Recherche dans les journaux** s’affiche au-dessus de la page du portail Azure.

![Analyser des données dans Log Analytics](./media/monitoring-container-health/container-health-view-logs.png)   

Les sorties de journaux de conteneur transmises à Log Analytics sont STDOUT et STDERR. Étant donné que le contrôle d’intégrité des conteneurs surveille un système Azure Kubernetes (AKS) géré, le système Kube n’est pas collecté aujourd’hui en raison de l’important volume de données générées. 

### <a name="example-log-search-queries"></a>Exemples de requêtes de recherche de journal
Il est souvent utile de créer des requêtes en commençant par un exemple ou deux, puis en les modifiant afin de les adapter à vos besoins. Pour créer des requêtes plus avancées, vous pouvez utiliser les exemples de requêtes suivants :

| Requête | Description | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Liste de toutes les informations sur le cycle de vie d’un conteneur| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Événements Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventaire des images | 
| **Dans Analytique avancée, sélectionnez les graphiques en courbes** :<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Processeur du conteneur | 
| **Dans Analytique avancée, sélectionnez les graphiques en courbes** :<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Mémoire du conteneur |

## <a name="how-to-stop-monitoring-with-container-health"></a>Procédure d’arrêt du contrôle d’intégrité du conteneur
Si après avoir activé la surveillance de votre conteneur AKS, vous décidez d’arrêter cette surveillance, vous pouvez la *désactiver* à l’aide des modèles Azure Resource Manager fournis avec l’applet de commande PowerShell **New-AzureRmResourceGroupDeployment** ou à l’aide d’Azure CLI. Un modèle JSON spécifie la configuration nécessaire à la *désactivation*. L’autre contient des valeurs de paramètre que vous pouvez configurer pour spécifier l’ID de ressource du cluster AKS, ainsi que le groupe de ressources dans lequel est déployé le cluster. 

Si vous n’êtes pas familiarisé avec le déploiement de ressources à l’aide d’un modèle, consultez les rubriques suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI version 2.0.27 ou ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Créer et exécuter le modèle

1. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Enregistrez ce fichier en tant que **OptOutTemplate.json** dans un dossier local.
3. Collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Modifiez **aksResourceId** et **aksResourceLocation** avec les valeurs du cluster AKS, que vous pouvez trouver dans la page **Propriétés** du cluster sélectionné.

    ![Page des propriétés du conteneur](./media/monitoring-container-health/container-properties-page.png)

    Lorsque vous êtes dans la page **Propriétés**, copiez également l’**ID de ressource de l’espace de travail**. Cette valeur est nécessaire si vous décidez ultérieurement de supprimer l’espace de travail Log Analytics. La suppression de l’espace de travail Log Analytics n’est pas effectuée dans le cadre de ce processus. 

5. Enregistrez ce fichier en tant que **OptOutParam.json** dans un dossier local.
6. Vous êtes prêt à déployer ce modèle. 

    * Pour utiliser les commandes PowerShell suivantes dans le dossier qui contient le modèle :

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire au suivant s’affiche avec les résultats :

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Pour effectuer une exécution après une commande avec Azure CLI sur Linux :

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire au suivant s’affiche avec les résultats :

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Si l’espace de travail a été créé uniquement pour prendre en charge la surveillance du cluster et qu’il n’est plus nécessaire, vous devez le supprimer manuellement. Si vous n’êtes pas familiarisé avec la suppression d’un espace de travail, consultez la rubrique [Supprimer un espace de travail Azure Log Analytics avec le portail Azure](../log-analytics/log-analytics-manage-del-workspace.md). N’oubliez pas l’**ID de ressource de l’espace de travail** que nous avons copié précédemment à l’étape 4 car vous en aurez besoin. 

## <a name="troubleshooting"></a>Résolution de problèmes
Cette section fournit des informations pour vous aider à résoudre les problèmes d’intégrité des conteneurs.

Si le contrôle d’intégrité des conteneurs a été correctement activé et configuré, mais que vous ne voyez aucune information d’état ni aucun résultat dans Log Analytics lorsque vous effectuez une recherche dans les journaux, vous pouvez effectuer les étapes suivantes pour diagnostiquer le problème : 

1. Vérifiez le statut de l’agent en exécutant la commande suivante : 

    `kubectl get ds omsagent --namespace=kube-system`

    La sortie doit ressembler à la suivante, qui indique que l’agent a été correctement déployé :

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Exécutez la commande suivante pour vérifier l’état du déploiement de la solution avec la version d’agent *06072018* ou version ultérieure :

    `kubectl get deployment omsagent-rs -n=kube-system`

    La sortie doit ressembler à la suivante, qui indique que l’agent a été correctement déployé :

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Pour vérifier si le pod est en cours d’exécution, exécutez la commande suivante : `kubectl get pods --namespace=kube-system`

    La sortie doit ressembler à ce qui suit, avec le statut *Running* (en cours d’exécution) pour l’Agent OMS :

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Vérifiez les journaux de l’agent. Quand l’agent conteneurisé est déployé, il effectue une vérification rapide en exécutant les commandes OMI, puis indique la version de l’agent et du fournisseur. 

5. Pour vérifier que l’agent a bien été intégré, exécutez la commande suivante : `kubectl logs omsagent-484hw --namespace=kube-system`

    Le statut doit ressembler à ce qui suit :

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour afficher des informations détaillées sur l’intégrité des conteneurs et les performances d’application, utilisez l’option [Rechercher dans les journaux](../log-analytics/log-analytics-log-search.md). 
