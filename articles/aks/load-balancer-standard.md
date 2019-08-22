---
title: Préversion - Utiliser un équilibreur de charge de référence (SKU) Standard dans Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser un équilibreur de charge avec une référence SKU Standard pour exposer vos services avec Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: 1dcf08f4fefb53ed46038c82e0ce8f9d3dd94de2
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032238"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Préversion - Utiliser un équilibreur de charge de référence (SKU) Standard dans Azure Kubernetes Service (AKS)

Pour restreindre l’accès à vos applications dans Azure Kubernetes Service (AKS), vous pouvez créer et utiliser un Azure Load Balancer. Un équilibreur de charge exécuté sur AKS peut être utilisé comme équilibreur de charge interne ou externe. Un équilibreur de charge interne rend un service Kubernetes accessible uniquement aux applications qui s’exécutent dans le même réseau virtuel que le cluster AKS. Un équilibreur de charge externe reçoit une ou plusieurs adresses IP publiques pour l’entrée et rend un service Kubernetes accessible en externe en utilisant des adresses IP publiques.

Azure Load Balancer se décline en deux références SKU : *De base* et *Standard*. Par défaut, la référence SKU *De base* est utilisée lorsqu’un manifeste de service est utilisé pour créer un équilibreur de charge sur AKS. L’utilisation d’un équilibreur de charge de référence SKU *Standard* fournit des fonctionnalités supplémentaires, comme une plus grande taille pour le pool principal, et les zones de disponibilité. Il est important de comprendre les différences entre les équilibreurs de charge *Standard* et *De base* avant de choisir lequel utiliser. Une fois que vous créez un cluster AKS, vous ne pouvez pas modifier la référence SKU de l’équilibreur de charge pour ce cluster. Pour plus d’informations sur les références SKU *De base* et *Standard*, consultez [Comparaison des références SKU des équilibreurs de charge Azure][azure-lb-comparison].

Cet article explique comment créer et utiliser un Azure Load Balancer avec la référence SKU *Standard* avec Azure Kubernetes Service (AKS).

Cet article suppose une compréhension élémentaire des concepts de Kubernetes et d’Azure Load Balancer. Pour plus d’informations, consultez [Concepts de base de Kubernetes pour AKS (Azure Kubernetes Service)][kubernetes-concepts] et [Qu’est-ce qu’Azure Load Balancer ?][azure-lb].

Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cet article vous demande d’exécuter Azure CLI version 2.0.59 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Avant de commencer

Le principal de service du cluster AKS a besoin de l’autorisation de gérer les ressources réseau si vous utilisez un sous-réseau ou un groupe de ressources existant. De façon générale, attribuez le rôle *Contributeur de réseau* au principal de service sur les ressources déléguées. Pour plus d’informations sur les autorisations, consultez [Déléguer l’accès à AKS à d’autres ressources Azure][aks-sp].

Vous devez créer un cluster AKS qui définit la référence SKU pour l’équilibreur de charge sur *Standard* au lieu de la valeur par défaut, *De base*. La création d’un cluster AKS est couverte dans une étape ultérieure, mais vous devez d’abord activer quelques fonctionnalités d’évaluation.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et font l’objet d’un abonnement. Les versions préliminaires sont fournies « en-l’état », « avec toutes les erreurs » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service (sla) et de la garantie limitée. Les versions préliminaires AKS sont partiellement couvertes par le service clientèle sur la base du meilleur effort. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour obtenir des informations supplémentaires, veuillez lire les articles de support suivants :
>
> * [Stratégies de support AKS][aks-support-policies]
> * [FAQ du support Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Pour utiliser la référence SKU d’équilibrage de charge Azure Standard, vous aurez besoin de l’extension de CLI *aks-preview* version 0.4.1 ou version ultérieure. Installez l’extension Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add], puis recherchez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Inscrire la fonctionnalité AKSAzureStandardLoadBalancer en préversion

Pour créer un cluster AKS qui peut utiliser un équilibreur de charge avec la référence SKU *Standard*, vous devez activer l’indicateur de fonctionnalité *AKSAzureStandardLoadBalancer* sur votre abonnement. La fonctionnalité *AKSAzureStandardLoadBalancer* utilise également *VMSSPreview* lors de la création d’un cluster à l’aide de jeux de mise à l’échelle de machine virtuelle. Cette fonctionnalité fournit la dernière série d’améliorations de service lors de la configuration d’un cluster. Si ce n’est pas obligatoire, il vous est recommandé d’activer l’indicateur de fonctionnalité *VMSSPreview* aussi.

