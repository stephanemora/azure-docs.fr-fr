---
title: Utiliser un équilibreur de charge de référence (SKU) Standard dans Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser un équilibreur de charge avec une référence SKU Standard pour exposer vos services avec Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/05/2019
ms.author: zarhoads
ms.openlocfilehash: d2a0ff5db6707c4f765c71937a7d0f0749401959
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172196"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Utiliser un équilibreur de charge de référence (SKU) Standard dans Azure Kubernetes Service (AKS)

Pour restreindre l’accès à vos applications dans Azure Kubernetes Service (AKS), vous pouvez créer et utiliser un Azure Load Balancer. Un équilibreur de charge exécuté sur AKS peut être utilisé comme équilibreur de charge interne ou externe. Un équilibreur de charge interne rend un service Kubernetes accessible uniquement aux applications qui s’exécutent dans le même réseau virtuel que le cluster AKS. Un équilibreur de charge externe reçoit une ou plusieurs adresses IP publiques pour l’entrée et rend un service Kubernetes accessible en externe en utilisant des adresses IP publiques.

Azure Load Balancer se décline en deux références SKU : *De base* et *Standard*. Par défaut, la référence SKU *De base* est utilisée lorsqu’un manifeste de service est utilisé pour créer un équilibreur de charge sur AKS. L’utilisation d’un équilibreur de charge de référence SKU *Standard* fournit des fonctionnalités supplémentaires, comme une plus grande taille pour le pool principal, et les zones de disponibilité. Il est important de comprendre les différences entre les équilibreurs de charge *Standard* et *De base* avant de choisir lequel utiliser. Une fois que vous créez un cluster AKS, vous ne pouvez pas modifier la référence SKU de l’équilibreur de charge pour ce cluster. Pour plus d’informations sur les références SKU *De base* et *Standard*, consultez [Comparaison des références SKU des équilibreurs de charge Azure][azure-lb-comparison].

Cet article explique comment créer et utiliser un Azure Load Balancer avec la référence SKU *Standard* avec Azure Kubernetes Service (AKS).

Cet article suppose une compréhension élémentaire des concepts de Kubernetes et d’Azure Load Balancer. Pour plus d’informations, consultez [Concepts de base de Kubernetes pour AKS (Azure Kubernetes Service)][kubernetes-concepts] et [Qu’est-ce qu’Azure Load Balancer ?][azure-lb].

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cet article vous demande d’exécuter Azure CLI version 2.0.59 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Avant de commencer

Le principal de service du cluster AKS a besoin de l’autorisation de gérer les ressources réseau si vous utilisez un sous-réseau ou un groupe de ressources existant. De façon générale, attribuez le rôle *Contributeur de réseau* au principal de service sur les ressources déléguées. Pour plus d’informations sur les autorisations, consultez [Déléguer l’accès à AKS à d’autres ressources Azure][aks-sp].

Vous devez créer un cluster AKS qui définit la référence SKU pour l’équilibreur de charge sur *Standard* au lieu de la valeur par défaut, *De base*.

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Pour utiliser la référence (SKU) d’équilibreur de charge Azure standard, vous avez besoin de l’extension de CLI *aks-preview* version 0.4.12 ou ultérieure. Installez l’extension Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add], puis recherchez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous créez et gérez des clusters AKS prenant un charge un équilibreur de charge avec la référence SKU *Standard* :

* Au moins une adresse IP publique ou un préfixe IP sont requis pour autoriser le trafic sortant du cluster AKS. L’adresse IP publique et le préfixe IP sont également nécessaires pour maintenir la connectivité entre le plan de contrôle et les nœuds d’agent, ainsi que pour assurer la compatibilité avec les versions précédentes d’AKS. Pour spécifier des adresses IP publiques ou des préfixes IP avec un équilibreur de charge de référence (SKU) *Standard*, vous disposez des options suivantes :
    * Fournir vos propres adresses IP publiques.
    * Fournir vos propres préfixes IP publics.
    * Spécifier un nombre de 1 à 100 pour autoriser le cluster AKS à créer ce nombre d’adresses IP publiques de référence (SKU) *Standard* dans le groupe de ressources créé en tant que cluster AKS, dont le nom commence généralement par *MC_* . AKS attribue l’adresse IP publique pour l’équilibreur de charge de référence SKU *Standard*. Par défaut, une adresse IP publique est automatiquement créée dans le même groupe de ressources que le cluster AKS si aucune adresse IP publique, aucun préfixe IP public ou aucun nombre d’adresses IP ne sont spécifiés. Vous devez également autoriser des adresses publiques et éviter de créer une Azure Policy interdisant la création d’adresses IP.
