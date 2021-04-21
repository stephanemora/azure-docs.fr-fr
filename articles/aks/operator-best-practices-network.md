---
title: Meilleures pratiques pour les ressources réseau
titleSuffix: Azure Kubernetes Service
description: Découvrez les meilleures pratiques de l’opérateur pour les ressources de réseau virtuel et la connectivité dans Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 1e0212766e7d5443664d57a97cfa9ea9d0035da3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104947"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Meilleures pratiques pour la connectivité réseau et la sécurité dans Azure Kubernetes Service (AKS)

Lorsque vous créez et gérez des clusters dans Azure Kubernetes Service (AKS), vous assurez une connectivité réseau à vos nœuds et à vos applications. Parmi ces ressources réseau figurent des plages d’adresses IP, des équilibreurs de charge et des contrôleurs d’entrée. Pour maintenir une qualité de service élevée dans vos applications, vous devez élaborer une stratégie et configurer ces ressources.

Cet article porte sur les meilleures pratiques en matière de connectivité réseau et de sécurité pour les opérateurs de clusters. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * comparer les modes réseau kubenet et Azure Container Networking interface (CNI) dans AKS ;
> * prévoir l'adressage IP et la connectivité nécessaires ;
> * distribuer le trafic à l'aide d'équilibreurs de charge, de contrôleurs d'entrée ou de pare-feu d'applications web ;
> * se connecter en toute sécurité aux nœuds de cluster.

## <a name="choose-the-appropriate-network-model"></a>Choisir le modèle réseau adapté

> **Conseils sur les bonnes pratiques** 
> 
> Utilisez la mise en réseau Azure CNI dans AKS pour l'intégration avec des réseaux virtuels existants ou des réseaux locaux. Ce modèle réseau offre une meilleure séparation des ressources et plus de contrôle dans un environnement d'entreprise.

Les réseaux virtuels assurent une connectivité de base pour les nœuds AKS et les clients qui accèdent à vos applications. Il existe deux façons différentes de déployer des clusters AKS dans des réseaux virtuels :

* **Mise en réseau Azure CNI**

    Se déploie dans un réseau virtuel et utilise le plug-in Kubernetes [Azure CNI][cni-networking]. Les pods reçoivent des adresses IP individuelles qui peuvent communiquer avec d’autres services réseau ou ressources locales.
* **Mise en réseau Kubenet**

    Azure gère les ressources du réseau virtuel pendant le déploiement du cluster et utilise le plug-in Kubernetes [kubenet][kubenet].


Pour les déploiements de production, kubenet et Azure CNI sont tous deux des options valides.

### <a name="cni-networking"></a>Mise en réseau CNI

Azure CNI est un protocole indépendant du fournisseur qui permet au runtime du conteneur d'adresser des demandes à un fournisseur de réseau. Il attribue des adresses IP aux pods et aux nœuds, et offre des fonctionnalités de Gestion des adresses IP (IPAM) lorsque vous vous connectez aux réseaux virtuels Azure existants. Chaque ressource de type nœud ou pod reçoit une adresse IP sur le réseau virtuel Azure, sans qu'aucun routage supplémentaire ne soit nécessaire pour communiquer avec d'autres ressources ou services.

