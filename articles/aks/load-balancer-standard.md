---
title: Utiliser un équilibreur de charge de référence (SKU) Standard dans Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser un équilibreur de charge avec une référence SKU Standard pour exposer vos services avec Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 9c414572e1c3b2f046ae9a14139885e9927ab3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79227601"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Utiliser un équilibreur de charge de référence (SKU) Standard dans Azure Kubernetes Service (AKS)

Pour fournir l’accès aux applications via des services Kubernetes de type `LoadBalancer` dans Azure Kubernetes Service (AKS), vous pouvez utiliser un Azure Load Balancer. Un équilibreur de charge exécuté sur AKS peut être utilisé comme équilibreur de charge interne ou externe. Un équilibreur de charge interne rend un service Kubernetes accessible uniquement aux applications qui s’exécutent dans le même réseau virtuel que le cluster AKS. Un équilibreur de charge externe reçoit une ou plusieurs adresses IP publiques pour l’entrée et rend un service Kubernetes accessible en externe en utilisant des adresses IP publiques.

Azure Load Balancer se décline en deux références SKU : *De base* et *Standard*. Par défaut, la référence SKU *Standard* est utilisée lorsque vous créez un cluster AKS. L’utilisation d’un équilibreur de charge de référence SKU *Standard* fournit des fonctionnalités supplémentaires, comme une plus grande taille pour le pool back-end et des zones de disponibilité. Il est important de comprendre les différences entre les équilibreurs de charge *Standard* et *De base* avant de choisir lequel utiliser. Une fois que vous créez un cluster AKS, vous ne pouvez pas modifier la référence SKU de l’équilibreur de charge pour ce cluster. Pour plus d’informations sur les références SKU *De base* et *Standard*, consultez [Comparaison des références SKU des équilibreurs de charge Azure][azure-lb-comparison].

Cet article suppose une compréhension élémentaire des concepts de Kubernetes et d’Azure Load Balancer. Pour plus d’informations, consultez [Concepts de base de Kubernetes pour AKS (Azure Kubernetes Service)][kubernetes-concepts] et [Qu’est-ce qu’Azure Load Balancer ?][azure-lb].

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cet article vous demande d’exécuter Azure CLI version 2.0.81 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Avant de commencer

Cet article part du principe que vous disposez d’un cluster AKS avec la référence SKU Azure Load Balancer *Standard*. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

Le principal de service du cluster AKS a également besoin de l’autorisation de gérer les ressources réseau si vous utilisez un sous-réseau ou un groupe de ressources existant. De façon générale, attribuez le rôle *Contributeur de réseau* au principal de service sur les ressources déléguées. Pour plus d’informations sur les autorisations, consultez [Delegate access to other Azure resources][aks-sp] (Déléguer l’accès à d’autres ressources Azure).

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passage d’un Load Balancer avec une référence SKU De base à une référence SKU Standard

Si vous avez un cluster existant avec le Load Balancer à référence SKU De base, il existe des différences de comportement importantes à noter lorsque vous migrez pour utiliser un cluster avec le Load Balancer à référence SKU Standard.

Par exemple, le fait d’effectuer des déploiements bleus/verts pour migrer des clusters est une pratique courante étant donné que le type de `load-balancer-sku` d’un cluster ne peut être défini qu’au moment de la création du cluster. Toutefois, les Load Balancers à *référence SKU De base* utilisent des adresses IP à *référence SKU De base* qui ne sont pas compatibles avec les Load Balancers à *référence SKU Standard*, car ceux-ci requièrent des adresses IP à *référence SKU Standard*. Lors de la migration de clusters pour mettre à niveau les références SKU du Load Balancer, une nouvelle adresse IP avec une référence SKU d’adresse IP compatible est nécessaire.

Pour plus d’informations sur la migration de clusters, consultez [notre documentation sur les considérations relatives à la migration](aks-migration.md) pour afficher une liste des rubriques importantes à prendre en compte lors de la migration. Les limitations ci-dessous constituent également des différences de comportement importantes à prendre en compte lors de l’utilisation de Load Balancers à référence SKU Standard dans AKS.

### <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous créez et gérez des clusters AKS prenant un charge un équilibreur de charge avec la référence SKU *Standard* :