* Lorsque vous utilisez la référence (SKU) *Standard* pour un équilibreur de charge, vous devez utiliser Kubernetes version 1.13 ou ultérieure.
* Vous ne pouvez définir la référence (SKU) d’équilibreur de charge que lorsque vous créez un cluster AKS. Vous ne pouvez pas modifier la référence SKU de l’équilibreur de charge après la création d’un cluster AKS.
* Vous pouvez utiliser qu’une seule référence (SKU) d’équilibreur de charge dans un même cluster.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées. Lorsque vous créez un groupe de ressources, vous devez spécifier un emplacement. Il s’agit de l’emplacement de stockage des métadonnées de groupe de ressources. C’est également là que vos ressources s’exécutent dans Azure si vous ne spécifiez pas une autre région lors de la création de ressources. Créez un groupe de ressources avec la commande [az group create][az-group-create].

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

L’exemple de sortie suivant montre que le groupe de ressources a été créé correctement :

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>Créer un cluster ACS
Pour exécuter un cluster AKS qui prend en charge un équilibreur de charge avec la référence SKU *Standard*, votre cluster doit définir le paramètre *load-balancer-sku* sur *standard*. Ce paramètre crée un équilibreur de charge avec la référence SKU *Standard* quand votre cluster est créé. Lorsque vous exécutez un service *LoadBalancer* sur votre cluster, la configuration de l’équilibreur de charge de référence (SKU) *Standard* est mise à jour avec la configuration du service. Utilisez la commande [az aks create][az-aks-create] pour créer un cluster AKS nommé *myAKSCluster*.