> [!CAUTION]
> Lorsque vous inscrivez une fonctionnalité sur un abonnement, vous ne pouvez actuellement pas désinscrire cette fonctionnalité. Après avoir activé des fonctionnalités en préversion, des valeurs par défaut peuvent être utilisées pour tous les clusters AKS créés ultérieurement dans l’abonnement. N’activez pas les fonctionnalités d’évaluation sur les abonnements de production. Utilisez un abonnement distinct pour tester les fonctionnalités en préversion et recueillir des commentaires.

Inscrivez les indicateurs de fonctionnalité *VMSSPreview* et *AKSAzureStandardLoadBalancer* à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Tous les clusters AKS que vous créez après avoir enregistré les indicateurs de fonctionnalité *VMSSPreview* ou *AKSAzureStandardLoadBalancer* utilisent l’expérience de cluster en préversion. Pour continuer à créer des clusters standard et entièrement supportés, n’activez pas les fonctionnalités en préversion sur les abonnements de production. Utilisez abonnement Azure de test ou de développement pour tester les fonctionnalités en préversion.

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l’état de l’enregistrement à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous créez et gérez des clusters AKS prenant un charge un équilibreur de charge avec la référence SKU *Standard* :

* Lorsque vous utilisez la référence SKU *Standard* pour un équilibreur de charge, vous devez autoriser des adresses publiques et éviter de créer une Azure Policy qui interdit la création d’IP. Le cluster AKS crée automatiquement une adresse IP publique pour la référence SKU *Standard* dans le même groupe de ressources que celui créé pour le cluster AKS, généralement nommé avec *MC_* au début. AKS attribue l’adresse IP publique pour l’équilibreur de charge de référence SKU *Standard*. L’adresse IP publique est requise pour autoriser le trafic sortant à partir du cluster AKS. Cette adresse IP publique est également nécessaire pour maintenir la connectivité entre le plan de contrôle et les nœuds d’agent, ainsi que pour assurer la compatibilité avec les versions précédentes d’AKS.
* Lorsque vous utilisez la référence SKU *Standard* pour un équilibreur de charge, vous devez utiliser Kubernetes version 1.13.5 ou version ultérieure.
* Si vous utilisez la [fonctionnalité Adresse publique du nœud](use-multiple-node-pools.md#assign-a-public-ip-per-node-in-a-node-pool) avec des équilibreurs de charge standard, vous pouvez définir une règle de trafic sortant SLB ou une adresse IP publique pour le nœud. Vous devez sélectionner l’une ou l’autre, car une machine virtuelle ne peut pas être attachée simultanément à une règle de trafic sortant SLB et à une adresse IP publique.

Même si cette fonctionnalité est en préversion préliminaire, les limitations supplémentaires suivantes s’appliquent :

* Lorsque vous utilisez la référence SKU *Standard* pour un équilibreur de charge dans AKS, vous ne pouvez pas définir votre propre adresse IP publique pour la sortie de l’équilibreur de charge. Vous devez utiliser l’adresse IP qu'AKS attribue à votre équilibreur de charge.

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
Pour exécuter un cluster AKS qui prend en charge un équilibreur de charge avec la référence SKU *Standard*, votre cluster doit définir le paramètre *load-balancer-sku* sur *standard*. Ce paramètre crée un équilibreur de charge avec la référence SKU *Standard* quand votre cluster est créé. Lorsque vous exécutez un service *LoadBalancer* sur votre cluster, la configuration de l’équilibreur de charge de référence SKU *Standard* est mise à jour avec la configuration du service. Utilisez la commande [az aks create][az-aks-create] pour créer un cluster AKS nommé *myAKSCluster*.

> [!NOTE]
> La propriété *load-balancer-sku* peut uniquement être utilisée lors de la création de votre cluster. Vous ne pouvez pas modifier la référence SKU de l’équilibreur de charge après la création d’un cluster AKS. En outre, vous pouvez utiliser un seul type de référence SKU d’équilibreur de charge dans un même cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
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
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.9
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
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
