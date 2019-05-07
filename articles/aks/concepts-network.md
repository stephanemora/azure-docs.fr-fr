---
title: 'Concepts : mise en réseau dans AKS (Azure Kubernetes Service)'
description: Découvrez la mise en réseau dans AKS (Azure Kubernetes Service), notamment la mise en réseau kubenet et Azure CNI, les contrôleurs d’entrée, les équilibreurs de charge et les adresses IP statiques.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 2d51699138914e4a8ad5d2a133161fcfce71e9fe
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074062"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Concepts de réseau pour les applications dans AKS (Azure Kubernetes Service)

Dans une approche du développement d’applications axée sur les microservices basés sur un conteneur, les composants d’application doivent collaborer pour traiter leurs tâches. Kubernetes fournit diverses ressources qui permettent cette communication entre les applications. Vous pouvez vous connecter aux applications et les exposer en interne ou en externe. Pour créer des applications hautement disponibles, vous pouvez équilibrer leurs charges. Les applications plus complexes peuvent nécessiter la configuration du trafic d’entrée pour l’arrêt ou le routage SSL/TLS de plusieurs composants. Pour des raisons de sécurité, vous pouvez également être amené à limiter le flux du trafic réseau vers ou entre les nœuds et les pods.

Cet article présente les concepts fondamentaux qui fournissent la mise en réseau à vos applications dans AKS :

- [Services](#services)
- [Réseaux virtuels Azure](#azure-virtual-networks)
- [Contrôleurs d’entrée](#ingress-controllers)
- [Stratégies réseau](#network-policies)

## <a name="kubernetes-basics"></a>Concepts de base Kubernetes

Pour autoriser l’accès à vos applications ou pour que les composants d’application communiquent entre eux, Kubernetes fournit une couche d’abstraction à la mise en réseau virtuelle. Les nœuds Kubernetes sont connectés à un réseau virtuel et peuvent fournir la connectivité entrante et sortante pour les pods. Le composant *kube-proxy* s’exécute sur chaque nœud afin de fournir ces fonctionnalités de réseau.

Dans Kubernetes, les *services* regroupent logiquement les pods pour permettre un accès direct via une adresse IP ou un nom DNS et sur un port spécifique. Vous pouvez également distribuer le trafic à l’aide d’un *équilibreur de charge*. En outre, vous pouvez affiner le routage du trafic des applications avec des *contrôleurs d’entrée*. Sécurité et le filtrage du trafic réseau pour des pods est possible avec Kubernetes *stratégies réseau* (en version préliminaire dans ACS).

La plateforme Azure permet également de simplifier la mise en réseau virtuelle pour les clusters AKS. Quand vous créez un équilibreur de charge Kubernetes, la ressource d’équilibreur de charge Azure sous-jacente est créée et configurée. Quand vous ouvrez des ports réseau sur les pods, les règles de groupe de sécurité réseau Azure correspondantes sont configurées. Pour le routage d’application HTTP, Azure peut également configurer un *DNS externe* quand de nouvelles routes d’entrée sont configurées.

## <a name="services"></a>Services

Pour simplifier la configuration du réseau pour les charges de travail d’applications, Kubernetes utilise des *services* pour regrouper logiquement un ensemble de pods et fournir une connectivité réseau. Les types de service suivants sont disponibles :

- **ClusterIP** : crée une adresse IP interne pour une utilisation au sein du cluster AKS. Convient pour les applications internes uniquement qui prennent en charge d’autres charges de travail au sein du cluster.

    ![Diagramme montrant le flux de trafic ClusterIP dans un cluster AKS][aks-clusterip]

- **NodePort** : crée un mappage de port sur le nœud sous-jacent qui rend l’application accessible directement avec l’adresse IP du nœud et le port.

    ![Diagramme montrant le flux de trafic NodePort dans un cluster AKS][aks-nodeport]

- **LoadBalancer** : crée une ressource d’équilibreur de charge Azure, configure une adresse IP externe et connecte les pods demandés au pool back-end d’équilibreurs de charge. Pour que le trafic des clients puisse atteindre l’application, des règles d’équilibrage de charge sont créées sur les ports de votre choix. 

    ![Diagramme montrant le flux de trafic LoadBalancer dans un cluster AKS][aks-loadbalancer]

    Pour renforcer le contrôle et le routage du trafic entrant, vous pouvez plutôt utiliser un [contrôleur d’entrée](#ingress-controllers).

- **ExternalName** : crée une entrée DNS spécifique pour faciliter l’accès aux applications.

L’adresse IP pour les services et les équilibreurs de charge peut être affectée dynamiquement, ou vous pouvez spécifier une adresse IP statique existante à utiliser. Des adresses IP statiques internes et externes peuvent être affectées. Cette adresse IP statique existante est souvent liée à une entrée DNS.

Des équilibreurs de charge *internes* et *externes* peuvent être créés. Les équilibreurs de charge internes ne recevant qu’une adresse IP privée, ils ne sont pas accessibles à partir d’Internet.

## <a name="azure-virtual-networks"></a>Réseaux virtuels Azure

Dans AKS, vous pouvez déployer un cluster qui utilise un des deux modèles de réseau suivants :

- Mise en réseau *Kubenet* : les ressources réseau sont généralement créées et configurées quand le cluster AKS est déployé.
- Mise en réseau *Azure CNI (Container Networking Interface)*  : le cluster AKS est connecté à des configurations et ressources de réseau virtuel existantes.

### <a name="kubenet-basic-networking"></a>Mise en réseau Kubenet (de base)

L’option de mise en réseau *kubenet* est la configuration par défaut de la création de cluster AKS. Avec *kubenet*, les nœuds obtiennent une adresse IP du sous-réseau du réseau virtuel Azure. Les pods reçoivent une adresse IP du sous-réseau de réseau virtuel Azure des nœuds à partir d’un espace d’adressage logiquement différent. La traduction d’adresses réseau (NAT) est ensuite configurée afin que les pods puissent accéder aux ressources sur le réseau virtuel Azure. L’adresse IP source du trafic fait l’objet d’une opération NAT sur l’adresse IP principale du nœud.

Les nœuds utilisent le plug-in Kubernetes [kubenet][kubenet]. Vous pouvez laisser la plateforme Azure créer et configurer les réseaux virtuels pour vous ou choisir de déployer votre cluster AKS dans un sous-réseau de réseau virtuel existant. Là encore, seuls les nœuds recevoir une adresse IP routable et les pods utilisent NAT pour communiquer avec d’autres ressources à l’extérieur du cluster AKS. Cette approche réduit considérablement le nombre d’adresses IP que vous devez réserver dans votre espace réseau pour que les pods les utilisent.

Pour plus d’informations, consultez [Configurer une mise en réseau kubenet pour un cluster AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Mise en réseau Azure CNI (avancée)

Avec Azure CNI, chaque pod reçoit une adresse IP du sous-réseau et est accessible directement. Ces adresses IP doivent être uniques dans votre espace réseau et doivent être planifiées à l’avance. Chaque nœud possède un paramètre de configuration pour le nombre maximal de pods qu’il prend en charge. Le nombre équivalent d’adresses IP par nœud est alors réservé à l’avance pour ce nœud. Cette approche nécessite davantage de planification. De plus, elle conduit souvent à l’épuisement des adresses IP ou à la nécessité de regénérer les clusters dans un sous-réseau plus vaste à mesure que vos demandes d’applications augmentent.

Les nœuds utilisent le plug-in Kubernetes [Azure CNI (Container Networking Interface)][cni-networking].

![Diagramme représentant 2 nœuds avec des ponts les reliant chacun à un réseau virtuel Azure][advanced-networking-diagram]

Azure CNI fournit les fonctionnalités suivantes par rapport à la mise en réseau kubenet :

- Chaque pod dans le cluster se voit affecter une adresse IP dans le réseau virtuel. Les pods du cluster peuvent communiquer directement entre eux et avec les autres nœuds du réseau virtuel.
- Les pods dans un sous-réseau disposant de points de terminaison de service activés peuvent se connecter de manière sécurisée aux services Azure (tels que Stockage Azure et SQL Database).
- Vous pouvez créer des itinéraires définis par l’utilisateur pour acheminer le trafic des pods vers une appliance virtuelle réseau.

Pour plus d’informations, consultez [Configurer Azure CNI pour un cluster AKS][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Contrôleurs d’entrée

Quand vous créez un service de type LoadBalancer, une ressource d’équilibreur de charge Azure sous-jacente est créée. L’équilibreur de charge est configuré pour distribuer le trafic vers les pods dans votre service sur un port donné. L’équilibreur de charge fonctionne uniquement au niveau de la couche 4 : le service n’a pas connaissance des applications réelles et n’intervient pas dans le routage.

Les *contrôleurs d’entrée* fonctionnent au niveau de la couche 7 ; ils peuvent utiliser des règles plus intelligentes pour distribuer le trafic des applications. Une utilisation courante d’un contrôleur d’entrée consiste à router le trafic HTTP vers les différentes applications en fonction de l’URL entrante.

![Diagramme montrant le flux de trafic d’entrée dans un cluster AKS][aks-ingress]

Dans AKS, vous pouvez créer une ressource d’entrée à l’aide de NGINX, ou d’un outil similaire, ou utiliser la fonctionnalité de routage d’application HTTP AKS. Quand vous activez le routage d’application HTTP pour un cluster AKS, la plateforme Azure crée le contrôleur d’entrée et un contrôleur *DNS externe*. Quand des ressources d’entrée sont créées dans Kubernetes, les enregistrements DNS A requis sont créés dans une zone DNS propre au cluster. Pour plus d’informations, consultez [Déployer le routage d’applications HTTP][aks-http-routing].

Une autre fonctionnalité d’entrée courante est l’arrêt SSL/TLS. Sur les grandes applications web accessibles via HTTPS, l’arrêt TLS peut être géré par la ressource d’entrée plutôt que dans l’application proprement dite. Pour fournir la configuration et la génération automatiques de la certification TLS, vous pouvez configurer la ressource d’entrée pour utiliser des fournisseurs tels que Let's Encrypt. Pour plus d’informations sur la configuration d’un contrôleur d’entrée NGINX avec Let's Encrypt, consultez [Entrée et TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Groupes de sécurité réseau

Un groupe de sécurité réseau filtre le trafic pour des machines virtuelles, par exemple les nœuds AKS. Quand vous créez des services, tels qu’un équilibreur de charge, la plateforme Azure configure automatiquement toutes les règles de groupe de sécurité réseau nécessaires. Ne configurez pas manuellement des règles de groupe de sécurité réseau pour filtrer le trafic des pods dans un cluster AKS. Définissez les ports et le transfert requis dans le cadre de vos manifestes de services Kubernetes et laissez la plateforme Azure créer ou mettre à jour les règles appropriées. Vous pouvez également utiliser les stratégies de réseau, comme indiqué dans la section suivante, pour appliquer automatiquement les règles de filtre de trafic avec les pods.

## <a name="network-policies"></a>Stratégies réseau

Par défaut, tous les pods d’un cluster AKS peuvent envoyer et recevoir du trafic sans limitations. Pour une sécurité accrue, vous pouvez définir des règles qui contrôlent le flux de trafic. Les applications principales sont souvent exposées uniquement aux services frontaux obligatoires, ou les composants de base de données sont uniquement accessibles aux couches d’application qui s’y connectent.

Stratégie de réseau est une fonctionnalité de Kubernetes dans AKS qui vous permet de contrôler le flux de trafic entre les pods. Vous pouvez choisir d’autoriser ou de refuser un trafic en fonction de paramètres, tels que des étiquettes attribuées, un espace de noms ou un port de trafic. Les groupes de sécurité réseau sont plus adaptés aux nœuds AKS qu’aux pods. L’utilisation de stratégies réseau est un moyen plus adapté et natif du cloud de contrôler le flux de trafic. Les pods étant créés de façon dynamique dans un cluster AKS, les stratégies réseau nécessaires peuvent être appliquées automatiquement.

Pour plus d’informations, consultez [Sécuriser le trafic entre les pods avec des stratégies réseau dans Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec la mise en réseau AKS, créez et configurez un cluster AKS avec vos propres plages d’adresses IP à l’aide de [kubenet][aks-configure-kubenet-networking] ou d’[Azure CNI][aks-configure-advanced-networking].

Pour les recommandations associées, consultez [meilleures pratiques pour la connectivité de réseau et de sécurité dans AKS][operator-best-practices-network].

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
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