> [!NOTE]
> La propriété *load-balancer-sku* peut uniquement être utilisée lors de la création de votre cluster. Vous ne pouvez pas modifier la référence SKU de l’équilibreur de charge après la création d’un cluster AKS. En outre, vous pouvez utiliser un seul type de référence SKU d’équilibreur de charge dans un même cluster.
> 
> Si vous souhaitez utiliser vos propres adresses IP publiques, servez-vous des paramètres *load-balancer-outbound-ips* ou *load-balancer-outbound-ip-prefixes*. Vous pouvez également utiliser ces deux paramètres lors de la [mise à jour du cluster](#optional---provide-your-own-public-ips-or-prefixes-for-egress).

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Au bout de quelques minutes, la commande se termine et retourne des informations au format JSON sur le cluster.

## <a name="connect-to-the-cluster"></a>Connexion au cluster

Pour gérer un cluster Kubernetes, vous utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes. Si vous utilisez Azure Cloud Shell, `kubectl` est déjà installé. Pour installer `kubectl` en local, utilisez la commande [az aks install-cli][az-aks-install-cli] :

```azurecli
az aks install-cli
```

Pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes, exécutez la commande [az aks get-credentials][az-aks-get-credentials]. Cette commande télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pour vérifier la connexion à votre cluster, utilisez la commande [kubectl get][kubectl-get] pour retourner une liste des nœuds du cluster.

```azurecli-interactive
kubectl get nodes
```

L’exemple de sortie suivant montre le nœud unique créé au cours des étapes précédentes. Vérifiez que l’état du nœud est *Ready* :

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Vérifier que votre cluster utilise la référence SKU *Standard*

Utilisez la commande [az aks show][az-aks-show] pour afficher la configuration de votre cluster.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

Vérifiez que la propriété *loadBalancerSku* affiche la valeur *standard*.

## <a name="use-the-load-balancer"></a>Utiliser l’équilibreur de charge

Pour utiliser l’équilibreur de charge sur votre cluster, créez un manifeste de service avec le type de service *LoadBalancer*. Pour afficher le fonctionnement de l’équilibreur de charge, créez un autre manifeste avec un exemple d’application à exécuter sur votre cluster. Cet exemple d’application est exposé via l’équilibreur de charge et peut être affiché dans un navigateur.

Créez un manifeste nommé `sample.yaml`, comme indiqué dans l’exemple suivant :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

Le manifeste ci-dessus configure deux déploiements : *azure-vote-front* et *azure-vote-back*. Pour configurer le déploiement *azure-vote-front* pour qu’il soit exposé à l’aide de l’équilibreur de charge, créez un manifeste nommé `standard-lb.yaml` comme indiqué dans l’exemple suivant :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Le service *azure-vote-front* utilise le type *LoadBalancer* pour configurer l’équilibreur de charge sur votre cluster AKS pour se connecter au déploiement *azure-vote-front*.

Déployez l’exemple d’application et l’équilibreur de charge avec la commande [kubectl apply][kubectl-apply] et spécifiez le nom de vos manifestes YAML :

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

L’équilibreur de charge de référence SKU *Standard* est maintenant configuré pour exposer l’exemple d’application. Affichez les détails du service *azure-vote-front* avec la commande [kubectl get][kubectl-get] pour voir l’adresse IP publique de l’équilibreur de charge. L’adresse IP publique de l’équilibreur de charge est indiquée dans la colonne *EXTERNAL-IP* . Le passage de l’adresse IP de l’état *\<en attente\>* à une adresse IP externe réelle peut prendre une ou deux minutes, comme indiqué dans l’exemple suivant :

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Accédez à l’adresse IP publique dans un navigateur et vérifiez que vous voyez l’exemple d’application. Dans l’exemple ci-dessus, l’IP publique est `52.179.23.131`.

![Image de la navigation vers Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Vous pouvez également configurer l’équilibreur de charge pour qu’il soit interne et n’expose pas une adresse IP publique. Pour configurer l’équilibreur de charge en interne, ajoutez `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` comme notation pour le service *LoadBalancer*. Vous pouvez voir un exemple de manifeste YAML, ainsi que plus de détails sur les équilibreurs de charge internes [ici][internal-lb-yaml].

## <a name="optional---scale-the-number-of-managed-public-ips"></a>Facultatif – Mettre à l’échelle le nombre d’adresses IP publiques gérées

Lorsque vous utilisez un équilibreur de charge de référence (SKU) *Standard* avec des adresses IP sortantes publiques gérées créées par défaut, vous pouvez mettre à l’échelle le nombre de ces adresses à l’aide du paramètre *load-balancer-managed-ip-count*.

Pour mettre à jour un cluster existant, exécutez la commande suivante. Ce paramètre peut également être défini au moment de la création du cluster pour avoir plusieurs adresses IP publiques sortantes gérées.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

L’exemple ci-dessus définit le nombre d’adresses IP sortantes publiques gérées sur *2* pour le cluster *myAKSCluster* dans *myResourceGroup*. 

Vous pouvez également utiliser le paramètre *load-balancer-managed-ip-count* pour définir le nombre initial d’adresses IP publiques sortantes gérées lors de la création de votre cluster en ajoutant le paramètre `--load-balancer-managed-outbound-ip-count` et en lui attribuant la valeur de votre choix. Le nombre d’adresses IP sortantes publiques gérées par défaut est 1.

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>Facultatif – Fournir vos propres adresses IP ou préfixes publics pour la sortie

Lorsque vous utilisez un équilibreur de charge de référence (SKU) *Standard*, le cluster AKS crée automatiquement une adresse IP publique dans le groupe de ressources créé pour le cluster AKS et attribue l’adresse IP publique à l’équilibreur de charge de référence (SKU) *Standard* . Vous pouvez également affecter votre propre adresse IP publique au moment de la création du cluster ou vous pouvez mettre à jour les propriétés d’équilibreur de charge d’un cluster existant.

En plaçant plusieurs adresses IP ou préfixes, vous pouvez définir plusieurs services de soutien lors de la définition de l’adresse IP derrière un seul objet d’équilibreur de charge. Le point de terminaison de sortie de nœuds spécifiques dépend du service auquel ils sont associés.

> [!IMPORTANT]
> Vous devez utiliser des adresses IP publiques de référence (SKU) *Standard* pour la sortie avec votre référence (SKU) *Standard* pour votre équilibreur de charge. Vous pouvez vérifier la référence (SKU) de vos adresses IP publiques à l’aide de la commande [az network public-ip show][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Utilisez la commande [az network public-ip show][az-network-public-ip-show] pour répertorier les ID de vos adresses IP publiques.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

La commande ci-dessus affiche l’ID de l’adresse IP publique *myPublicIP* dans le groupe de ressources *myResourceGroup*.

Utilisez la commande *az aks update* avec le paramètre *load-balancer-outbound-ips* pour mettre à jour votre cluster avec vos adresses IP publiques.

L’exemple suivant utilise le paramètre *load-balancer-outbound-ips* avec les ID obtenus par la commande précédente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Vous pouvez également utiliser des préfixes IP publics pour la sortie avec votre équilibreur de charge de référence (SKU) *Standard*. L’exemple suivant utilise la commande [az network public-ip prefix show][az-network-public-ip-prefix-show] pour répertorier les ID de vos préfixes IP publics :

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

La commande ci-dessus affiche l’ID du préfixe IP public *myPublicIPPrefix* dans le groupe de ressources *myResourceGroup*.

L’exemple suivant utilise le paramètre *load-balancer-outbound-ip-prefixes* avec les ID obtenus par la commande précédente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Les adresses IP publiques et les préfixes IP doivent se trouver dans la même région et faire partie du même abonnement que votre cluster AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Définir vos propres adresses IP publiques ou préfixes au moment de la création du cluster

Vous souhaiterez peut-être apporter vos propres adresses IP ou préfixes d’adresses IP pour la sortie au moment de la création du cluster, afin de prendre en charge des scénarios tels que la mise en liste verte des points de terminaison de sortie. Ajoutez les mêmes paramètres que ceux indiqués ci-dessus à l’étape de création de votre cluster pour définir vos propres adresses IP publiques et préfixes d’adresses IP au début du cycle de vie d’un cluster.

Utilisez la commande *az aks create* avec le paramètre *load-balancer-outbound-ips* pour créer un cluster commençant par vos adresses IP publiques.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Utilisez la commande *az aks create* avec le paramètre *load-balancer-outbound-ip-prefixes* pour créer un cluster commençant par vos préfixes d’adresses IP publiques.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Nettoyer la configuration d’un équilibreur de charge de référence SKU Standard

Pour supprimer l’exemple d'application et la configuration de l’équilibreur de charge, utilisez [kubectl delete][kubectl-delete] :

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les services Kubernetes dans la [documentation des services Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

