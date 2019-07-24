---
title: Utiliser une adresse IP statique avec l’équilibrage de charge d’Azure Kubernetes Service (AKS)
description: Découvrez comment créer et utiliser une adresse IP statique avec l’équilibreur de charge d’Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 9e32715766734bcbb150d70aeed2dc5b06a4bcbb
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614466"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Utiliser une adresse IP statique avec l’équilibreur de charge d’Azure Kubernetes Service (AKS)

Par défaut, l’adresse IP publique affectée à une ressource d’équilibreur de charge créée par un cluster AKS est valide seulement pour la durée de vie de cette ressource. Si vous supprimez le service Kubernetes, l’adresse IP et l’équilibreur de charge associés sont également supprimés. Si vous voulez affecter une adresse IP spécifique ou conserver une adresse IP pour des services Kubernetes redéployés, vous pouvez créer et utiliser une adresse IP publique statique.

Cet article vous montre comment créer une adresse IP publique statique et l’affecter à votre service Kubernetes.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Azure CLI 2.0.59 (ou une version ultérieure) doit également être installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez  [Installer Azure CLI][install-azure-cli].

Actuellement, seule la *référence SKU d’adresse IP de base* est prise en charge. Nous travaillons actuellement à la prise en charge de la référence SKU de ressource d’*adresse IP standard*. Pour plus d’informations, consultez [Types d’adresses IP et méthodes d’allocation dans Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Créer une adresse IP statique

Quand vous créez une adresse IP publique statique pour l’utiliser avec AKS, la ressource d’adresse IP doit de préférence être créée dans le groupe de ressources du **nœud**. Si vous souhaitez séparer les ressources, consultez la section suivante pour [utiliser une adresse IP statique en dehors du groupe de ressources du nœud](#use-a-static-ip-address-outside-of-the-node-resource-group).

Récupérez d’abord le nom du groupe de ressources du nœud avec la commande [az aks show][az-aks-show] et ajoutez le paramètre de requête `--query nodeResourceGroup`. L’exemple suivant obtient les le groupe de ressources du nœud pour le cluster AKS nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Créez maintenant une adresse IP publique statique avec la commande [az network public ip create][az-network-public-ip-create]. Spécifiez le nom de groupe de ressources du nœud obtenue dans la commande précédente, puis un nom pour la ressource d’adresse IP, par exemple *myAKSPublicIP* :

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

L’adresse IP apparaît, comme illustré dans l’exemple de sortie condensée suivante :

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Vous pouvez obtenir ultérieurement l’adresse IP publique avec la commande [az network public-ip list][az-network-public-ip-list]. Spécifiez le nom du groupe de ressources du nœud et l’adresse IP publique que vos avez créés, puis recherchez *ipAddress* comme indiqué dans l’exemple suivant :

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Créer un service utilisant l’adresse IP statique

Pour créer un service avec l’adresse IP publique statique, ajoutez la propriété `loadBalancerIP` et la valeur de l’adresse IP publique statique au manifeste YAML. Créez un fichier nommé `load-balancer-service.yaml` et copiez-y le YAML suivant. Spécifiez votre propre adresse IP publique créée à l’étape précédente.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Créez le service et le déploiement avec la commande `kubectl apply`.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Utiliser une adresse IP statique en dehors du groupe de ressources de nœud

Avec Kubernetes 1.10 ou version ultérieure, vous pouvez utiliser une adresse IP statique créée en dehors du groupe de ressources du nœud. Le principal de service utilisé par le cluster AKS doit disposer d’autorisations déléguées sur l’autre groupe de ressources, comme indiqué dans l’exemple suivant :

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Pour utiliser une adresse IP en dehors du groupe de ressources de nœud, ajoutez une annotation à la définition de service. L’exemple suivant définit l’annotation pour le groupe de ressources nommé *myResourceGroup*. Indiquez le nom de votre propre groupe de ressources :

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>Résolution des problèmes

Si l’adresse IP statique définie dans la propriété *loadBalancerIP* du manifeste du service Kubernetes n’existe pas ou n’a pas été créée dans le groupe de ressources du nœud et qu’aucune autre délégation n’est configurée, la création du service d’équilibreur de charge échoue. Pour résoudre les problèmes, examinez les événements de création du service avec la commande [kubectl describe][kubectl-describe]. Spécifiez le nom du service tel qu’il est spécifié dans le manifeste YAML, comme indiqué dans l’exemple suivant :

```console
kubectl describe service azure-load-balancer
```

Les informations sur la ressource de service Kubernetes sont affichées. Les *événements* à la fin de l’exemple de sortie suivant indiquent que *l’adresse IP fournie par l’utilisateur est introuvable*. Dans ces scénarios, vérifiez que vous avez créé l’adresse IP publique statique dans le groupe de ressources du nœud et que l’adresse IP spécifiée dans le manifeste du service Kubernetes est correct.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus de contrôle du trafic réseau vers vos applications, vous pouvez plutôt [créer un contrôleur d’entrée][aks-ingress-basic]. You can also [create an ingress controller with a static public IP address][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