* Au moins une adresse IP publique ou un préfixe IP sont requis pour autoriser le trafic sortant du cluster AKS. L’adresse IP publique et le préfixe IP sont également nécessaires pour maintenir la connectivité entre le plan de contrôle et les nœuds d’agent, ainsi que pour assurer la compatibilité avec les versions précédentes d’AKS. Pour spécifier des adresses IP publiques ou des préfixes IP avec un équilibreur de charge de référence (SKU) *Standard*, vous disposez des options suivantes :
    * Fournir vos propres adresses IP publiques.
    * Fournir vos propres préfixes IP publics.
    * Spécifier un nombre de 1 à 100 pour autoriser le cluster AKS à créer ce nombre d’adresses IP publiques de référence (SKU) *Standard* dans le groupe de ressources créé en tant que cluster AKS, dont le nom commence généralement par *MC_* . AKS attribue l’adresse IP publique pour l’équilibreur de charge de référence SKU *Standard*. Par défaut, une adresse IP publique est automatiquement créée dans le même groupe de ressources que le cluster AKS si aucune adresse IP publique, aucun préfixe IP public ou aucun nombre d’adresses IP ne sont spécifiés. Vous devez également autoriser des adresses publiques et éviter de créer une Azure Policy interdisant la création d’adresses IP.
* Lorsque vous utilisez la référence SKU *Standard* pour un équilibreur de charge, vous devez utiliser Kubernetes version *1.13 ou ultérieure*.
* Vous ne pouvez définir la référence (SKU) d’équilibreur de charge que lorsque vous créez un cluster AKS. Vous ne pouvez pas modifier la référence SKU de l’équilibreur de charge après la création d’un cluster AKS.
* Vous pouvez utiliser un seul type de référence SKU d’équilibreur de charge (De base ou Standard) dans un même cluster.
* Les Load Balancers à référence SKU *Standard* prennent uniquement en charge les adresses IP à référence SKU *Standard*.

## <a name="use-the-standard-sku-load-balancer"></a>Utiliser un équilibreur de charge avec une référence SKU *Standard*

Lorsque vous créez un cluster AKS, par défaut, l’équilibreur de charge avec une référence SKU *Standard* est utilisé lorsque vous exécutez des services dans ce cluster. Par exemple, le [guide de démarrage rapide avec Azure CLI][aks-quickstart-cli] déploie un exemple d’application qui utilise un équilibreur de charge avec une référence SKU *Standard*.

