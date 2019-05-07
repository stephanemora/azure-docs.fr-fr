---
title: Surveiller un cluster Azure Kubernetes Service (AKS) déployé | Microsoft Docs
description: Découvrez comment activer la surveillance d’un cluster Azure Kubernetes Service (AKS) avec Azure Monitor pour les conteneurs déjà déployés dans votre abonnement.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: ae340fb11d422b7516cc315e78be974d22239503
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074679"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Activer la surveillance de cluster Azure Kubernetes Service (AKS) déjà déployé

Cet article décrit comment configurer Azure Monitor pour les conteneurs pour surveiller le cluster Kubernetes géré hébergé sur [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) qui ont déjà été déployés dans votre abonnement.

Vous pouvez activer la surveillance d’un cluster AKS qui a déjà été déployé à l’aide d’une des méthodes prises en charge :

* Azure CLI
* Terraform
* [À partir d’Azure Monitor](#enable-from-azure-monitor-in-the-portal) ou [directement depuis le cluster AKS](#enable-directly-from-aks-cluster-in-the-portal) dans le portail Azure 
* Avec le [fourni le modèle Azure Resource Manager](#enable-using-an-azure-resource-manager-template) à l’aide de l’applet de commande Azure PowerShell `New-AzResourceGroupDeployment` ou avec Azure CLI. 

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com). 

## <a name="enable-using-azure-cli"></a>Activer à l’aide d’Azure CLI

L’étape suivante permet la supervision de votre cluster AKS à l’aide d’Azure CLI. Dans cet exemple, vous n'êtes pas obligé de créer ou de spécifier un espace de travail existant. Cette commande simplifie le processus en créant un espace de travail par défaut dans le groupe de ressources par défaut de l’abonnement cluster AKS s’il n’existe pas dans la région.  L’espace de travail créé par défaut est semblable au format de *DefaultWorkspace-\<GUID>-\<Région>*.  

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

## <a name="enable-using-terraform"></a>Activer à l’aide de Terraform

1. Ajoutez le profil de module complémentaire **oms_agent** à la [ressource azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile) existante.

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Ajoutez [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) en suivant les étapes décrites dans la documentation Terraform.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Activer à partir d’Azure Monitor dans le portail 

Pour activer la supervision de votre cluster AKS dans le portail Azure à partir d’Azure Monitor, effectuez les étapes suivantes :

1. Dans le Portail Azure, sélectionnez **Surveiller**. 
2. Sélectionnez **Conteneurs** dans la liste.
3. Dans la page **Monitor - Conteneurs**, sélectionnez **Clusters non analysés**.
4. Dans la liste des clusters non surveillés, recherchez le conteneur dans la liste et cliquez sur **Activer**.   
5. Dans la page **Onboarding to Container Health and Logs** (Intégration de l’intégrité des conteneurs et aux journaux), si vous disposez d’un espace de travail Log Analytics dans le même abonnement que le cluster, sélectionnez-le dans la liste déroulante.  
    La liste présélectionne l’espace de travail par défaut et l’emplacement où le conteneur AKS est déployé dans l’abonnement. 

    ![Activer la supervision des conteneurs Insights AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Si vous souhaitez créer un espace de travail Log Analytics pour stocker les données de supervision du cluster, suivez les instructions de [Créer un espace de travail Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Veillez à créer l’espace de travail dans le même abonnement que le conteneur AKS. 
 
Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité du cluster. 

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Activer directement à partir de cluster AKS dans le portail

Pour activer la surveillance directement à partir de vos clusters AKS dans le portail Azure, procédez comme suit :

1. Dans le portail Azure, sélectionnez **Tous les services**. 
2. Dans la liste des ressources, commencez à taper **Conteneurs**.  
    Au fur et à mesure de votre saisie, la liste est filtrée. 
3. Sélectionnez **Services Kubernetes**.  

    ![Lien vers les services Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. Dans la liste des conteneurs, sélectionnez un conteneur.
5. Dans la vue d’ensemble du conteneur, sélectionnez **Conteneurs Monitor**.  
6. Dans la page **Intégration à Azure Monitor pour conteneurs**, si vous disposez d’un espace de travail Log Analytics dans le même abonnement que le cluster, sélectionnez-le dans la liste déroulante.  
    La liste présélectionne l’espace de travail par défaut et l’emplacement où le conteneur AKS est déployé dans l’abonnement. 

    ![Activer le contrôle d’intégrité des conteneurs AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Si vous souhaitez créer un espace de travail Log Analytics pour stocker les données de supervision du cluster, suivez les instructions de [Créer un espace de travail Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Veillez à créer l’espace de travail dans le même abonnement que le conteneur AKS. 
 
Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous ne puissiez voir les données opérationnelles du cluster. 

## <a name="enable-using-an-azure-resource-manager-template"></a>Activer à l’aide d’un modèle Azure Resource Manager

Cette méthode inclut deux modèles JSON. Le premier modèle spécifie la configuration permettant d’activer la surveillance, tandis que l’autre modèle contient des valeurs de paramètre que vous configurez pour spécifier les éléments suivants :

* L’ID de ressource du conteneur AKS 
* Le groupe de ressources dans lequel le cluster est déployé

>[!NOTE]
>Le modèle doit être déployé dans le même groupe de ressources que le cluster.
>

L’espace de travail Analytique de journal doit être créé avant d’activer la surveillance à l’aide d’Azure PowerShell ou CLI. Pour créer l’espace de travail, vous pouvez en configurer un via [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../../azure-monitor/learn/quick-create-workspace.md).

Si vous n’êtes pas familiarisé avec le déploiement de ressources à l’aide d’un modèle, consultez les rubriques suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI version 2.0.59 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-a-template"></a>Créer et exécuter un modèle

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
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       }  
     }
    }
    ```

4. Modifiez les valeurs pour **aksResourceId** et **aksResourceLocation** en utilisant les valeurs sur le **vue d’ensemble d’AKS** page pour le cluster AKS. La valeur de **workspaceResourceId** est l’ID de ressource complet de votre espace de travail Log Analytics, qui inclut le nom de l’espace de travail. 
5. Enregistrez ce fichier en tant que **existingClusterParam.json** dans un dossier local.
6. Vous êtes prêt à déployer ce modèle. 

   * Pour déployer avec Azure PowerShell, utilisez les commandes suivantes dans le dossier qui contient le modèle :

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

       ```powershell
       provisioningState       : Succeeded
       ```

   * Pour déployer avec Azure CLI, exécutez les commandes suivantes :
    
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
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
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
