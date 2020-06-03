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
ms.openlocfilehash: 962b6a17743ea2beed1e16503739c55c83babbce
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860543"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Connecter un cluster Kubernetes à extension Azure Arc (préversion)

Connectez un cluster Kubernetes à Azure Arc. 

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que les exigences suivantes sont remplies :

* Cluster Kubernetes en cours d’exécution.
* Accès avec kubeconfig et accès d’administrateur de cluster. 
* L’utilisateur ou le principal du service utilisé avec les commandes `az login` et `az connectedk8s connect` doit avoir les autorisations en lecture et en écriture sur le type de ressource « Microsoft. Kubernetes/connectedclusters ». Le rôle « Intégration d’Azure Arc pour Kubernetes » disposant de ces autorisations peut être utilisé pour les attributions de rôles sur l’utilisateur ou le principal du service utilisé avec Azure CLI pour l’intégration.
* Version la plus récente des extensions *connectedk8s* et *k8sconfiguration*

## <a name="supported-regions"></a>Régions prises en charge

* USA Est
* Europe Ouest

## <a name="network-requirements"></a>Configuration requise pour le réseau

Les agents Azure Arc requièrent les protocoles/ports/URL sortantes suivants pour fonctionner.

* TCP sur le port 443 --> `https://:443`
* TCP sur le port 9418 --> `git://:9418`

| Point de terminaison (DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Requis pour que l’agent se connecte à Azure et inscrive le cluster.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Point de terminaison du plan de données pour que l’agent transmette les informations de configuration d’état et de récupération.                                      |
| `https://docker.io`                                                                                            | Requis pour extraire des images de conteneur.                                                                                         |
| `https://github.com`, git://github.com                                                                         | Des exemples de dépôts GitOps sont hébergés sur GitHub. L’agent de configuration requiert une connectivité à un point de terminaison Git que vous spécifiez. |
| `https://login.microsoftonline.com`                                                                            | Requis pour extraire et mettre à jour des jetons Azure Resource. Manager                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Requis pour extraire des images de conteneur pour les agents Azure Arc.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Inscrire les deux fournisseurs pour Kubernetes à extension Azure Arc :

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

L’inscription est un processus asynchrone. L’inscription peut prendre environ 10 minutes. Vous pouvez surveiller le processus d’inscription à l’aide des commandes suivantes :

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-extensions"></a>Installer les extensions Azure CLI

Installez l’extension `connectedk8s` qui vous aide à connecter des clusters Kubernetes à Azure :

```console
az extension add --name connectedk8s
```

Installez l’extension `k8sconfiguration`.

```console
az extension add --name k8sconfiguration
```

Exécutez les commandes suivantes pour mettre à jour les extensions vers les versions les plus récentes.

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="create-a-resource-group"></a>Création d’un groupe de ressources

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

Nous allons ensuite connecter notre cluster Kubernetes à Azure. Le flux de travail pour `az connectedk8s connect` est le suivant :

1. Vérifiez la connectivité à votre cluster Kubernetes : via `KUBECONFIG`, `~/.kube/config` ou `--kube-config`
1. Déployez des agents Azure Arc pour Kubernetes en utilisant Helm 3, dans l’espace de noms `azure-arc`

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

Répertoriez vos clusters connectés :

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

Un Kubernetes à extension Azure Arc déploie quelques opérateurs dans l’espace de noms `azure-arc`. Vous pouvez afficher ces déploiements et pods ici :

```console
kubectl -n azure-arc get deployments,pods
```

**Output:**

```console
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

## <a name="azure-arc-agents-for-kubernetes"></a>Agents Azure Arc pour Kubernetes

Un Kubernetes à extension Azure Arc se compose de quelques agents (opérateurs) qui s’exécutent dans votre cluster déployé dans l’espace de noms `azure-arc`.

* `deployment.apps/config-agent` : surveille le cluster connecté pour les ressources de configuration de contrôle de code source appliquées au cluster, et met à jour l’état de conformité.
* `deployment.apps/controller-manager` : opérateur d’opérateurs orchestrant les interactions entre les composants Azure Arc.
* `deployment.apps/metrics-agent` : collecte les métriques d’autres agents Arc pour s’assurer que ces agents offrent des performances optimales.
* `deployment.apps/cluster-metadata-operator` : collecte des métadonnées de cluster (version du cluster, nombre de nœuds et version de l’agent Arc).
* `deployment.apps/resource-sync-agent` : synchronise les métadonnées de cluster précitées dans Azure.
* `deployment.apps/clusteridentityoperator` : gère le certificat d’identité de service managé (MSI) utilisé par d’autres agents pour la communication avec Azure.
* `deployment.apps/flux-logs-agent` : collecte des journaux des opérateurs de flux déployés dans le cadre de la configuration du contrôle de code source.

## <a name="delete-a-connected-cluster"></a>Supprimer un cluster connecté

Vous pouvez supprimer une ressource `Microsoft.Kubernetes/connectedcluster` à l’aide d’Azure CLI ou du portail Azure.

La commande Azure CLI `az connectedk8s delete` supprime la ressource `Microsoft.Kubernetes/connectedCluster` dans Azure. Azure CLI supprime toutes les ressources `sourcecontrolconfiguration` associées dans Azure. Azure CLI utilise une désinstallation de Helm pour supprimer les agents du cluster.

Le portail Azure supprime la ressource `Microsoft.Kubernetes/connectedcluster` dans Azure, ainsi que toutes les ressources `sourcecontrolconfiguration` associées dans Azure.

Pour supprimer les agents du cluster, vous devez exécuter `az connectedk8s delete` ou `helm uninstall azurearcfork8s`.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser GitOps dans un cluster connecté](./use-gitops-connected-cluster.md)
* [Utiliser Azure Policy pour gérer la configuration du cluster](./use-azure-policy.md)
