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
ms.date: 06/19/2018
ms.author: magoedte
ms.openlocfilehash: 7c4294947cba72b1638e77c2dd8de1f5ee37b62a
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285984"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Surveiller l’intégrité d’un conteneur Azure Kubernetes Service (AKS) (préversion)

Cet article explique comment configurer et utiliser l’intégrité du conteneur Azure Monitor pour surveiller les performances de vos charges de travail déployées dans des environnements Kubernetes hébergés sur Azure Kubernetes Service (AKS).  La surveillance de votre cluster Kubernetes et des conteneurs est cruciale, particulièrement lorsque vous exécutez un cluster de production à grande échelle avec plusieurs applications.

L’intégrité du conteneur vous permet de surveiller les performances en collectant des métriques sur la mémoire et le processeur à partir des contrôleurs, nœuds et conteneurs disponibles dans Kubernetes via l’API Metrics.  Après avoir activé l’intégrité du conteneur, ces métriques sont automatiquement collectées pour vous à l’aide d’une version en conteneur de l’Agent OMS pour Linux, puis stockées dans votre espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md).  Les affichages prédéfinis inclus montrent les charges de travail de conteneur résidantes et les éléments qui affectent l’intégrité des performances du cluster Kubernetes. Vous pouvez ainsi :  

* identifier les conteneurs en cours d’exécution sur le nœud et leur utilisation moyenne de la mémoire et du processeur afin de cibler les goulets d’étranglement des ressources ;
* identifier où le conteneur se trouve dans un contrôleur et/ou des pods afin d’afficher les performances globales d’un contrôleur ou d’un pod ; 
* évaluer l’utilisation des ressources des charges de travail en cours d’exécution sur l’hôte non lié aux processus standard prenant en charge le pod ;
* comprendre le comportement du cluster sous une charge moyenne et sous la charge la plus lourde afin d’identifier les besoins de capacité et déterminer la charge maximale supportable. 

Si vous souhaitez surveiller et gérer vos hôtes de conteneurs Docker et Windows pour afficher la configuration, un audit et l’utilisation des ressources, consultez la [solution de surveillance des conteneurs](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Configuration requise 
Avant de commencer, passez en revue les informations suivantes pour connaître la configuration requise.

- Les versions suivantes du cluster AKS sont prises en charge : 1.7.7 à 1.9.6.
- Un agent OMS en conteneur pour la version Linux microsoft/oms:ciprod04202018 et versions ultérieures. Cet agent est installé automatiquement lors de l’intégration du contrôle d’intégrité du conteneur.  
- Un espace de travail Log Analytics.  Il peut être créé lorsque vous activez la surveillance de votre nouveau cluster AKS, ou vous pouvez en créer un via [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), ou à partir du [portail Azure](../log-analytics/log-analytics-quick-create-workspace.md).
- Membre du rôle de collaborateur Log Analytics pour activer l’analyse des conteneurs.  Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Composants 

Cette fonctionnalité s’appuie sur un Agent OMS en conteneur pour Linux afin de collecter les données de performances et les événements sur tous les nœuds du cluster.  L’agent est automatiquement déployé et inscrit dans l’espace de travail Log Analytics spécifié, une fois que vous activez la surveillance des conteneurs. 

>[!NOTE] 
>Si vous avez déjà déployé un cluster AKS, vous activez la surveillance à l’aide d’un modèle Azure Resource Manager fourni, comme indiqué plus loin dans cet article. Vous ne pouvez pas utiliser `kubectl` pour mettre à niveau, supprimer, redéployer ou déployer l’agent.  
>

## <a name="sign-in-to-azure-portal"></a>Se connecter au portail Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Activer le contrôle d’intégrité du conteneur pour un nouveau cluster
Vous pouvez uniquement activer le contrôle de votre cluster AKS lors de son déploiement depuis le portail Azure.  Suivez les étapes de l’article de démarrage rapide [Déployer un cluster Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md).  Dans la page **Surveillance**, sélectionnez **Oui** pour activer l’option **Activer la surveillance**, puis sélectionnez un espace de travail Log Analytics existant ou créez-en un.  

Une fois ma surveillance activée, toutes les tâches de configuration sont terminées avec succès et vous pouvez contrôler les performances de votre cluster de deux manières :

1. Directement à partir du cluster AKS en sélectionnant **Intégrité** dans le volet gauche.<br><br> 
2. En cliquant sur la mosaïque **Analyser le fonctionnement du conteneur** dans la page du cluster AKS pour le cluster sélectionné.  Dans Azure Monitor, sélectionnez **Intégrité** dans le volet gauche.  

![Options pour sélectionner le contrôle d’intégrité du conteneur dans AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Une fois la surveillance activée, environ 15 minutes peuvent s’écouler avant que les données opérationnelles du cluster apparaissent.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Activer le contrôle d’intégrité du conteneur pour des clusters gérés existants
Le contrôle d’intégrité d’un conteneur AKS déjà déployé peut être activé depuis le portail Azure ou à l’aide du modèle Azure Resource Manager fourni avec l’applet de commande PowerShell **New-AzureRmResourceGroupDeployment** ou l’interface CLI Azure.  


### <a name="enable-from-azure-portal"></a>Activer à partir du portail Azure
Procédez comme suit pour activer l’analyse de votre conteneur AKS à partir du portail Azure.

1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste des ressources, saisissez **Conteneurs**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Services Kubernetes**.<br><br> ![Portail Azure](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. Dans votre liste de conteneurs, sélectionnez un conteneur.
3. Dans la page de présentation des conteneurs, sélectionnez **Analyser le fonctionnement du conteneur** pour que la page **Onboarding to Container Health and Logs** (Intégration à l’analyse du fonctionnement des conteneurs et aux journaux) s’affiche.
4. Sur la page **Onboarding to Container Health and Logs** (Intégration à la surveillance de l’intégrité des conteneurs et aux journaux), si vous disposez d’un espace de travail Log Analytics dans le même abonnement que le cluster, sélectionnez-le dans la liste déroulante.  La liste présélectionne l’espace de travail par défaut et l’emplacement où le conteneur AKS est déployé dans l’abonnement. Vous pouvez également sélectionner **Créer nouveau** et spécifiez un nouvel espace de travail dans le même abonnement.<br><br> ![Activer l’analyse du fonctionnement des conteneurs AKS](./media/monitoring-container-health/container-health-enable-brownfield.png) 

    Si vous sélectionnez **Créer nouveau**, le volet **Créer un espace de travail** s’affiche. La **région** par défaut correspond à celle dans laquelle votre ressource de conteneurs est créée et vous pouvez accepter la valeur par défaut ou sélectionner une autre région, puis spécifier un nom pour l’espace de travail.  Cliquez sur **Créer** pour valider la sélection.<br><br> ![Définir l’espace de travail pour l’analyse des conteneurs](./media/monitoring-container-health/create-new-workspace-01.png)  

    >[!NOTE]
    >À ce stade, vous ne pouvez pas créer de nouvel espace de travail dans la région USA Centre-Ouest. Vous pouvez juste sélectionner un espace de travail préexistant dans cette région.  Bien que vous puissiez sélectionner cette région dans la liste, le déploiement démarre, mais échoue peu de temps après.  
    >
 
Une fois la surveillance activée, environ 15 minutes peuvent s’écouler avant que les données opérationnelles du cluster apparaissent. 

### <a name="enable-using-azure-resource-manager-template"></a>Activer l’utilisation d’un modèle Azure Resource Manager
Cette méthode inclut deux modèles JSON, un modèle spécifie la configuration pour activer la surveillance, tandis que l’autre modèle JSON contient des valeurs de paramètre que vous configurez pour spécifier les éléments suivants :

* ID de ressource du conteneur AKS 
* Groupe de ressources dans lequel le cluster est déployé 
* Espace de travail et région Log Analytics dans lesquels créer l’espace de travail 

L’espace de travail Log Analytics doit être créé manuellement.  Pour créer l’espace de travail, vous pouvez en configurer un via [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), ou depuis le [portail Azure](../log-analytics/log-analytics-quick-create-workspace.md).

Si vous n’êtes pas familiarisé avec les concepts de déploiement de ressources à l’aide d’un modèle avec PowerShell, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md), ou pour l’interface CLI Azure, consultez [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Si vous avez choisi d’utiliser l’interface de ligne de commande Azure CLI, vous devez tout d’abord installer et utiliser l’interface CLI localement.  Ce tutoriel nécessite l’exécution d’Azure CLI 2.0.27 ou version ultérieure. Exécutez `az --version` pour identifier la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-template"></a>Créer et exécuter le modèle

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
3. Copiez et collez la syntaxe JSON suivante dans votre fichier :

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

4. Modifiez **aksResourceId** et **aksResourceLocation** avec les valeurs que vous pouvez trouver dans la page **Vue d’ensemble d’AKS** pour le cluster AKS.  La valeur de **workspaceResourceId** est l’ID de ressource complet de votre espace de travail Log Analytics, qui inclut le nom de l’espace de travail.  Spécifiez également l’emplacement dans lequel se trouve l’espace de travail pour **workspaceRegion**.    
5. Enregistrez ce fichier en tant que **existingClusterParam.json** dans un dossier local.
6. Vous êtes prêt à déployer ce modèle. 

    * Utilisez les commandes PowerShell suivantes à partir du dossier qui contient le modèle :

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, vous voyez un message similaire au message suivant qui inclut le résultat :

        ```powershell
        provisioningState       : Succeeded
        ```

    * Pour exécuter après une commande avec Azure CLI sur Linux :
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, vous voyez un message similaire au message suivant qui inclut le résultat :

        ```azurecli
        provisioningState       : Succeeded
        ```
Une fois la surveillance activée, environ 15 minutes peuvent s’écouler avant que les données opérationnelles du cluster apparaissent.  

## <a name="verify-agent-deployed-successfully"></a>Vérifier que l’agent est correctement déployé
Pour vérifier que l’Agent OMS est correctement déployé, exécutez la commande suivante : ` kubectl get ds omsagent --namespace=kube-system`.

La sortie doit ressembler ce qui suit, indiquant que le déploiement a réussi :

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Afficher l’utilisation des performances
Lorsque vous consultez l’intégrité du conteneur, la page affiche aussitôt l’utilisation des performances de vos nœuds de cluster.  L’affichage des informations sur votre cluster AKS se présente sous trois perspectives :

- Nœuds 
- Controllers  
- Containers

La hiérarchie de ligne suit le modèle d’objet Kubernetes commençant par un nœud dans votre cluster.  En développant le nœud, vous voyez un ou plusieurs pods en cours d’exécution sur le nœud, et s’il existe plusieurs conteneurs regroupés dans un pod, ils apparaissent en tant que dernière ligne dans la hiérarchie.<br><br> ![Exemple de hiérarchie de nœud Kubernetes dans l’affichage des performances](./media/monitoring-container-health/container-performance-and-health-view-03.png)

Vous pouvez sélectionner des contrôleurs ou des conteneurs en haut de la page puis examiner l’état et l’utilisation des ressources de ces objets.  Utilisez les zones de liste déroulante en haut de l’écran pour filtrer par espace de noms, service et nœud. Si vous préférez afficher l’utilisation de la mémoire, dans la liste déroulante **Métrique**, sélectionnez **Mémoire résidente** ou **Plage de travail de la mémoire**.  L’option **Mémoire résidente** est uniquement prise en charge par Kubernetes 1.8 et versions ultérieures. Sinon, vous pouvez afficher les valeurs **AVG %** sous la forme *NaN%*, qui est une valeur de type de données numérique représentant une valeur non définie ou non représentable. 

![Affichage des performances des nœuds d’un conteneur](./media/monitoring-container-health/container-performance-and-health-view-04.png)

Par défaut, les données de performances sont basées sur les six dernières heures, mais vous pouvez modifier la fenêtre à l’aide de la liste déroulante **Période** dans le coin supérieur droit de la page. À ce stade, la page ne s’actualise pas automatiquement : vous devez le faire manuellement. 

Dans l’exemple suivant, vous remarquez que pour le nœud *aks-agentpool-3402399-0*, la valeur **Containers** est 10, soit le cumul du nombre total de conteneurs déployés.<br><br> ![Cumul des conteneurs pour l’exemple de nœud](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> Cela peut vous aider à identifier rapidement un déséquilibre de conteneurs entre les nœuds de votre cluster.  

Le tableau suivant décrit les informations présentées lorsque vous affichez des nœuds.

| Colonne | Description | 
|--------|-------------|
| NOM | Nom de l’hôte |
| Statut | Vue Kubernetes de l’état du nœud |
| AVG % | Pourcentage moyen du nœud basé sur la métrique sélectionnée pour la période spécifiée. |
| AVERAGE | Moyenne de la valeur réelle des nœuds, basée sur la métrique sélectionnée pour la période spécifiée.  La valeur moyenne est mesurée à partir de la limite de processeur/mémoire définie pour un nœud ; pour les pods et les conteneurs, il s’agit de la valeur moyenne indiquée par l’hôte. |
| Containers | Nombre de conteneurs. |
| Uptime | Indique le temps écoulé depuis le démarrage ou le redémarrage d’un nœud. |
| Pod | Uniquement pour les conteneurs. Indique les pods. |
| Controllers | Uniquement pour les conteneurs et les pods. Indique le contrôleur. Les pods ne figurent pas tous dans un contrôleur et certains peuvent donc afficher la mention N/A (non applicable). | 
| Trend AVG% | Graphique à barres indiquant une tendance basée sur la métrique de % moyen du conteneur et du nœud. |


Dans le sélecteur, choisissez **Contrôleurs**.<br><br> ![Sélectionner la vue des contrôleurs](./media/monitoring-container-health/container-performance-and-health-view-08.png)

Ici, vous pouvez voir l’intégrité des performances de vos contrôleurs.<br><br> ![Affichage des performances des contrôleurs <nom>](./media/monitoring-container-health/container-performance-and-health-view-05.png)

La hiérarchie de ligne commence par un contrôleur et s’étend au contrôleur, et vous voyez un ou plusieurs pods, ou un ou plusieurs conteneurs.  Développez un module et la dernière ligne affiche le conteneur lié à un pod.  

Le tableau suivant décrit les informations présentées lorsque vous affichez des contrôleurs.

| Colonne | Description | 
|--------|-------------|
| NOM | Nom du contrôleur|
| Statut | Statut des conteneurs lorsque l’exécution est terminée, par exemple *Terminated* (terminé), *Failed* (échec), *Stopped* (arrêt) ou *Paused* (pause). Si le conteneur est en cours d’exécution, mais que le statut n’est pas correctement affiché ou n’a pas été identifié par l’agent et n’a pas répondu dans un délai de 30 minutes, le statut sera *Unknown* (inconnu). |
| AVG % | Cumul du % moyen de chaque entité pour la métrique sélectionnée. |
| AVERAGE | Cumul de la performance moyenne du processeur ou de la mémoire du conteneur.  La valeur moyenne est mesurée à partir de la limite Processeur/Mémoire définie pour un pod. |
| Containers | Nombre total de conteneurs pour le contrôleur ou pod. |
| Restarts | Cumul du nombre de redémarrages à partir des conteneurs. |
| Uptime | Indique le temps écoulé depuis le démarrage d’un conteneur. |
| Pod | Uniquement pour les conteneurs. Indique les pods. |
| Nœud | Uniquement pour les conteneurs et les pods. Indique le contrôleur. | 
| Trend AVG% | Graphique à barres indiquant une tendance de la métrique de % moyen du conteneur. |

Dans le sélecteur, choisissez **Conteneurs**.<br><br> ![Sélectionner la vue des conteneurs](./media/monitoring-container-health/container-performance-and-health-view-09.png)

Ici, nous pouvons voir l’intégrité des performances de vos conteneurs.<br><br> ![Affichage des performances des contrôleurs <nom>](./media/monitoring-container-health/container-performance-and-health-view-06.png)

Le tableau suivant décrit les informations présentées lorsque vous affichez des conteneurs.

| Colonne | Description | 
|--------|-------------|
| NOM | Nom du contrôleur|
| Statut | Statut du cumul des conteneurs, le cas échéant. |
| AVG % | Cumul du % moyen de chaque entité pour la métrique sélectionnée. |
| AVERAGE | Cumul de la performance moyenne du processeur ou de la mémoire du conteneur. La valeur moyenne est mesurée à partir de la limite Processeur/Mémoire définie pour un pod. |
| Containers | Nombre total de conteneurs pour le contrôleur.|
| Restarts | Indique le temps écoulé depuis le démarrage d’un conteneur. |
| Uptime | Indique le temps écoulé depuis le démarrage ou le redémarrage d’un conteneur. |
| Pod | Informations sur pod où il réside. |
| Nœud |  Nœud sur lequel se trouve le conteneur.  | 
| Trend AVG% | Graphique à barres indiquant une tendance de la métrique de % moyen du conteneur. |

## <a name="container-data-collection-details"></a>Détails sur la collecte de données des conteneurs
Le contrôle d’intégrité des conteneurs collecte diverses métriques de performances et données de journaux à partir des hôtes de conteneur et des conteneurs. Les données sont collectées toutes les trois minutes.

### <a name="container-records"></a>Enregistrements de conteneur

Le tableau suivant présente des exemples d’enregistrements collectés par le contrôle d’intégrité des conteneurs, ainsi que les types de données qui s’affichent dans les résultats de recherche des journaux.

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
Log Analytics peut vous aider à rechercher des tendances, à diagnostiquer les goulets d’étranglement, à effectuer des prévisions, ou à mettre en corrélation des données afin de déterminer si la configuration actuelle du cluster garantit des performances optimales.  Des recherches de journaux prédéfinies sont fournies pour vous permettre de commencer immédiatement à les utiliser ou pour les personnaliser afin de retourner les informations comme vous le souhaitez. 

Vous pouvez effectuer une analyse interactive des données dans l’espace de travail en sélectionnant l’option **Afficher le journal**, disponible tout à fait à droite lorsque vous développez un conteneur.  La page **Recherche dans les journaux** apparaît au-dessus de la page dans laquelle vous étiez sur le portail.<br><br> ![Analyser des données dans Log Analytics](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

Les sorties de journaux de conteneur transmises à Log Analytics sont STDOUT et STDERR. Étant donné que le contrôle d’intégrité du conteneur surveille un système Azure Kubernetes (AKS) géré, le système Kube n’est pas collecté aujourd'hui en raison de l’important volume de données générées.     

### <a name="example-log-search-queries"></a>Exemples de requêtes de recherche de journal
Il est souvent utile de créer des requêtes en commençant par un exemple ou deux, puis en les modifiant afin de les adapter à vos besoins. Vous pouvez utiliser les exemples de requêtes suivants pour vous aider à créer des requêtes plus avancées.

| Requête | Description | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Liste de toutes les informations sur le cycle de vie d’un conteneur| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Événements Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventaire d’image | 
| **Dans Analytique avancée, sélectionnez les graphiques en courbes** :<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Processeur du conteneur | 
| **Dans Analytique avancée, sélectionnez les graphiques en courbes** :<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Mémoire du conteneur |

## <a name="how-to-stop-monitoring-with-container-health"></a>Procédure d’arrêt du contrôle d’intégrité du conteneur
Après avoir activé la surveillance de votre conteneur AKS, vous décidez d’arrêter cette surveillance en la *désactivant* à l’aide des modèles Azure Resource Manager fournis avec l’applet de commande PowerShell **New-AzureRmResourceGroupDeployment** ou d’Azure CLI.  Un modèle JSON spécifie la configuration pour la *désactivation*, tandis que l’autre modèle JSON contient des valeurs de paramètre que vous configurez pour spécifier l’ID de ressource du cluster AKS ainsi que le groupe de ressources dans lequel le cluster est déployé.  Si vous n’êtes pas familiarisé avec les concepts de déploiement de ressources à l’aide d’un modèle avec PowerShell, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md), ou pour l’interface CLI Azure, consultez [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Si vous avez choisi d’utiliser l’interface de ligne de commande Azure CLI, vous devez tout d’abord installer et utiliser l’interface CLI localement.  Ce tutoriel nécessite l’exécution d’Azure CLI 2.0.27 ou version ultérieure. Exécutez `az --version` pour identifier la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. Enregistrez ce fichier en tant que **OptOutTemplate.json** dans un dossier local.
3. Copiez et collez la syntaxe JSON suivante dans votre fichier :

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

4. Modifiez **aksResourceId** et **aksResourceLocation** avec les valeurs du cluster AKS, que vous pouvez trouver dans la page **Propriétés** pour le cluster sélectionné.<br><br> ![Page des propriétés du conteneur](./media/monitoring-container-health/container-properties-page.png)<br>

    Lorsque vous êtes dans la page **Propriétés**, copiez également l’**ID de ressource de l’espace de travail**.  Cette valeur est requise si vous décidez de supprimer l’espace de travail Log Analytics ultérieurement, opération qui ne sera pas effectuée dans le cadre de ce processus.  

5. Enregistrez ce fichier en tant que **OptOutParam.json** dans un dossier local.
6. Vous êtes prêt à déployer ce modèle. 

    * Pour utiliser les commandes PowerShell suivantes à partir du dossier qui contient le modèle :

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire au message suivant qui inclut le résultat s’affiche :

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Pour exécuter après une commande avec Azure CLI sur Linux :

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire au message suivant qui inclut le résultat s’affiche :

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Si l’espace de travail a été créé uniquement pour prendre en charge la surveillance du cluster et qu’il n’est plus nécessaire, vous devez le supprimer manuellement. Si vous n’êtes pas familiarisé avec la suppression d’un espace de travail, consultez la rubrique [Supprimer un espace de travail Azure Log Analytics avec le portail Azure](../log-analytics/log-analytics-manage-del-workspace.md).  N’oubliez pas l’**ID de ressource de l’espace de travail** que nous avons copié précédemment à l’étape 4 car vous en aurez besoin.  

## <a name="troubleshooting"></a>Résolution de problèmes
Cette section fournit des informations pour vous aider à résoudre les problèmes d’intégrité des conteneurs.

Si le contrôle d’intégrité du conteneur a été correctement activé et configuré, mais que vous ne voyez aucune information de statut ni aucun résultat dans Log Analytics lorsque vous effectuez une recherche dans les journaux, vous pouvez effectuer les étapes suivantes diagnostiquer le problème.   

1. Vérifiez le statut de l’agent en exécutant la commande suivante : `kubectl get ds omsagent --namespace=kube-system`

    La sortie doit ressembler ce qui suit, indiquant que l’agent s’exécute sur tous les nœuds du cluster.  Par exemple, ce cluster comporte deux nœuds, et la valeur devrait correspondre au même nombre de nœuds.  

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Consultez le statut du pod pour vérifier s’il est en cours d’exécution ou pas, en exécutant la commande suivante : `kubectl get pods --namespace=kube-system`

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

3. Vérifiez les journaux de l’agent. Quand l’agent en conteneur est déployé, il effectue une vérification rapide en exécutant les commandes OMI puis indique la version de l’agent et le fournisseur Docker. Pour vérifier que l’agent a été intégré, exécutez la commande suivante : `kubectl logs omsagent-484hw --namespace=kube-system`

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

Utilisez l’option [Rechercher dans les journaux](../log-analytics/log-analytics-log-search.md) pour afficher des informations détaillées sur l’intégrité du conteneur et les performances de l’application.  
