---
title: Guide pratique pour intégrer Azure Monitor pour conteneurs (préversion) | Microsoft Docs
description: Cet article décrit la façon dont vous intégrez et configurez Azure Monitor pour les conteneurs afin que vous puissiez comprendre les performances de votre conteneur et les problèmes liés aux performances identifiés.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: 4893141ec199d21e09bfa2b6a790473d983acd32
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165405"
---
# <a name="how-to-onboard-azure-monitor-for-containers-preview"></a>Guide pratique pour intégrer Azure Monitor pour conteneurs (préversion) 
Cet article explique comment configurer Azure Monitor les conteneurs en vue de surveiller les performances des charges de travail qui sont déployées dans un environnement Kubernetes et hébergées sur [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Azure Monitor pour conteneurs peut être activé pour les nouveaux déploiements d’AKS à l’aide des méthodes prises en charge suivantes :

* Déployer un cluster Kubernetes managé à partir du portail Azure ou avec Azure CLI
* Création d’un cluster Kubernetes à l’aide de [Terraform et AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

Vous pouvez également activer la supervision d’un ou plusieurs clusters AKS existants à partir du portail Azure ou avec Azure CLI. 

## <a name="prerequisites"></a>Prérequis 
Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un espace de travail Log Analytics. Vous pouvez le créer lorsque vous activez la supervision de votre nouveau cluster AKS ou lorsque vous laissez l’expérience d’intégration créer un espace de travail par défaut dans le groupe de ressources par défaut de l’abonnement de cluster AKS. Si vous choisissez de le créer vous-même, vous pouvez le créer via [Azure Resource Manager](../../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../../log-analytics/log-analytics-quick-create-workspace.md).
- Vous êtes membre du rôle de contributeur Log Analytics pour activer la supervision des conteneurs. Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Composants 

Votre capacité à surveiller les performances s’appuie sur un Agent Log Analytics pour Linux conteneurisé, afin de collecter les données de performances et les événements à partir de tous les nœuds du cluster. L’agent est automatiquement déployé et inscrit dans l’espace de travail Log Analytics spécifié une fois Azure Monitor pour conteneurs activé. La version déployée est microsoft/oms:ciprod04202018 ou ultérieure. Elle est représentée par une date au format suivant : *mmjjaaaa*. 

>[!NOTE] 
>Si vous avez déjà déployé un cluster AKS, activez la supervision à l’aide d’Azure CLI ou un modèle Azure Resource Manager fourni, comme indiqué plus loin dans cet article. Vous ne pouvez pas utiliser `kubectl` pour mettre à niveau, supprimer, redéployer ou déployer l’agent. Le modèle doit être déployé dans le même groupe de ressources que le cluster.”

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [Portail Azure](https://portal.azure.com). 

## <a name="enable-monitoring-for-a-new-cluster"></a>Activer la supervision d’un nouveau cluster
Pendant le déploiement, vous pouvez activer la supervision d’un nouveau cluster AKS dans le portail Azure, avec Azure CLI ou avec Terraform.  Suivez les étapes de l’article de démarrage rapide [Déployer un cluster Azure Kubernetes Service (AKS)](../../aks/kubernetes-walkthrough-portal.md) si vous souhaitez l’activer depuis le portail. Dans la page **Surveillance**, pour l’option **Activer la surveillance**, sélectionnez **Oui**, puis sélectionnez un espace de travail Log Analytics existant ou créez-en un. 

Pour activer la supervision d’un nouveau cluster AKS créé avec Azure CLI, suivez l’étape de l’article de démarrage rapide sous la section [Créer un cluster AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI version 2.0.43 ou ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

Si vous [déployez un cluster AKS à l’aide de Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), vous pouvez également activer Azure Monitor pour conteneurs en incluant l’argument [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) et en spécifiant **oms_agent**.  

Une fois la surveillance activée et toutes les tâches de configuration terminées correctement, vous pouvez contrôler les performances de votre cluster de deux manières :

* Directement dans le cluster AKS en sélectionnant **Intégrité** dans le volet gauche.
* En cliquant sur la vignette **Conteneurs Monitor Insights** dans la page du cluster AKS pour le cluster sélectionné. Dans Azure Monitor, dans le volet gauche, sélectionnez **Intégrité**. 

  ![Options de sélection d’Azure Monitor pour les conteneurs dans AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité du cluster. 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>Activer la supervision des clusters gérés existants
Vous pouvez activer la supervision d’un cluster AKS déjà déployé, soit à l’aide d’Azure CLI, à partir du portail, soit à l’aide du modèle Azure Resource Manager fourni en utilisant la cmdlet PowerShell`New-AzureRmResourceGroupDeployment`. 

### <a name="enable-monitoring-using-azure-cli"></a>Activer la supervision à l’aide d’Azure CLI
L’étape suivante permet la supervision de votre cluster AKS à l’aide d’Azure CLI. Dans cet exemple, vous n'êtes pas obligé de créer ou de spécifier un espace de travail existant. Cette commande simplifie le processus en créant un espace de travail par défaut dans le groupe de ressources par défaut de l’abonnement cluster AKS s’il n’existe pas dans la région.  L’espace de travail créé par défaut est semblable au format de *DefaultWorkspace-<GUID>-<Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

La sortie doit ressembler à ce qui suit :

```azurecli
provisioningState       : Succeeded
```

Si vous préférez intégrer un espace de travail existant, utilisez la commande suivante pour spécifier celui-ci.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

La sortie doit ressembler à ce qui suit :

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-monitoring-from-azure-monitor"></a>Activer la supervision à partir d’Azure Monitor
Pour activer la supervision de votre cluster AKS dans le portail Azure à partir d’Azure Monitor, effectuez les étapes suivantes :

1. Dans le portail Azure, sélectionnez **Monitor**. 
2. Sélectionnez **Conteneurs (préversion)** dans la liste.
3. Sur la page **Monitor - Conteneurs (préversion)**, sélectionnez **Clusters non surveillés**.
4. Dans la liste des clusters non surveillés, recherchez le conteneur dans la liste et cliquez sur **Activer**.   
5. Sur la page **Onboarding to Container Health and Logs** (Intégration à la surveillance de l’intégrité des conteneurs et aux journaux), si vous disposez d’un espace de travail Log Analytics dans le même abonnement que le cluster, sélectionnez-le dans la liste déroulante.  
    La liste présélectionne l’espace de travail par défaut et l’emplacement où le conteneur AKS est déployé dans l’abonnement. 

    ![Activer la supervision des conteneurs Insights AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Si vous souhaitez créer un espace de travail Log Analytics pour stocker les données de surveillance du cluster, suivez les instructions de [Créer un espace de travail Log Analytics](../../log-analytics/log-analytics-quick-create-workspace.md). Veillez à créer l’espace de travail dans le même abonnement que le conteneur AKS. 
 
Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité du cluster. 

### <a name="enable-monitoring-from-aks-cluster-in-the-portal"></a>Activer la supervision à partir d’un cluster AKS dans le portail
Pour activer la surveillance de votre conteneur AKS à partir du portail Azure, effectuez les étapes suivantes :

1. Dans le portail Azure, sélectionnez **Tous les services**. 
2. Dans la liste des ressources, commencez à taper **Conteneurs**.  
    Au fur et à mesure de votre saisie, la liste est filtrée. 
3. Sélectionnez **Services Kubernetes**.  

    ![Lien vers les services Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. Dans la liste des conteneurs, sélectionnez un conteneur.
5. Dans la vue d’ensemble du conteneur, sélectionnez **Surveiller l’intégrité des conteneurs**.  
6. Dans la page **Onboarding to Container Health and Logs** (Intégration à la surveillance de l’intégrité des conteneurs et aux journaux), si vous disposez d’un espace de travail Log Analytics appartenant au même abonnement que le cluster, sélectionnez-le dans la liste déroulante.  
    La liste présélectionne l’espace de travail par défaut et l’emplacement où le conteneur AKS est déployé dans l’abonnement. 

    ![Activer le contrôle d’intégrité des conteneurs AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Si vous souhaitez créer un espace de travail Log Analytics pour stocker les données de surveillance du cluster, suivez les instructions de [Créer un espace de travail Log Analytics](../../log-analytics/log-analytics-quick-create-workspace.md). Veillez à créer l’espace de travail dans le même abonnement que le conteneur AKS. 
 
Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous ne puissiez voir les données opérationnelles du cluster. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Activer la surveillance à l’aide d’un modèle Azure Resource Manager
Cette méthode inclut deux modèles JSON. Le premier modèle spécifie la configuration permettant d’activer la surveillance, tandis que l’autre modèle contient des valeurs de paramètre que vous configurez pour spécifier les éléments suivants :

* L’ID de ressource du conteneur AKS 
* Le groupe de ressources dans lequel le cluster est déployé
* L’espace de travail et la région Log Analytics dans lesquels créer l’espace de travail 

>[!NOTE]
>Le modèle doit être déployé dans le même groupe de ressources que le cluster.
>

L’espace de travail Log Analytics doit être créé manuellement. Pour créer l’espace de travail, vous pouvez en configurer un via [Azure Resource Manager](../../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../../log-analytics/log-analytics-quick-create-workspace.md).

Si vous n’êtes pas familiarisé avec le déploiement de ressources à l’aide d’un modèle, consultez les rubriques suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI 2.0.27 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
        "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]", 
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
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "<workspaceLocation>"
       }
     }
    }
    ```

4. Modifiez **aksResourceId** et **aksResourceLocation** à l’aide des valeurs situées dans la page **Vue d’ensemble d’AKS** du cluster AKS. La valeur de **workspaceResourceId** est l’ID de ressource complet de votre espace de travail Log Analytics, qui inclut le nom de l’espace de travail. Spécifiez également l’emplacement de l’espace de travail pour **workspaceRegion**. 
5. Enregistrez ce fichier en tant que **existingClusterParam.json** dans un dossier local.
6. Vous êtes prêt à déployer ce modèle. 

    * Utilisez les commandes PowerShell suivantes dans le dossier qui contient le modèle :

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ClusterResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

        ```powershell
        provisioningState       : Succeeded
        ```

    * Pour exécuter la commande ci-dessous à l’aide d’Azure CLI :
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

        ```azurecli
        provisioningState       : Succeeded
        ```
Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité du cluster. 

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

Pour vérifier que la version de l’agent Log Analytics antérieure à la version *06072018* a été correctement déployée, exécutez la commande suivante :  

```
kubectl get ds omsagent --namespace=kube-system
```

La sortie doit ressembler à la suivante, qui indique que l’agent a été correctement déployé :  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Afficher la configuration avec l’interface de ligne de commande
Utilisez la commande `aks show` pour obtenir des détails comme l’activation ou non de la solution, le resourceID de l’espace de travail Log Analytics, et un résumé détaillé du cluster.  

```azurecli
az aks show -g <resoourceGroupofAKSCluster> -n <nameofAksCluster>
```

Au bout de quelques minutes, la commande se termine et renvoie des informations formatées JSON sur la solution.  Les résultats de la commande doivent afficher le profil de module complémentaire de supervision et ressemblent à l’exemple de sortie suivant :

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```
## <a name="next-steps"></a>Étapes suivantes

* Si vous rencontrez des problèmes en tentant d’intégrer la solution, consultez le [guide de résolution des problèmes](container-insights-troubleshoot.md)

* Vous pouvez activer la fonctionnalité de supervision pour collecter des métriques d’intégrité pour les nœuds et pods du cluster AKS et les consulter dans le portail Azure. Pour savoir comment utiliser Azure Monitor pour les conteneurs, consultez l’article [Connaître l’état d’Azure Kubernetes Service](container-insights-analyze.md).