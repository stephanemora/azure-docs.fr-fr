---
title: Configurer des clusters Kubernetes hybrides avec Azure Monitor pour les conteneurs | Microsoft Docs
description: Cet article explique comment configurer Azure Monitor pour les conteneurs afin de surveiller les clusters Kubernetes hébergés sur Azure Stack ou dans un autre environnement.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198052"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Configurer des clusters Kubernetes hybrides avec Azure Monitor pour les conteneurs

Azure Monitor pour les conteneurs offre une expérience d’analyse riche pour Azure Kubernetes Service (AKS) et [Moteur AKS sur Azure](https://github.com/Azure/aks-engine), qui est un cluster Kubernetes automanagé hébergé sur Azure. Cet article explique comment activer la surveillance des clusters Kubernetes hébergés en dehors d’Azure et obtenir une expérience de surveillance similaire.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

* Un espace de travail Log Analytics.

    Azure Monitor pour conteneurs prend en charge un espace de travail Log Analytics dans les régions répertoriées dans [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Pour créer votre propre espace de travail, vous pouvez utiliser [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../learn/quick-create-workspace.md).

    >[!NOTE]
    >L’analyse de plusieurs clusters avec le même nom de cluster dans le même espace de travail Log Analytics n’est pas prise en charge. Les noms de cluster doivent être uniques.
    >

* Vous êtes membre du **rôle de contributeur Log Analytics** pour activer la supervision des conteneurs. Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer l’accès à l’espace de travail et aux données de journal](../platform/manage-access.md)

* [Client HELM](https://helm.sh/docs/using_helm/) pour intégrer le graphique Azure Monitor pour conteneurs pour le cluster Kubernetes spécifié.

* Les informations de configuration du proxy et du pare-feu suivantes sont requises pour permettre à la version de l’agent Log Analytics pour Linux de communiquer avec Azure Monitor :

    |Ressource de l'agent|Ports |
    |------|---------|   
    |*.ods.opinsights.azure.com |Port 443 |  
    |*.oms.opinsights.azure.com |Port 443 |  
    |*.blob.core.windows.net |Port 443 |  
    |*.dc.services.visualstudio.com |Port 443 |

* L’agent en conteneur requiert l’ouverture de `cAdvisor secure port: 10250` ou `unsecure port :10255` de Kubelet sur tous les nœuds du cluster pour collecter les mesures de performance. Nous vous recommandons de configurer `secure port: 10250` sur le cAdvisor de Kubelet s’il n’est pas déjà configuré.

* L’agent en conteneur requiert que les variables d’environnement suivantes soient spécifiées sur le conteneur pour pouvoir communiquer avec le service d’API Kubernetes au sein du cluster afin de collecter les données d’inventaire : `KUBERNETES_SERVICE_HOST` et `KUBERNETES_PORT_443_TCP_PORT`.

>[!IMPORTANT]
>La version minimale prise en charge de l’agent pour la surveillance des clusters Kubernetes hybrides est ciprod10182019.

## <a name="supported-configurations"></a>Configurations prises en charge

Les éléments suivants sont officiellement pris en charge avec Azure Monitor pour les conteneurs.

- Environnements : Kubernetes local, Moteur AKS sur Azure et Azure Stack. Pour plus d’informations, consultez [Moteur AKS sur Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Les versions de Kubernetes et de la stratégie de support sont les mêmes que celles [prises en charge par AKS](../../aks/supported-kubernetes-versions.md).
- Runtime de conteneurs : Docker et Moby
- Version du système d’exploitation Linux pour le nœud principal et les nœuds de travail : Ubuntu (18.04 LTS et 16.04 LTS)
- Contrôle d’accès pris en charge : Kubernetes RBAC et non RBAC

## <a name="enable-monitoring"></a>Activer la supervision

L’activation d’Azure Monitor pour les conteneurs pour le cluster Kubernetes hybride consiste à effectuer les étapes suivantes dans l’ordre.

1. Configurez votre espace de travail Log Analytics avec la solution Container Insights.

2. Activez le graphique HELM Azure Monitor pour conteneurs avec l’espace de travail Log Analytics.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Comment ajouter la solution Azure Monitor pour conteneurs

Vous pouvez déployer la solution avec le modèle Azure Resource Manager fourni, à l’aide de la cmdlet Azure PowerShell `New-AzResourceGroupDeployment` ou avec Azure CLI.

Si vous n’êtes pas familiarisé avec le déploiement de ressources à l’aide d’un modèle, consultez les rubriques suivantes :

* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../../azure-resource-manager/templates/deploy-cli.md)

Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI version 2.0.59 ou une version ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Cette méthode inclut deux modèles JSON. Le premier modèle spécifie la configuration permettant d’activer la surveillance, tandis que l’autre modèle contient des valeurs de paramètre que vous configurez pour spécifier les éléments suivants :

- **workspaceResourceId** : ID de ressource complet de votre espace de travail Log Analytics.
- **workspaceRegion** : région dans laquelle l’espace de travail est créé, également appelé **Emplacement** dans les propriétés de l’espace de travail lors de l’affichage à partir du portail Azure.

Pour identifier l’ID de ressource complet de votre espace de travail Log Analytics requis pour la valeur du paramètre `workspaceResourceId` dans le fichier de **containerSolutionParams.json**, procédez comme suit, puis exécutez l’applet de commande PowerShell ou la commande Azure CLI pour ajouter la solution.

1. Affichez la liste de tous les abonnements auxquels vous avez accès à l’aide de la commande suivante :

    ```azurecli
    az account list --all -o table
    ```

    La sortie doit ressembler à ce qui suit :

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Copiez la valeur de **SubscriptionId**.

2. Basculez vers l’abonnement qui héberge l’espace de travail Log Analytics à l’aide de la commande suivante :

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. L’exemple suivant affiche la liste des espaces de travail de vos abonnements au format JSON par défaut.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Dans la sortie, recherchez le nom de l’espace de travail, puis copiez l’ID de ressource complet de cet espace de travail Log Analytics sous le champ **ID**.

4. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
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

5. Enregistrez ce fichier sous le nom containerSolution.json dans un dossier local.

6. Collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Modifiez les valeurs de **workspaceResourceId** à l’aide de la valeur que vous avez copiée à l’étape 3, et pour **workspaceRegion** copiez la valeur **Région** obtenue après avoir exécuté la commande Azure CLI [az monitor log-analytics workspace show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Enregistrez ce fichier sous le nom containerSolutionParams.json dans un dossier local.

9. Vous êtes prêt à déployer ce modèle.

   * Pour effectuer un déploiement avec Azure PowerShell, utilisez les commandes suivantes dans le dossier qui contient le modèle :

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

       ```powershell
       provisioningState       : Succeeded
       ```

   * Pour effectuer un déploiement avec Azure CLI, exécutez les commandes suivantes :

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

       ```azurecli
       provisioningState       : Succeeded
       ```

       Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité du cluster.

## <a name="install-the-chart"></a>Installer le graphique

Pour activer le graphique HELM, procédez comme suit :

1. Ajoutez le référentiel de graphiques Azure à votre liste locale en exécutant la commande suivante :

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Exécutez la commande suivante pour installer le graphique :

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Si l’espace de travail Log Analytics se trouve dans Azure Chine, exécutez la commande suivante :

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Si l’espace de travail Log Analytics se trouve dans Azure US Government, exécutez la commande suivante :

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Configurer la collecte de données de l’agent

Avec la version de graphique 1.0.0, les paramètres de collecte des données de l’agent sont contrôlés à partir de ConfigMap. Reportez-vous à la documentation sur les paramètres de collecte de données de l’agent [ici](container-insights-agent-config.md).

Une fois que vous avez correctement déployé le graphique, vous pouvez examiner les données de votre cluster Kubernetes hybride dans Azure Monitor pour conteneurs depuis le portail Azure.  

>[!NOTE]
>La latence d’ingestion est de cinq à dix minutes entre l’agent et la validation dans l’espace de travail Azure Log Analytics. L’état du cluster affiche la valeur **Pas de données** ou **Inconnu** jusqu’à ce que toutes les données d’analyse requises soient disponibles dans Azure Monitor.

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez une erreur lors de la tentative d’activation de la surveillance de votre cluster Kubernetes hybride, copiez le script PowerShell [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) et enregistrez-le dans un dossier sur votre ordinateur. Ce script est fourni pour aider à détecter et à résoudre les problèmes rencontrés. Les problèmes qu’il est conçu de détecter et tenter de corriger sont les suivants :

* L’espace de travail Log Analytics spécifié est valide
* L’espace de travail Log Analytics est configuré avec la solution Azure Monitor pour conteneurs. Si ce n’est pas le cas, configurez l’espace de travail.
* Les pods replicaset OmsAgent sont en cours d’exécution
* Les pods daemonset OmsAgent sont en cours d’exécution
* Le service Intégrité OmsAgent est en cours d’exécution
* L’ID et la clé de l’espace de travail Log Analytics configurés sur l’agent en conteneur correspondent à l’espace de travail avec lequel Insight est configuré.
* Vérifiez que tous les nœuds Worker Linux ont l’étiquette `kubernetes.io/role=agent` pour planifier le pod rs. Si ce n’est pas le cas, ajoutez-la.
* Vérifiez que `cAdvisor secure port:10250` ou `unsecure port: 10255` est ouvert sur tous les nœuds du cluster.

Pour exécuter avec Azure PowerShell, utilisez les commandes suivantes dans le dossier qui contient le script :

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Étapes suivantes

Une fois l’analyse activée pour collecter l’utilisation des ressources et l’intégrité de votre cluster Kubernetes hybride et des charges de travail s’y exécutant, découvrez [comment utiliser](container-insights-analyze.md) Azure Monitor pour les conteneurs.