![Diagramme représentant 2 nœuds avec des ponts les reliant chacun à un réseau virtuel Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

La mise en réseau Azure CNI pour la production permet notamment de séparer le contrôle et la gestion des ressources. Du point de vue de la sécurité, il est souvent préférable que différentes équipes gèrent et sécurisent ces ressources. Une mise en réseau Azure CNI vous permet de vous connecter directement à des ressources Azure existantes, à des ressources locales ou à d'autres services avec les adresses IP attribuées à chacun des pods.

Avec une mise en réseau Azure CNI, la ressource de réseau virtuel se trouve dans un groupe de ressources distinct du cluster AKS. Déléguez des permissions à l’identité du cluster AKS pour qu’il puisse accéder à ces ressources et les gérer. L’identité de cluster utilisée par le cluster AKS doit au moins disposer des autorisations [Contributeur de réseau](../role-based-access-control/built-in-roles.md#network-contributor) sur le sous-réseau de votre réseau virtuel. 

Si vous souhaitez définir un [rôle personnalisé](../role-based-access-control/custom-roles.md) au lieu d’utiliser le rôle de contributeur de réseau intégré, les autorisations suivantes sont nécessaires :
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Par défaut, AKS utilise une identité managée pour son identité de cluster. Mais vous pouvez aussi utiliser un principal de service. Le cas échéant, consultez les références suivantes :
* Concernant la délégation du principal de service AKS, consultez [Déléguer l'accès à d'autres ressources Azure][sp-delegation]. 
* Concernant les identités managées, consultez [Utiliser des identités managées](use-managed-identity.md).

Dans la mesure où chacun des nœuds et des pods reçoit sa propre adresse IP, vous devez planifier les plages d'adresses des sous-réseaux AKS. N'oubliez pas les points suivants :
* Le sous-réseau doit être suffisamment grand pour fournir une adresse IP à chacun des nœuds, pods et ressources réseau déployés. 
    * Les mises en réseau kubenet et Azure CNI appliquent toutes deux des limites par défaut quant au nombre de pods qu'exécute chaque nœud.
* Chaque cluster AKS doit être placé dans son propre sous-réseau. 
* Évitez d'utiliser des plages d'adresses IP qui se chevauchent avec les ressources réseau existantes. 
    * Nécessaire pour permettre la connectivité aux réseaux locaux ou appairés dans Azure.
* Pour gérer les événements de scale-out et les mises à niveau de cluster, des adresses IP supplémentaires sont nécessaires sur le sous-réseau attribué. 
    * Cet espace d'adressage supplémentaire est particulièrement important si vous utilisez des conteneurs Windows Server, car ces pools de nœuds nécessitent une mise à niveau pour appliquer les derniers correctifs de sécurité. Pour plus d’informations sur les nœuds Windows Server, consultez [Mettre à niveau un pool de nœuds dans AKS][nodepool-upgrade].

Pour calculer l’adresse IP requise, voir [Configurer la mise en réseau Azure CNI dans AKS][advanced-networking].

Lorsque vous créez un cluster avec la mise en réseau Azure CNI, vous spécifiez d'autres plages d'adresses pour le cluster, comme l'adresse du pont Docker, l'adresse IP du service DNS et la plage d'adresses du service. En général, veillez à ce que ces plages d'adresses :
* ne se chevauchent pas ;
* n'empiètent pas sur les réseaux associés au cluster, comme les réseaux virtuels, les sous-réseaux, les réseaux locaux et les réseaux appairés. 

Pour plus d'informations sur les limites et le dimensionnement de ces plages d'adresses, consultez [Configurer la mise en réseau Azure CNI dans AKS][advanced-networking].

### <a name="kubenet-networking"></a>Mise en réseau Kubenet

La mise en réseau kubenet ne vous oblige pas à configurer les réseaux virtuels avant le déploiement du cluster, mais le fait d'attendre présente des inconvénients :

* Comme les nœuds et les pods sont placés sur des sous-réseaux IP différents, l'itinéraire défini par l'utilisateur (UDR) et le transfert IP acheminent le trafic entre les pods et les nœuds. Ce routage supplémentaire peut réduire les performances du réseau.
* Les connexions à des réseaux locaux existants et le peering avec d’autres réseaux virtuels Azure peuvent être complexes.

Étant donné que vous ne créez pas le réseau virtuel et les sous-réseaux séparément du cluster AKS, la mise en réseau kubenet est idéale pour :
* les petites charges de travail de développement ou de test ; 
* les sites web simples à faible trafic ;
* les opérations de lift-and-shift des charges de travail dans des conteneurs.

Pour la plupart des déploiements de production, vous devez planifier et utiliser une mise en réseau Azure CNI.

Vous pouvez également [configurer vos propres plages d’adresses IP et réseaux virtuels à l’aide de Kubenet][aks-configure-kubenet-networking]. Comme pour la mise en réseau Azure CNI, ces plages d'adresses ne doivent pas se chevaucher ni empiéter sur les réseaux associés au cluster (réseaux virtuels, sous-réseaux, réseaux locaux et réseaux appairés). 

Pour plus d'informations sur les limites et le dimensionnement de ces plages d'adresses, consultez [Utiliser la mise en réseau kubenet avec vos propres plages d'adresses IP dans AKS][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuer le trafic d’entrée

> **Conseils sur les bonnes pratiques** 
> 
> Pour distribuer le trafic HTTP ou HTTPS à vos applications, utilisez des ressources et des contrôleurs d'entrée. Par rapport à un équilibreur de charge Azure, les contrôleurs d'entrée offrent des fonctionnalités supplémentaires et peuvent être gérés comme des ressources Kubernetes natives.

Bien qu'un équilibreur de charge Azure puisse distribuer le trafic client aux applications de votre cluster AKS, sa compréhension de ce trafic est limitée. Une ressource d’équilibrage de charge, qui agit au niveau de la couche 4, répartit le trafic en fonction du protocole ou des ports. 

La plupart des applications web qui utilisent HTTP ou HTTPS doivent de préférence utiliser des ressources et des contrôleurs d'entrée Kubernetes, qui fonctionnent au niveau de la couche 7. L’entrée peut distribuer le trafic en fonction de l’URL de l’application et gérer l’arrêt TLS/SSL. L'entrée réduit également le nombre d'adresses IP exposées et mappées. 

Avec un équilibreur de charge, une adresse IP publique doit généralement être affectée et mappée au service dans le cluster AKS pour chaque application. Avec une ressource d’entrée, une seule adresse IP peut répartir le trafic entre plusieurs applications.

![Diagramme montrant le flux de trafic d’entrée dans un cluster AKS](media/operator-best-practices-network/aks-ingress.png)

 Il existe deux composants d’entrée :

 * une *ressource* d'entrée ;
 * un *contrôleur* d’entrée.

### <a name="ingress-resource"></a>Ressource d'entrée

La *ressource d'entrée* est un manifeste YAML de `kind: Ingress`. Celui-ci définit l'hôte, les certificats et les règles d'acheminement du trafic vers les services exécutés dans votre cluster AKS. 

L’exemple de manifeste YAML suivant distribuerait le trafic de *myapp.com* à un des deux services, *blogservice* ou *storeservice*. Le client est dirigé vers l’un ou l’autre selon l’URL consultée.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

### <a name="ingress-controller"></a>Contrôleur d’entrée

Un *contrôleur d'entrée* est un démon qui s'exécute sur un nœud AKS et qui surveille les demandes entrantes. Le trafic est ensuite distribué selon les règles définies dans la ressource d’entrée. Le contrôleur d'entrée le plus courant est basé sur [NGINX], mais AKS ne vous impose aucun contrôleur spécifique. Vous pouvez utiliser [Contour][contour], [HAProxy][haproxy], [Traefik][traefik], etc.

Les contrôleurs d’entrée doivent être planifiés sur un nœud Linux. Utilisez un sélecteur de nœud dans votre manifeste YAML ou votre déploiement de graphique Helm pour indiquer que la ressource doit s'exécuter sur un nœud Linux. Pour plus d’informations, consultez [Use node selectors to control where pods are scheduled in AKS (Utiliser des sélecteurs de nœud pour contrôler l’emplacement de planification des pods dans AKS)][concepts-node-selectors].

> [!NOTE]
> Les nœuds Windows Server ne doivent pas exécuter le contrôleur d’entrée.

Il existe de nombreux scénarios pour l’entrée, notamment ceux des guides pratiques suivants :

* [Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)][aks-ingress-basic]
* [Create an ingress controller to an internal virtual network in Azure Kubernetes Service (AKS)][aks-ingress-internal] (Créer un contrôleur d’entrée pour un réseau virtuel interne dans Azure Kubernetes Service (AKS))
* [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
* Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS [avec une adresse IP publique dynamique][aks-ingress-tls] ou [avec une adresse IP publique statique][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Sécuriser le trafic avec un pare-feu d’applications web (WAF)

> **Conseils sur les bonnes pratiques**
> 
> Pour analyser le trafic entrant à la recherche d'attaques potentielles, utilisez un pare-feu d'application web (WAF) tel que [Barracuda pour Azure][barracuda-waf] ou Azure Application Gateway. Ces ressources réseau plus avancées peuvent également acheminer le trafic au-delà des connexions HTTP et HTTPS et de l’arrêt TLS de base.

Généralement, un contrôleur d'entrée est une ressource Kubernetes du cluster AKS qui distribue le trafic aux services et applications. Le contrôleur s'exécute en tant que démon sur un nœud AKS et consomme des ressources du nœud, comme le processeur, la mémoire et la bande passante réseau. Dans les environnements de grande taille, il est souvent nécessaire de :
* décharger une partie du routage du trafic ou de la terminaison TLS vers une ressource réseau située en dehors du cluster AKS ;
* analyser le trafic entrant à la recherche d'attaques potentielles.

![Un pare-feu d’applications web (WAF) comme Azure Application Gateway peut protéger et distribuer le trafic d’un cluster AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Pour cette couche supplémentaire de sécurité, un pare-feu d'applications web (WAF) filtre le trafic entrant. Le projet OWASP (Open Web Application Security Project) propose un ensemble de règles pour surveiller les attaques de type script intersites ou cookie poisoning. [Azure Application Gateway][app-gateway] (actuellement en préversion dans AKS) est un pare-feu WAF qui intègre ces fonctionnalités de sécurité dans des clusters AKS, avant que le trafic n'atteigne les clusters et les applications. 

Comme d'autres solutions tierces remplissent également ces fonctions, vous pouvez continuer à utiliser les investissements ou l'expertise déjà acquis avec votre produit préféré.

Les ressources d'équilibrage de charge ou d'entrée s'exécutent en permanence dans le cluster AKS et affinent la distribution du trafic. App Gateway peut être géré de manière centralisée comme contrôleur d’entrée avec une définition de ressource. Pour commencer, voir [Créer un contrôleur d’entrée Application Gateway][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Contrôler le flux de trafic avec des stratégies réseau

> **Conseils sur les bonnes pratiques** 
>
> Utilisez des stratégies réseau pour autoriser ou refuser le trafic vers les pods. Par défaut, tout le trafic est autorisé entre les pods au sein d’un cluster. Pour une sécurité accrue, définissez des règles qui limitent la communication des pods.

L’utilisation de stratégies réseau est une fonctionnalité Kubernetes disponible dans AKS qui vous permet de contrôler le flux de trafic entre les pods. Vous autorisez ou refusez le trafic vers le pod en fonction de paramètres tels que les étiquettes attribuées, l'espace de noms ou le port de trafic. Les stratégies réseau constituent un moyen natif Cloud de contrôler le flux de trafic pour les pods. Les pods étant créés de façon dynamique dans un cluster AKS, les stratégies réseau nécessaires peuvent être appliquées automatiquement.

Pour utiliser une stratégie réseau, activez la fonctionnalité lorsque vous créez un cluster AKS. Vous ne pouvez pas activer une stratégie réseau sur un cluster AKS existant. Anticipez l'activation de la stratégie réseau sur les clusters nécessaires. 

>[!NOTE]
>Une stratégie réseau doit uniquement être utilisée pour les nœuds et pods Linux dans AKS.

Vous créez une stratégie réseau en tant que ressource Kubernetes à l'aide d'un manifeste YAML. Les stratégies sont appliquées aux pods définis, avec des règles d'entrée ou de sortie qui définissent le flux de trafic. 

L’exemple suivant applique une stratégie réseau à des pods dotés de l’étiquette *app: backend*. La règle d'entrée autorise uniquement le trafic en provenance des pods dotés de l'étiquette *app: frontend* :

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Pour commencer à utiliser des stratégies, consultez [Sécuriser le trafic entre les pods avec des stratégies réseau dans Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Se connecter en toute sécurité aux nœuds à travers un hôte bastion

> **Conseils sur les bonnes pratiques** 
>
> N'exposez pas de connectivité à distance sur vos nœuds AKS. Créez un hôte bastion, ou une jumpbox, dans un réseau virtuel de gestion. Utilisez l’hôte bastion pour acheminer le trafic en toute sécurité dans votre cluster AKS aux tâches de gestion à distance.

Dans AKS, la plupart des opérations peuvent être effectuées à l'aide des outils de gestion Azure ou via le serveur d'API Kubernetes. Les nœuds AKS sont uniquement disponibles sur un réseau privé et ne sont pas connectés au réseau Internet public. Pour vous connecter aux nœuds et assurer la maintenance et le support, acheminez vos connexions via un hôte bastion ou une jumpbox. Vérifiez que cet hôte réside dans un réseau virtuel de gestion distinct et appairé de manière sécurisée au réseau virtuel du cluster AKS.

![Se connecter à des nœuds AKS à l’aide d’un hôte bastion ou d’une jumpbox](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Le réseau de gestion de l’hôte bastion doit lui aussi être sécurisé. Utilisez [Azure ExpressRoute][expressroute] ou une [passerelle VPN][vpn-gateway] pour vous connecter à un réseau local et contrôler l’accès avec des groupes de sécurité réseau.

## <a name="next-steps"></a>Étapes suivantes

Cet article porte sur la sécurité et la connectivité réseau. Pour plus d’informations sur les principes fondamentaux des réseaux dans Kubernetes, voir [Concepts relatifs au réseau des applications dans Azure Kubernetes Service (AKS)][aks-concepts-network].

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool