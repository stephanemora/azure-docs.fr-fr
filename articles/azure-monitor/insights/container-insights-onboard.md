---
title: Procédure d’intégration d’Azure Monitor pour les conteneurs | Microsoft Docs
description: Cet article décrit la façon dont vous intégrez et configurez Azure Monitor pour les conteneurs afin que vous puissiez comprendre les performances de votre conteneur et les problèmes liés aux performances identifiés.
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
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: 403cbeb0a68e39eab714ceb428fcfaefe8de0ff7
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576239"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Procédure d’intégration d’Azure Monitor pour les conteneurs  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Cet article explique comment configurer Azure Monitor les conteneurs en vue de surveiller les performances des charges de travail qui sont déployées dans un environnement Kubernetes et hébergées sur [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Azure Monitor pour conteneurs peut être activé pour un ou plusieurs déploiements d’AKS nouveaux ou existants, à l’aide des méthodes prises en charge suivantes :

* À partir du portail Azure, Azure PowerShell, ou avec Azure CLI
* Utiliser [Terraform et AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)


## <a name="prerequisites"></a>Conditions préalables 
Avant de commencer, vérifiez que vous disposez des éléments suivants :

- **Un espace de travail Analytique de journal.** Vous pouvez le créer lorsque vous activez la supervision de votre nouveau cluster AKS ou lorsque vous laissez l’expérience d’intégration créer un espace de travail par défaut dans le groupe de ressources par défaut de l’abonnement de cluster AKS. Si vous choisissez de le créer vous-même, vous pouvez le créer via [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../../azure-monitor/learn/quick-create-workspace.md).
- Vous êtes membre de la **rôle de contributeur Log Analytique** pour activer la surveillance de conteneur. Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../../azure-monitor/platform/manage-access.md).
- Vous êtes membre de la **[propriétaire](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-role.mds#owner)** rôle sur la ressource de cluster AKS. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Composants 

Votre capacité à superviser les performances dépend d’un agent Log Analytics conteneurisé pour Linux, spécialement développé pour Azure Monitor pour les conteneurs. Cet agent spécialisé collecte les données de performances et d’événements à partir de tous les nœuds du cluster, et l’agent est automatiquement déployé puis inscrit auprès de l’espace de travail Log Analytics spécifié pendant le déploiement. La version d’agent est microsoft/oms:ciprod04202018 ou ultérieure. Elle est représentée par une date au format suivant : *mmjjaaaa*. 

Lorsqu’une nouvelle version de l’agent est disponible, celui-ci est automatiquement mis à niveau sur vos clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). Pour suivre les versions publiées, consultez [Annonces des versions de l’agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Si vous avez déjà déployé un cluster AKS, activez la supervision à l’aide d’Azure CLI ou un modèle Azure Resource Manager fourni, comme indiqué plus loin dans cet article. Vous ne pouvez pas utiliser `kubectl` pour mettre à niveau, supprimer, redéployer ou déployer l’agent. Le modèle doit être déployé dans le même groupe de ressources que le cluster.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [Portail Azure](https://portal.azure.com). 

## <a name="enable-monitoring-for-a-new-cluster"></a>Activer la supervision d’un nouveau cluster
Pendant le déploiement, vous pouvez activer la supervision d’un nouveau cluster AKS dans le portail Azure, avec Azure CLI ou avec Terraform.  Pour activer à partir du portail, suivez les étapes décrites dans l’article de démarrage rapide [déployer un cluster Azure Kubernetes Service (AKS)](../../aks/kubernetes-walkthrough-portal.md) et suivez les étapes décrites dans la section **surveiller l’intégrité et les journaux**.  

>[!NOTE]
>Lorsque vous suivez les étapes décrites dans l’article de démarrage rapide pour activer l’analyse pour le cluster AKS à partir du portail, vous êtes invité à sélectionner un espace de travail Analytique de journal existant ou créez-en un. 

### <a name="enable-using-azure-cli"></a>Activer à l’aide d’Azure CLI
Pour activer la supervision d’un nouveau cluster AKS créé avec Azure CLI, suivez l’étape de l’article de démarrage rapide sous la section [Créer un cluster AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI version 2.0.59 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

### <a name="enable-using-terraform"></a>Activer à l’aide de Terraform
Si vous souhaitez [déployer un nouveau cluster AKS à l’aide de Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), vous devez spécifier les arguments nécessaires dans le profil [pour créer un espace de travail Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html), si vous n’en spécifiez pas un existant. 

>[!NOTE]
>Si vous choisissez d’utiliser Terraform, vous devez exécuter le fournisseur de gestion des ressources Azure Terraform version 1.17.0 ou ultérieure.

Pour ajouter Azure Monitor pour les conteneurs à l’espace de travail, consultez [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) et renseignez le profil en incluant [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) et en spécifiant **oms_agent**. 

Une fois la surveillance activée et toutes les tâches de configuration terminées correctement, vous pouvez contrôler les performances de votre cluster de deux manières :

* Directement dans le cluster AKS en sélectionnant **Intégrité** dans le volet gauche.
* En cliquant sur la vignette **Conteneurs Monitor Insights** dans la page du cluster AKS pour le cluster sélectionné. Dans Azure Monitor, dans le volet gauche, sélectionnez **Intégrité**. 

  ![Options de sélection d’Azure Monitor pour les conteneurs dans AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité du cluster. 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>Activer la supervision des clusters gérés existants
Vous pouvez activer la surveillance d’un cluster AKS qui a déjà été déployé à l’aide d’une des méthodes prises en charge :

* Azure CLI
* Terraform
* [À partir d’Azure Monitor](#enable-from-azure-monitor-in-the-portal) ou [directement depuis le cluster AKS](#enable-directly-from-aks-cluster-in-the-portal) dans le portail Azure 
* Avec le [fourni le modèle Azure Resource Manager](#enable-using-an-azure-resource-manager-template) à l’aide de l’applet de commande Azure PowerShell `New-AzResourceGroupDeployment` ou avec Azure CLI. 

### <a name="enable-using-azure-cli"></a>Activer à l’aide d’Azure CLI
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

### <a name="enable-using-terraform"></a>Activer à l’aide de Terraform
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

### <a name="enable-from-azure-monitor-in-the-portal"></a>Activer à partir d’Azure Monitor dans le portail 
Pour activer la supervision de votre cluster AKS dans le portail Azure à partir d’Azure Monitor, effectuez les étapes suivantes :

1. Dans le Portail Azure, sélectionnez **Surveiller**. 
2. Sélectionnez **Conteneurs** dans la liste.
3. Dans la page **Monitor - Conteneurs**, sélectionnez **Clusters non analysés**.
4. Dans la liste des clusters non surveillés, recherchez le conteneur dans la liste et cliquez sur **Activer**.   
5. Dans la page **Onboarding to Container Health and Logs** (Intégration de l’intégrité des conteneurs et aux journaux), si vous disposez d’un espace de travail Log Analytics dans le même abonnement que le cluster, sélectionnez-le dans la liste déroulante.  
    La liste présélectionne l’espace de travail par défaut et l’emplacement où le conteneur AKS est déployé dans l’abonnement. 

    ![Activer la supervision des conteneurs Insights AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Si vous souhaitez créer un espace de travail Log Analytics pour stocker les données de surveillance du cluster, suivez les instructions de [Créer un espace de travail Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Veillez à créer l’espace de travail dans le même abonnement que le conteneur AKS. 
 
Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité du cluster. 

### <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Activer directement à partir de cluster AKS dans le portail
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
    >Si vous souhaitez créer un espace de travail Log Analytics pour stocker les données de surveillance du cluster, suivez les instructions de [Créer un espace de travail Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Veillez à créer l’espace de travail dans le même abonnement que le conteneur AKS. 
 
Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous ne puissiez voir les données opérationnelles du cluster. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Activer à l’aide d’un modèle Azure Resource Manager
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
