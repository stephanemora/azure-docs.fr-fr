---
title: 'Concepts : mise en réseau dans AKS (Azure Kubernetes Service)'
description: Découvrez la mise en réseau dans AKS (Azure Kubernetes Service), notamment la mise en réseau kubenet et Azure CNI, les contrôleurs d’entrée, les équilibreurs de charge et les adresses IP statiques.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 56c98163434fbe2d29cf49bf750d6f7d1cfe0d2b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105338"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Concepts de réseau pour les applications dans AKS (Azure Kubernetes Service)

Dans une approche du développement d’applications axée sur les microservices basés sur des conteneurs, les composants d’application travaillent ensemble pour traiter leurs tâches. Kubernetes fournit diverses ressources permettant cette coopération :
* Vous pouvez vous connecter aux applications et les exposer en interne ou en externe. 
* Vous pouvez créer des applications hautement disponibles en équilibrant la charge de vos applications. 
* Pour vos applications plus complexes, vous pouvez configurer le trafic d’entrée pour la terminaison SSL/TLS ou le routage de plusieurs composants. 
* Pour des raisons de sécurité, vous pouvez restreindre le flux du trafic vers ou entre les nœuds et les pods.

Cet article présente les concepts fondamentaux qui fournissent la mise en réseau à vos applications dans AKS :

- [Services](#services)
- [Réseaux virtuels Azure](#azure-virtual-networks)
- [Contrôleurs d’entrée](#ingress-controllers)
- [Stratégies réseau](#network-policies)

## <a name="kubernetes-basics"></a>Concepts de base Kubernetes

Pour autoriser l’accès à vos applications ou entre les composants d’application, Kubernetes fournit une couche d’abstraction à la mise en réseau virtuel. Les nœuds Kubernetes se connectent à un réseau virtuel, fournissant une connectivité entrante et sortante pour les pods. Le composant *kube-proxy* s’exécute sur chaque nœud afin de fournir ces fonctionnalités de réseau.

Dans Kubernetes :
* Les *services* regroupent logiquement les pods pour permettre un accès direct sur un port spécifique via une adresse IP ou un nom DNS. 
* Vous pouvez distribuer le trafic à l’aide d’un *équilibreur de charge*. 
* En outre, vous pouvez affiner le routage du trafic des applications avec des *contrôleurs d’entrée*. 
* La sécurité et le filtrage du trafic réseau pour les pods sont possibles avec des *stratégies réseau* Kubernetes.

La plateforme Azure simplifie également la mise en réseau virtuel des clusters AKS. Lorsque vous créez un équilibreur de charge Kubernetes, vous créez et configurez également la ressource d’équilibreur de charge Azure sous-jacente. Quand vous ouvrez des ports réseau sur les pods, les règles de groupe de sécurité réseau Azure correspondantes sont configurées. Pour le routage d’application HTTP, Azure peut également configurer un *DNS externe* quand de nouvelles routes d’entrée sont configurées.

## <a name="services"></a>Services

Pour simplifier la configuration du réseau pour les charges de travail d’applications, Kubernetes utilise des *services* pour regrouper logiquement un ensemble de pods et fournir une connectivité réseau. Les types de service suivants sont disponibles :

- **IP du cluster** 
  
  Crée une adresse IP interne à utiliser au sein du cluster AKS. Convient pour les applications internes uniquement qui prennent en charge d’autres charges de travail au sein du cluster.

    ![Diagramme montrant le flux de trafic ClusterIP dans un cluster AKS][aks-clusterip]

- **NodePort** 

  Crée un mappage de ports sur le nœud sous-jacent qui rend l’application accessible directement avec l’adresse IP du nœud et le port.

    ![Diagramme montrant le flux de trafic NodePort dans un cluster AKS][aks-nodeport]

- **LoadBalancer** 

  Crée une ressource d’équilibreur de charge Azure, configure une adresse IP externe et connecte les pods demandés au pool back-end de l’équilibreur de charge. Pour que le trafic des clients puisse atteindre l’application, des règles d’équilibrage de charge sont créées sur les ports souhaités. 

    ![Diagramme montrant le flux de trafic LoadBalancer dans un cluster AKS][aks-loadbalancer]

    Pour renforcer le contrôle et le routage du trafic entrant, vous pouvez utiliser un [contrôleur d’entrée](#ingress-controllers).

- **ExternalName** 

  Crée une entrée DNS spécifique pour faciliter l’accès aux applications.

L’adresse IP des équilibreurs de charge et des services peut être attribuée dynamiquement, ou vous pouvez spécifier une adresse IP statique existante. Vous pouvez attribuer des adresses IP statiques internes et externes. Les adresses IP statiques existantes sont souvent liées à une entrée DNS.

Vous pouvez créer des équilibreurs de charge *internes* et *externes*. Les équilibreurs de charge internes ne recevant qu'une adresse IP privée, ils ne sont pas accessibles à partir d'Internet.

## <a name="azure-virtual-networks"></a>Réseaux virtuels Azure

Dans AKS, vous pouvez déployer un cluster qui utilise un des deux modèles de réseau suivants :

- Mise en réseau *Kubenet*

  Les ressources réseau sont généralement créées et configurées lors du déploiement du cluster AKS.

- Mise en réseau *Azure Container Networking Interface (CNI)*
 
  Le cluster AKS est connecté aux ressources et configurations de réseau virtuel existantes.

### <a name="kubenet-basic-networking"></a>Mise en réseau Kubenet (de base)

L’option de mise en réseau *kubenet* est la configuration par défaut de la création de cluster AKS. Avec *kubenet* :
1. Les nœuds reçoivent une adresse IP du sous-réseau du réseau virtuel Azure. 
1. Les pods reçoivent une adresse IP d’un espace d’adressage logiquement différent du sous-réseau du réseau virtuel Azure des nœuds. 
1. La traduction d’adresses réseau (NAT) est ensuite configurée afin que les pods puissent accéder aux ressources sur le réseau virtuel Azure. 
1. L’adresse IP source du trafic est traduite en adresse IP principale du nœud.

Les nœuds utilisent le plug-in Kubernetes [kubenet][kubenet]. Vous pouvez :
* laisser la plateforme Azure créer et configurer les réseaux virtuels pour vous ; ou 
* choisir de déployer votre cluster AKS dans un sous-réseau de réseau virtuel existant. 

N’oubliez pas que seuls les nœuds reçoivent une adresse IP routable. Les pods utilisent la traduction d’adresses réseau pour communiquer avec d’autres ressources en dehors du cluster AKS. Cette approche réduit le nombre d’adresses IP que vous devez réserver dans votre espace réseau à l’usage des pods.

Pour plus d’informations, consultez [Configurer une mise en réseau kubenet pour un cluster AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Mise en réseau Azure CNI (avancée)

Avec Azure CNI, chaque pod reçoit une adresse IP du sous-réseau et est accessible directement. Ces adresses IP doivent être planifiées et être uniques dans votre espace réseau. Chaque nœud possède un paramètre de configuration pour le nombre maximal de pods qu’il prend en charge. Le nombre équivalent d’adresses IP par nœud est alors réservé à l’avance. Sans planification, cette approche peut conduire à l’épuisement des adresses IP ou à la nécessité de regénérer les clusters dans un sous-réseau plus vaste à mesure que vos demandes d’applications augmentent.

Contrairement à kubenet, le trafic vers les points de terminaison dans le même réseau virtuel ne fait pas l’objet d’une traduction d’adresses réseau (NAT) vers l’adresse IP principale du nœud. L’adresse source pour le trafic à l’intérieur du réseau virtuel est l’adresse IP du pod. Le trafic externe au réseau virtuel continue de faire l’objet d’une traduction d’adresses réseau (NAT) vers l’adresse IP principale du nœud.

Les nœuds utilisent le plug-in Kubernetes [Azure CNI][cni-networking].

![Diagramme représentant 2 nœuds avec des ponts les reliant chacun à un réseau virtuel Azure][advanced-networking-diagram]

Pour plus d’informations, consultez [Configurer Azure CNI pour un cluster AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Comparer des modèles de réseaux

Kubenet et Azure CNI fournissent une connectivité réseau pour vos clusters AKS. Chacun présente toutefois des avantages et des inconvénients. À un niveau élevé, les considérations suivantes s’appliquent :

* **Kubenet**
    * Conserve l’espace d’adressage IP.
    * Utilise l’équilibreur de charge interne ou externe Kubernetes pour atteindre des pods depuis l’extérieur du cluster.
    * Vous gérez et mettez à jour manuellement les itinéraires définis par l’utilisateur (UDR).
    * 400 nœuds maximum par cluster.
* **Azure CNI**
    * Les pods bénéficient de la connectivité complète du réseau virtuel et sont directement accessibles via leur adresse IP privée depuis des réseaux connectés.
    * Nécessite plus d’espace d’adressage IP.

Les différences de comportement suivantes existent entre kubenet et Azure CNI :

| Fonctionnalité                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Déployer un cluster dans un réseau virtuel nouveau ou existant                                            | Pris en charge : UDR appliqués manuellement | Prise en charge |
| Connectivité pod-pod                                                                         | Prise en charge | Prise en charge |
| Connectivité pod-machine virtuelle ; la machine virtuelle se trouve dans le même réseau virtuel                                          | S’applique si initié par le pod | S’applique dans les deux sens |
| Connectivité pod-machine virtuelle ; la machine virtuelle se trouve dans un réseau virtuel homologué                                            | S’applique si initié par le pod | S’applique dans les deux sens |
| Accès local à l’aide de VPN ou d’Express Route                                                | S’applique si initié par le pod | S’applique dans les deux sens |
| Accès à des ressources sécurisées par des points de terminaison de service                                             | Prise en charge | Prise en charge |
| Exposer des services Kubernetes à l’aide d’un service d’équilibreur de charge, App Gateway ou d’un contrôleur d’entrée | Prise en charge | Prise en charge |
| Azure DNS et zones privées par défaut                                                          | Prise en charge | Prise en charge |

En ce qui concerne DNS, avec les plug-ins kubenet et Azure CNI, DNS est offert par CoreDNS, un déploiement s’exécutant dans AKS avec son propre autoscaler. Pour plus d’informations concernant CoreDNS sur Kubernetes, consultez [Personnalisation du service DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). CoreDNS est configuré par défaut pour transférer les domaines inconnus à la fonctionnalité DNS du réseau virtuel Azure sur lequel le cluster AKS est déployé. Par conséquent, Azure DNS Private Zones fonctionne pour les pods s’exécutant dans AKS.

### <a name="support-scope-between-network-models"></a>Étendue du support entre des modèles de réseaux

Quel que soit le modèle de réseau que vous utilisez, kubenet et Azure CNI peuvent être déployés de l’une des manières suivantes :

* La plateforme Azure peut automatiquement créer et configurer les ressources de réseau virtuel lorsque vous créez un cluster AKS.
* Vous pouvez créer et configurer manuellement les ressources de réseau virtuel et joindre à ces ressources lorsque vous créez votre cluster AKS.

Bien que des fonctionnalités telles que les points de terminaison de service ou les UDR soient prises en charge avec kubenet et Azure CNI, les [stratégies de support pour AKS][support-policies] définissent les modifications que vous pouvez apporter. Par exemple :

* Si vous créez manuellement les ressources de réseau virtuel pour un cluster AKS, une prise en charge est assurée lorsque vous configurez vos propres UDR ou points de terminaison de service.
* Si la plateforme Azure crée automatiquement les ressources de réseau virtuel pour votre cluster AKS, vous ne pouvez pas modifier manuellement ces ressources managées par AKS pour configurer vos propres UDR ou points de terminaison de service.

## <a name="ingress-controllers"></a>Contrôleurs d’entrée

Quand vous créez un service de type LoadBalancer, vous créez également une ressource d’équilibreur de charge Azure sous-jacente. L’équilibreur de charge est configuré pour distribuer le trafic vers les pods dans votre service sur un port donné. 

Le LoadBalancer fonctionne uniquement au niveau de la couche 4. Au niveau de la couche 4, le service n’a pas connaissance des applications réelles et n’intervient pas dans le routage.

Les *contrôleurs d’entrée* fonctionnent au niveau de la couche 7 ; ils peuvent utiliser des règles plus intelligentes pour distribuer le trafic des applications. Les contrôleurs d’entrée acheminent généralement le trafic HTTP vers différentes applications en fonction de l’URL d’entrée.

![Diagramme montrant le flux de trafic d’entrée dans un cluster AKS][aks-ingress]

### <a name="create-an-ingress-resource"></a>Créer une ressource d’entrée
Dans AKS, vous pouvez créer une ressource d’entrée à l’aide de NGINX, d’un outil similaire ou de la fonctionnalité de routage d’applications HTTP d’AKS. Quand vous activez le routage d’application HTTP pour un cluster AKS, la plateforme Azure crée le contrôleur d’entrée et un contrôleur *DNS externe*. Quand des ressources d’entrée sont créées dans Kubernetes, les enregistrements DNS A requis sont créés dans une zone DNS propre au cluster. 

Pour plus d’informations, consultez [Déployer le routage d’applications HTTP][aks-http-routing].

### <a name="application-gateway-ingress-controller-agic"></a>Application Gateway Ingress Controller (AGIC)

Grâce au module complémentaire AGIC (Application Gateway Ingress Controller), les clients d’AKS tirent parti de l’équilibreur de charge Application Gateway natif de niveau 7 d’Azure pour exposer un logiciel cloud sur Internet. AGIC surveille le cluster Kubernetes hôte et met continuellement à jour une passerelle applicative, exposant certains services à Internet. 

Pour en savoir plus sur le module complémentaire AGIC pour AKS, consultez [Qu’est-ce que Application Gateway Ingress Controller ?][agic-overview]

### <a name="ssltls-termination"></a>Terminaison SSL/TLS

La terminaison SSL/TLS est une autre caractéristique commune d’Ingress. Sur les grandes applications web accessibles via HTTPS, la ressource Ingress gère la terminaison TLS plutôt que dans l’application proprement dite. Pour fournir la génération et la configuration automatiques de la certification TLS, vous pouvez configurer la ressource Ingress pour utiliser des fournisseurs tels que « Let’s Encrypt ». 

Pour plus d’informations sur la configuration d’un contrôleur d’entrée NGINX avec Let’s Encrypt, consultez [Entrée et TLS][aks-ingress-tls].

### <a name="client-source-ip-preservation"></a>Préservation de l’adresse IP source du client

Configurez votre contrôleur d’entrée pour qu’il conserve l’adresse IP source de client dans les requêtes adressées aux conteneurs de votre cluster AKS. Lorsque votre contrôleur d’entrée achemine la requête d’un client vers un conteneur de votre cluster AKS, l’adresse IP source originale de cette requête n’est pas disponible pour le conteneur cible. Lorsque vous activez la *conservation de l’adresse IP source de client*, l’adresse IP source du client est disponible dans l’en-tête de requête sous *X-Forwarded-For*. 

Si vous utilisez la conservation de l’adresse IP source du client sur votre contrôleur d’entrée, vous ne pouvez pas utiliser le protocole TLS direct. La conservation de l’adresse IP source de client et le protocole TLS direct peuvent être utilisés avec d’autres services, tels que le type *LoadBalancer*.

## <a name="network-security-groups"></a>Groupes de sécurité réseau

Un groupe de sécurité réseau filtre le trafic pour des machines virtuelles comme les nœuds AKS. Quand vous créez des services, tels qu’un LoadBalancer, la plateforme Azure configure automatiquement toutes les règles de groupe de sécurité réseau nécessaires. 

Vous n’avez pas besoin de configurer manuellement des règles de groupe de sécurité réseau pour filtrer le trafic des pods dans un cluster AKS. Il suffit de définir les ports nécessaires et la redirection dans le cadre de vos manifestes de service Kubernetes. Laissez la plateforme Azure créer ou mettre à jour les règles appropriées. 

Vous pouvez également utiliser des stratégies réseau pour appliquer automatiquement des règles de filtrage de trafic aux pods.

## <a name="network-policies"></a>Stratégies réseau

Par défaut, tous les pods d’un cluster AKS peuvent envoyer et recevoir du trafic sans aucune limite. Pour une sécurité accrue, définissez des règles qui contrôlent le flux de trafic, par exemple :
* Les applications back-end sont exposées uniquement aux services frontaux requis. 
* Les composants de base de données sont uniquement accessibles aux couches Application qui s’y connectent.

L’utilisation de stratégies réseau est une fonctionnalité Kubernetes disponible dans AKS qui vous permet de contrôler le flux de trafic entre les pods. Vous autorisez ou refusez le trafic vers le pod en fonction de paramètres tels que les étiquettes attribuées, l’espace de noms ou le port de trafic. Bien que les groupes de sécurité réseau soient mieux adaptés aux nœuds AKS, les stratégies réseau sont un moyen natif Cloud plus adapté pour contrôler le flux de trafic pour les pods. Les pods étant créés de façon dynamique dans un cluster AKS, les stratégies réseau nécessaires peuvent être appliquées automatiquement.

Pour plus d’informations, consultez [Sécuriser le trafic entre les pods avec des stratégies réseau dans Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec les réseaux AKS, créez et configurez un cluster AKS avec vos propres plages d’adresses IP à l’aide de [kubenet][aks-configure-kubenet-networking] ou d’[Azure CNI][aks-configure-advanced-networking].

Pour connaître les meilleures pratiques associées, consultez [Meilleures pratiques relatives à la connectivité réseau et à la sécurité dans AKS][operator-best-practices-network].

Pour plus d’informations sur les concepts fondamentaux de Kubernetes et d’AKS, consultez les articles suivants :

- [Clusters et charges de travail Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Accès et identité Kubernetes/AKS][aks-concepts-identity]
- [Sécurité Kubernetes/AKS][aks-concepts-security]
- [Stockage Kubernetes/AKS][aks-concepts-storage]
- [Mise à l’échelle Kubernetes/AKS][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
