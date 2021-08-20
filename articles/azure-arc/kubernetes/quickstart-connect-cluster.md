---
title: 'Démarrage rapide : Connecter un cluster Kubernetes existant à Azure Arc'
description: Dans ce guide de démarrage rapide, découvrez comment connecter un cluster Kubernetes avec Azure Arc.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 06/30/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli, devx-track-azurepowershell
keywords: Kubernetes, Arc, Azure, cluster
ms.openlocfilehash: 1464f7f2c9d38b859823ab99e52499642fa4af4b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113595837"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Démarrage rapide : Connecter un cluster Kubernetes existant à Azure Arc

Dans ce guide de démarrage rapide, vous allez découvrir les avantages d’utiliser Kubernetes avec Azure Arc ainsi que les étapes à effectuer pour connecter un cluster Kubernetes existant à Azure Arc. Pour avoir un aperçu conceptuel de la connexion de clusters à Azure Arc, consultez l’[article sur l’architecture des agents Kubernetes avec Azure Arc](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

* Un cluster Kubernetes fonctionnel. Si vous n'en avez pas, vous pouvez créer un cluster en utilisant l'une des options suivantes :
    * [Kubernetes dans Docker (KIND)](https://kind.sigs.k8s.io/)
    * Créer un cluster Kubernetes à l’aide de Docker pour [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) ou [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Cluster Kubernetes automanagé utilisant l'[API Cluster](https://cluster-api.sigs.k8s.io/user/quick-start.html)
    * Si vous souhaitez connecter un cluster OpenShift à Azure Arc, vous devez exécuter la commande suivante une seule fois sur votre cluster avant d’exécuter `az connectedk8s connect` :

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > Le cluster doit disposer d'au moins un nœud de système d'exploitation et d'une architecture de type `linux/amd64`. Les clusters uniquement dotés de nœuds `linux/arm64` ne sont pas encore pris en charge.

* Fichier `kubeconfig` et contexte pointant vers votre cluster.
* Autorisations d'accès en « lecture » et en « écriture » sur le type de ressource Kubernetes avec Azure Arc (`Microsoft.Kubernetes/connectedClusters`).

* Installez la [dernière version de Helm 3](https://helm.sh/docs/intro/install).

* [Procédez à l'installation ou à la mise à niveau d'Azure CLI](/cli/azure/install-azure-cli) vers la version >= 2.16.0
* Installez l'extension `connectedk8s` d'Azure CLI, version >= 1.0.0 :

  ```console
  az extension add --name connectedk8s
  ```
>[!NOTE]
> Pour les [**emplacements personnalisés**](./custom-locations.md) sur votre cluster, utilisez les régions USA Est ou Europe Ouest. Pour toutes les autres fonctionnalités Kubernetes avec Azure Arc, [sélectionnez n’importe quelle région dans cette liste](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Tant que le module PowerShell **Az.ConnectedKubernetes** est en préversion, vous devez l’installer séparément à l’aide de la cmdlet `Install-Module`.

```azurepowershell-interactive
Install-Module -Name Az.ConnectedKubernetes
```

* Un cluster Kubernetes fonctionnel. Si vous n'en avez pas, vous pouvez créer un cluster en utilisant l'une des options suivantes :
    * [Kubernetes dans Docker (KIND)](https://kind.sigs.k8s.io/)
    * Créer un cluster Kubernetes à l’aide de Docker pour [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) ou [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Cluster Kubernetes automanagé utilisant l'[API Cluster](https://cluster-api.sigs.k8s.io/user/quick-start.html)
    * Si vous souhaitez connecter un cluster OpenShift à Azure Arc, vous devez exécuter la commande suivante une seule fois sur votre cluster avant d’exécuter `New-AzConnectedKubernetes` :

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > Le cluster doit disposer d'au moins un nœud de système d'exploitation et d'une architecture de type `linux/amd64`. Les clusters uniquement dotés de nœuds `linux/arm64` ne sont pas encore pris en charge.

* Fichier `kubeconfig` et contexte pointant vers votre cluster.
* Autorisations d'accès en « lecture » et en « écriture » sur le type de ressource Kubernetes avec Azure Arc (`Microsoft.Kubernetes/connectedClusters`).

* Installez la [dernière version de Helm 3](https://helm.sh/docs/intro/install).

* [Azure PowerShell version 5.9.0 ou ultérieure](/powershell/azure/install-az-ps)

---

>[!NOTE]
> Pour les [**emplacements personnalisés**](./custom-locations.md) sur votre cluster, utilisez les régions USA Est ou Europe Ouest. Pour toutes les autres fonctionnalités Kubernetes avec Azure Arc, [sélectionnez n’importe quelle région dans cette liste](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="meet-network-requirements"></a>Répondre à la configuration réseau nécessaire

> [!IMPORTANT]
> Les agents Azure Arc ont besoin de tous les protocoles/ports/URL sortantes suivants pour fonctionner :
> * TCP sur le port 443 : `https://:443`

| Point de terminaison (DNS) | Description |
| ----------------- | ------------- |
| `https://management.azure.com` (pour le Cloud Azure), `https://management.usgovcloudapi.net` (pour Azure US Government) | Requis pour que l’agent se connecte à Azure et inscrive le cluster. |
| `https://<region>.dp.kubernetesconfiguration.azure.com` (pour le Cloud Azure), `https://<region>.dp.kubernetesconfiguration.azure.us` (pour Azure US Government) | Point de terminaison du plan de données permettant à l’agent d’envoyer (push) le statut et de récupérer (fetch) les informations de configuration. |
| `https://login.microsoftonline.com` (pour le Cloud Azure), `https://login.microsoftonline.us` (pour Azure US Government) | Requis pour extraire et mettre à jour des jetons Azure Resource Manager. |
| `https://mcr.microsoft.com` | Requis pour extraire des images conteneurs pour les agents Azure Arc.                                                                  |
| `https://gbl.his.arc.azure.com` |  Requis pour obtenir le point de terminaison régional pour l’extraction des certificats Managed Service Identity (MSI) attribués par le système. |
| `https://<region-code>.his.arc.azure.com` (pour le Cloud Azure), `https://usgv.his.arc.azure.us` (pour Azure US Government) |  Indispensable à l’extraction des certificats attribués par le système dans le cadre des identités managées pour les ressources Azure. Mappage de `<region-code>` pour les régions du cloud Azure : `eus` (USA Est), `weu` (Europe Ouest), `wcus` (USA Centre-Ouest), `scus` (USA Centre Sud), `sea` (Asie Sud-Est), `uks` (Royaume-Uni Sud), `wus2` (USA Ouest 2), `ae` (Australie Est), `eus2` (USA Est 2), `ne` (Europe Nord), `fc` (France Centre). |

## <a name="1-register-providers-for-azure-arc-enabled-kubernetes"></a>1. Inscrire les fournisseurs pour Kubernetes avec Azure Arc

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Entrez les commandes suivantes :
    ```console
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Supervisez le processus d’inscription. L’inscription peut prendre jusqu’à 10 minutes.
    ```console
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

1. Entrez les commandes suivantes :
    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```
1. Supervisez le processus d’inscription. L’inscription peut prendre jusqu’à 10 minutes.
    ```azurepowershell-interactive
    Get-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```

---

## <a name="2-create-a-resource-group"></a>2. Créer un groupe de ressources

Exécutez la commande suivante :

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```console
az group create --name AzureArcTest --location EastUS --output table
```

Sortie :
<pre>
Location    Name
----------  ------------
eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name AzureArcTest -Location EastUS
```

Sortie :
<pre>
ResourceGroupName : AzureArcTest
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureArcTest
</pre>

---

## <a name="3-connect-an-existing-kubernetes-cluster"></a>3. Connecter un cluster Kubernetes existant

Exécutez la commande suivante :

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Sortie :
<pre>
Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
</pre>

> [!TIP]
> La commande ci-dessus sans paramètre d’emplacement spécifié crée la ressource Kubernetes avec Azure Arc dans le même emplacement que le groupe de ressources. Pour créer la ressource Kubernetes avec Azure Arc dans un emplacement différent, spécifiez `--location <region>` ou `-l <region>` lors de l’exécution de la commande `az connectedk8s connect`.

> [!NOTE]
> Si vous êtes connecté à Azure CLI avec un principal de service, vous devez définir un [paramètre supplémentaire](troubleshooting.md#enable-custom-locations-using-service-principal) pour activer la fonctionnalité d’emplacement personnalisé sur le cluster.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest -Location eastus
```

Sortie :
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

## <a name="4-verify-cluster-connection"></a>4. Vérifier la connexion du cluster

Exécutez la commande suivante :

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```console
az connectedk8s list --resource-group AzureArcTest --output table
```

Sortie :
<pre>
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzConnectedKubernetes -ResourceGroupName AzureArcTest
```

Sortie :
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

> [!NOTE]
> Après l’intégration du cluster, il faut environ 5 à 10 minutes pour que les métadonnées du cluster (version du cluster, version de l’agent, nombre de nœuds, etc.) soient visibles sur la page Vue d’ensemble de la ressource Kubernetes avec Azure Arc sur le Portail Azure.

## <a name="5-connect-using-an-outbound-proxy-server"></a>5. Établir la connexion via un serveur proxy sortant

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Si votre cluster se trouve derrière un serveur proxy sortant, Azure CLI et les agents Kubernetes avec Azure Arc doivent router leurs requêtes via ce serveur proxy sortant.

1. Définissez les variables d’environnement nécessaires pour Azure CLI afin d’utiliser le serveur proxy sortant :

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

2. Exécutez la commande connect avec les paramètres de proxy spécifiés :

    ```console
    az connectedk8s connect --name <cluster-name> --resource-group <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Spécifiez `excludedCIDR` sous `--proxy-skip-range` pour vérifier si la communication des agents au sein du cluster n’est pas interrompue.
> * `--proxy-http`, `--proxy-https` et `--proxy-skip-range` sont attendus pour la plupart des environnements de serveurs proxy sortants. `--proxy-cert` est nécessaire *uniquement* si vous devez injecter les certificats approuvés attendus par le serveur proxy dans le magasin de certificats approuvés des pods d’agents.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Si votre cluster se trouve derrière un serveur proxy sortant, Azure PowerShell et les agents Kubernetes avec Azure Arc doivent acheminer leurs requêtes via ce serveur proxy sortant.

1. Définissez les variables d’environnement nécessaires pour Azure PowerShell afin d’utiliser le serveur proxy sortant :

    * Exécutez la commande suivante avec les valeurs appropriées :

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. Exécutez la commande connect avec les paramètres de proxy spécifiés :

    ```azurepowershell-interactive
    New-AzConnectedKubernetes -ClusterName <cluster-name> -ResourceGroupName <resource-group> -Location eastus -Proxy 'https://<proxy-server-ip-address>:<port>'
    ```

---

## <a name="6-view-azure-arc-agents-for-kubernetes"></a>6. Voir les agents Azure Arc pour Kubernetes

Un Kubernetes à extension Azure Arc déploie quelques opérateurs dans l’espace de noms `azure-arc`.

1. Visualisez ces déploiements et ces pods à l’aide de :

    ```console
    kubectl get deployments,pods -n azure-arc
    ```

1. Vérifiez que tous les pods sont à l’état `Running`.

    Sortie :
    <pre>

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
    </pre>

## <a name="7-clean-up-resources"></a>7. Nettoyer les ressources

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez supprimer la ressource Kubernetes avec Azure Arc, toutes les ressources de configuration associées *et* tous les agents s’exécutant sur le cluster à l’aide d’Azure CLI et de la commande suivante :

```console
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
> La suppression de la ressource Kubernetes avec Azure Arc à l’aide du portail Azure entraîne la suppression de toutes les ressources de configuration associées mais *pas* des agents s’exécutant sur le cluster. Il est recommandé de suivre la bonne pratique qui consiste à supprimer la ressource Kubernetes avec Azure Arc via `az connectedk8s delete` au d’utiliser le portail Azure.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Vous pouvez supprimer la ressource Kubernetes avec Azure Arc, toutes les ressources de configuration associées *et* tous les agents s’exécutant sur le cluster à l’aide d’Azure PowerShell et de la commande suivante :

```azurepowershell-interactive
Remove-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest
```

>[!NOTE]
> La suppression de la ressource Kubernetes avec Azure Arc à l’aide du portail Azure entraîne la suppression de toutes les ressources de configuration associées mais *pas* des agents s’exécutant sur le cluster. Il est recommandé de suivre la bonne pratique qui consiste à supprimer la ressource Kubernetes avec Azure Arc via `Remove-AzConnectedKubernetes` au d’utiliser le portail Azure.

---

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment déployer des configurations sur votre cluster Kubernetes connecté à l’aide de GitOps.
> [!div class="nextstepaction"]
> [Déployer des configurations à l’aide de GitOps](tutorial-use-gitops-connected-cluster.md)
