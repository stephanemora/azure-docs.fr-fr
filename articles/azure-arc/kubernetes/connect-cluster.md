---
title: Connecter un cluster Kubernetes à extension Azure Arc (préversion)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Connecter un cluster Kubernetes à extension Azure Arc avec Azure Arc
keywords: Kubernetes, Arc, Azure, K8s, conteneurs
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b4ab84153eaaf81c668d8589fec7516853aca5f9
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008109"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Connecter un cluster Kubernetes à extension Azure Arc (préversion)

Cet article décrit le processus de connexion à Azure Arc d’un cluster Kubernetes certifié CNCF (Cloud Native Computing Foundation) comme AKS-engine sur Azure, AKS-engine sur Azure Stack Hub, GKE, EKS ou VMware vSphere.

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez préparé les prérequis suivants :

* Un cluster Kubernetes fonctionnel. Si vous ne disposez pas d’un cluster Kubernetes, vous pouvez utiliser l’un des guides suivants pour créer un cluster de test :
  * Créez un cluster Kubernetes à l’aide de [Kubernetes dans Docker (type)](https://kind.sigs.k8s.io/).
  * Créez un cluster Kubernetes à l’aide de Docker pour [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) ou [Windows](https://docs.docker.com/docker-for-windows/#kubernetes).
* Un fichier kubeconfig permettant d’accéder au cluster et au rôle d’administrateur de cluster sur le cluster pour le déploiement d’agents Kubernetes avec Arc.
* L’utilisateur ou le principal du service utilisé avec les commandes `az login` et `az connectedk8s connect` doit avoir les autorisations en lecture et en écriture sur le type de ressource « Microsoft. Kubernetes/connectedclusters ». Le rôle « Intégration d’Azure Arc pour cluster Kubernetes » disposant de ces autorisations peut être utilisé pour les attributions de rôles sur l’utilisateur ou le principal du service.
* Helm 3 pour l’intégration du cluster à l’aide d’une extension connectedk8s. [Installez la dernière version de Helm 3](https://helm.sh/docs/intro/install) pour répondre à cette exigence.
* Version 2.15 ou ultérieure d’Azure CLI pour installer les extensions Kubernetes CLI avec Azure Arc. [Installez Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) ou mettez-la à jour.
* Installez les extensions Kubernetes CLI avec Arc :
  
  * Installez l’extension `connectedk8s` qui vous aide à connecter des clusters Kubernetes à Azure :
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * Installez l’extension `k8sconfiguration`.
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Si vous souhaitez mettre à jour ces extensions ultérieurement, exécutez les commandes suivantes :
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Régions prises en charge

* USA Est
* Europe Ouest

## <a name="network-requirements"></a>Configuration requise pour le réseau

Les agents Azure Arc requièrent les protocoles/ports/URL sortantes suivants pour fonctionner :

* TCP sur le port 443 : `https://:443`
* TCP sur le port 9418 : `git://:9418`

| Point de terminaison (DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Requis pour que l’agent se connecte à Azure et inscrive le cluster.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Point de terminaison du plan de données permettant à l’agent d’envoyer (push) le statut et de récupérer (fetch) les informations de configuration.                                      |
| `https://login.microsoftonline.com`                                                                            | Requis pour extraire et mettre à jour des jetons Azure Resource Manager.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Requis pour extraire des images conteneurs pour les agents Azure Arc.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Requis pour tirer (pull) les certificats d’identité managée affectée par le système.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Inscription des deux fournisseurs pour Kubernetes avec Azure Arc

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

L’inscription est un processus asynchrone qui peut prendre environ 10 minutes. Vous pouvez surveiller le processus d’inscription à l’aide des commandes suivantes :

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Utilisez un groupe de ressources pour stocker les métadonnées pour votre cluster.

Tout d’abord, créez un groupe de ressources pour contenir la ressource de cluster connectée.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Output:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Connecter un cluster

Nous allons ensuite connecter notre cluster Kubernetes à Azure avec `az connectedk8s connect` :

1. Vérifiez la connectivité à votre cluster Kubernetes avec l’une des commandes suivantes :
   1. `KUBECONFIG`
   1. `~/.kube/config`
   1. `--kube-config`
1. Déployez des agents Azure Arc pour Kubernetes avec Helm 3 dans l’espace de noms `azure-arc` :

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Output:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Vérifier un cluster connecté

Utilisez la commande suivante pour afficher la liste de vos clusters connectés :

```console
az connectedk8s list -g AzureArcTest -o table
```

**Output:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Vous pouvez également afficher cette ressource dans le [Portail Azure](https://portal.azure.com/). Ouvrez le portail dans votre navigateur et accédez au groupe de ressources et à la ressource Kubernetes avec Azure Arc en fonction des entrées de nom de ressource et de nom du groupe de ressources utilisées plus tôt dans la commande `az connectedk8s connect`.

> [!NOTE]
> Après l’intégration du cluster, il faut environ 5 à 10 minutes pour que les métadonnées du cluster (version du cluster, version de l’agent, nombre de nœuds, etc.) soient visibles sur la page Vue d’ensemble de la ressource Kubernetes avec Azure Arc sur le Portail Azure.

## <a name="connect-using-an-outbound-proxy-server"></a>Se connecter à l’aide d’un serveur proxy sortant

Si votre cluster se trouve derrière un serveur proxy sortant, Azure CLI et les agents Kubernetes avec Arc doivent acheminer leurs demandes via ce serveur proxy :

1. Vérifiez la version de l’extension `connectedk8s` installée sur votre ordinateur :

    ```console
    az -v
    ```

    Vous devez disposer de la version 0.2.5 de l’extension `connectedk8s` ou d’une version ultérieure pour configurer des agents avec un proxy sortant. Si vous possédez la version 0.2.3 ou une version antérieure, suivez la [procédure de mise à jour](#before-you-begin) pour obtenir la dernière version de l’extension sur votre ordinateur.

2. Définissez les variables d’environnement nécessaires pour Azure CLI afin d’utiliser le serveur proxy sortant :

    * Si vous utilisez bash, exécutez la commande suivante avec les valeurs appropriées :

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Si vous utilisez PowerShell, exécutez la commande suivante avec les valeurs appropriées :

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

3. Exécutez la commande connect avec les paramètres de proxy spécifiés :

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> 1. Il est important de spécifier `excludedCIDR` sous `--proxy-skip-range` pour éviter que la communication dans le cluster ne soit interrompue pour les agents.
> 2. Tandis que les valeurs `--proxy-http`, `--proxy-https` et `--proxy-skip-range` sont attendues pour la plupart des environnements de proxy sortants, `--proxy-cert` n’est requis que si des certificats de confiance provenant du proxy doivent être injectés dans le magasin de certificats de confiance des pods d’agent.
> 3. La spécification de proxy ci-dessus est actuellement appliquée uniquement pour les agents Arc et non pour les pods de flux utilisés dans sourceControlConfiguration. L’équipe Kubernetes avec Arc travaille activement sur cette fonctionnalité, qui sera bientôt disponible.

## <a name="azure-arc-agents-for-kubernetes"></a>Agents Azure Arc pour Kubernetes

Kubernetes avec Azure Arc déploie quelques opérateurs dans l’espace de noms `azure-arc`. Pour afficher ces déploiements et ces pods, utilisez la commande suivante :

```console
kubectl -n azure-arc get deployments,pods
```

**Output:**

```console
NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
deployment.apps/cluster-metadata-operator     1/1             1        1      16h
deployment.apps/clusteridentityoperator       1/1             1        1      16h
deployment.apps/config-agent                  1/1             1        1      16h
deployment.apps/controller-manager            1/1             1        1      16h
deployment.apps/flux-logs-agent               1/1             1        1      16h
deployment.apps/metrics-agent                 1/1             1        1      16h
deployment.apps/resource-sync-agent           1/1             1        1      16h

NAME                                           READY    STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Kubernetes avec Azure Arc se compose de quelques agents (opérateurs) qui s’exécutent dans votre cluster déployé dans l’espace de noms `azure-arc`.

| Agents (opérateurs)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `deployment.apps/config-agent`                                                                                 | Surveille le cluster connecté pour les ressources de configuration du contrôle de code source appliquées au cluster et met à jour l’état de conformité.                                                        |
| `deployment.apps/controller-manager` | Opérateur d’opérateurs orchestrant les interactions entre les composants Azure Arc.                                      |
| `deployment.apps/metrics-agent`                                                                            | Collecte les métriques de performances d’autres agents Arc.                                                                                    |
| `deployment.apps/cluster-metadata-operator`                                                                            | Collecte les métadonnées du cluster, notamment sa version, le nombre de nœuds et la version de l’agent Azure Arc.                                                                  |
| `deployment.apps/resource-sync-agent`                                                                            |  Synchronise les métadonnées de cluster mentionnées ci-dessus dans Azure.                                                                  |
| `deployment.apps/clusteridentityoperator`                                                                            |  Kubernetes avec Azure Arc prend actuellement en charge l’identité affectée par le système. `clusteridentityoperator` gère le certificat MSI (Managed Service Identity) utilisé par d’autres agents pour la communication avec Azure.                                                                  |
| `deployment.apps/flux-logs-agent`                                                                            |  Collecte des journaux des opérateurs de flux déployés dans le cadre de la configuration du contrôle de code source.                                                                  |

## <a name="delete-a-connected-cluster"></a>Supprimer un cluster connecté

Vous pouvez supprimer une ressource `Microsoft.Kubernetes/connectedcluster` à l’aide d’Azure CLI ou du portail Azure.


* **Suppression avec Azure CLI** : Utilisez la commande Azure CLI suivante pour lancer la suppression d’une ressource Kubernetes avec Azure Arc.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Cette commande supprime la `Microsoft.Kubernetes/connectedCluster`ressource et toute ressource qui y est associée`sourcecontrolconfiguration` dans Azure. Azure CLI utilise `helm uninstall` pour supprimer également les agents en cours d’exécution sur le cluster.

* **Suppression dans le portail Azure** : La suppression de la ressource Kubernetes avec Azure Arc sur le Portail Azure permet de supprimer la ressource `Microsoft.Kubernetes/connectedcluster` et toutes les ressources `sourcecontrolconfiguration` associées dans Azure, mais *non* les agents en cours d’exécution sur le cluster. 

  Pour supprimer ces agents, exécutez la commande suivante :

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser GitOps dans un cluster connecté](./use-gitops-connected-cluster.md)
* [Utiliser Azure Policy pour gérer la configuration du cluster](./use-azure-policy.md)