> [!IMPORTANT]
> Les adresses IP publiques peuvent être évitées en personnalisant un itinéraire défini par l’utilisateur (UDR). Si vous spécifiez le type de trafic sortant d’un cluster AKS comme UDR, vous pouvez ignorer l’approvisionnement IP et la configuration du pool principal pour l’équilibrage de charge Azure créé par AKS. Consultez le [paramétrage du `outboundType` d’un cluster sur « userDefinedRouting »](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>Configurer l’équilibreur de charge pour qu’il soit interne

Vous pouvez également configurer l’équilibreur de charge pour qu’il soit interne et n’expose pas une adresse IP publique. Pour configurer l’équilibreur de charge en interne, ajoutez `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` comme notation pour le service *LoadBalancer*. Vous pouvez voir un exemple de manifeste YAML, ainsi que plus de détails sur les équilibreurs de charge internes [ici][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Mettre à l’échelle le nombre d’adresses IP publiques gérées

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

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Fournir vos propres adresses IP ou préfixes publics pour la sortie

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

```azurecli-interactive
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

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>Configurer les ports de sortie et le délai d’inactivité

> [!WARNING]
> La section suivante concerne les scénarios avancés de réseau à plus grande échelle ou de résolution des problèmes d’épuisement de la traduction d’adresses réseau sources (SNAT) avec les configurations par défaut. Vous devez disposer d’un inventaire précis du quota disponible pour les machines virtuelles et les adresses IP avant de modifier les valeurs par défaut de *AllocatedOutboundPorts* ou *IdleTimeoutInMinutes* afin de conserver des clusters sains.
> 
> La modification des valeurs de *AllocatedOutboundPorts* et *IdleTimeoutInMinutes* peut modifier considérablement le comportement de la règle de trafic sortant pour votre équilibreur de charge. Passez en revue les [règles de trafic sortant de l’équilibreur de charge][azure-lb-outbound-rules-overview], les [règles de trafic entrant de l’équilibreur de charge][azure-lb-outbound-rules] et les [connexions sortantes dans Azure][azure-lb-outbound-connections] avant de mettre à jour ces valeurs pour bien comprendre l’impact de vos modifications.

Les ports de sortie alloués et leurs délais d’inactivité sont utilisés pour la [traductions d’adresses réseau sources (SNAT)][azure-lb-outbound-connections]. Par défaut, l’équilibreur de charge de la référence SKU *Standard* utilise l’[affectation automatique pour le nombre de ports de sortie en fonction de la taille du pool back-end][azure-lb-outbound-preallocatedports] et un délai d’inactivité de 30 minutes pour chaque port. Pour voir ces valeurs, utilisez [az network lb outbound-rule list][az-network-lb-outbound-rule-list] pour afficher la règle de trafic sortant de l’équilibreur de charge :

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Les commandes précédentes répertorient par exemple la règle de sortie pour votre équilibreur de charge :

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

L’exemple de sortie montre la valeur par défaut pour *AllocatedOutboundPorts* et *IdleTimeoutInMinutes*. La valeur 0 pour *AllocatedOutboundPorts* définit le nombre de ports de sortie à l’aide de l’attribution automatique pour le nombre de ports de sortie en fonction de la taille du pool principal. Par exemple, si le cluster a 50 nœuds ou moins, 1 024 ports sont alloués pour chaque nœud.

Envisagez de modifier le paramètre *allocatedOutboundPorts* ou *IdleTimeoutInMinutes* si vous pensez être confronté à l’épuisement de la traduction d’adresses réseau sources (SNAT) compte tenu de la configuration par défaut ci-dessus. Chaque adresse IP supplémentaire active 64 000 ports supplémentaires pour l’allocation, mais Azure Standard Load Balancer n’augmente pas automatiquement les ports par nœud quand d’autres adresses IP sont ajoutées. Vous pouvez modifier ces valeurs en définissant les paramètres *load-balancer-outbound-ports* et *load-balancer-idle-timeout*. Par exemple :

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Pour éviter les problèmes de connectivité ou de mise à l’échelle, vous devez [calculer votre quota nécessaire][calculate-required-quota] avant de personnaliser *allocatedOutboundPorts*. La valeur que vous spécifiez pour *allocatedOutboundPorts* doit également être un multiple de 8.

Vous pouvez aussi utiliser les paramètres *load-balancer-outbound-ports* et *load-balancer-idle-timeout* lors de la création d’un cluster. Toutefois, vous devez également spécifier *load-balancer-managed-outbound-ip-count*, *load-balancer-outbound-ips* ou *load-balancer-outbound-ip-prefixes*.  Par exemple :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

Quand vous modifiez les valeurs par défaut des paramètres *load-balancer-outbound-ports* et *load-balancer-idle-timeout*, cela affecte le comportement du profil d’équilibreur de charge, ce qui a un impact sur l’ensemble du cluster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Quota nécessaire à la personnalisation du paramètre allocatedOutboundPorts
Vous devez disposer d’une capacité d’adresses IP sortantes suffisante pour le nombre de machines virtuelles de votre nœud et les ports de sortie alloués souhaités. Pour vérifier que vous avez suffisamment de capacité d’adresses IP sortantes, utilisez la formule suivante : 
 
*adressesIPsortantes* \* 64 000 \> *machinesVirtuellesdeNœud* \* *portsDeSortieAllouésSouhaités*.
 
Par exemple, si vous avez 3 *machinesVirtuellesdeNœud*et 50 000 *portsDeSortieAllouésSouhaités*, vous avez besoin d’au moins 3 *adressesIPsortantes*. Il est recommandé d’intégrer une capacité d’adresses IP sortantes plus élevée que vos besoins. De plus, vous devez tenir compte de la mise à l’échelle automatique du cluster et de la possibilité de mettre à niveau les pools de nœuds lors du calcul de la capacité d’adresses IP sortantes. Pour la mise à l’échelle automatique du cluster, examinez le nombre de nœuds actuel et le nombre maximal de nœuds, puis utilisez la valeur la plus élevée. Pour la mise à niveau, comptez une machine virtuelle de nœud supplémentaire pour chaque pool de nœuds qui autorise la mise à niveau.
 
Lorsque vous définissez *IdleTimeoutInMinutes* sur une valeur différente de la valeur par défaut égale à 30 minutes, réfléchissez à la durée pendant laquelle vos charges de travail auront besoin d’une connexion sortante. Tenez également compte de la valeur du délai d’expiration par défaut d’un équilibreur de charge de la référence SKU *Standard* utilisé en dehors d’AKS qui s’élève à 4 minutes. Une valeur *IdleTimeoutInMinutes* qui reflète plus précisément votre charge de travail AKS spécifique peut contribuer à réduire l’épuisement SNAT provoqué par la liaison des connexions qui ne sont plus utilisées.

## <a name="restrict-access-to-specific-ip-ranges"></a>Restreindre l’accès à certaines plages d’adresses IP

Par défaut, le groupe de sécurité réseau qui est associé au réseau virtuel pour l’équilibreur de charge comprend une règle qui autorise tout le trafic externe entrant. Vous pouvez mettre à jour cette règle de manière à autoriser uniquement certaines plages d’adresses IP pour le trafic entrant. Le manifeste suivant utilise *loadBalancerSourceRanges* afin de spécifier une nouvelle plage d’adresses IP pour le trafic externe entrant :

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
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

L’exemple ci-dessus met à jour la règle de manière à autoriser uniquement le trafic externe entrant provenant de la plage *MY_EXTERNAL_IP_RANGE*. Pour plus d’informations sur l’utilisation de cette méthode en vue de restreindre l’accès au service d’équilibrage de charge, consultez la [documentation Kubernetes][kubernetes-cloud-provider-firewall].

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les services Kubernetes dans la [documentation des services Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
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
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
