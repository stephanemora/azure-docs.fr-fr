---
title: 'Concepts : mise en réseau dans AKS (Azure Kubernetes Service)'
description: Découvrez la mise en réseau dans AKS (Azure Kubernetes Service ), notamment la mise en réseau de base et avancée, les contrôleurs d’entrée, les équilibreurs de charge et les adresses IP statiques.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 62ba98f221041d5bbf9bb095a02d052218eb0fd0
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380654"
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

Dans Kubernetes, les *services* regroupent logiquement les pods pour permettre un accès direct via une adresse IP ou un nom DNS et sur un port spécifique. Vous pouvez également distribuer le trafic à l’aide d’un *équilibreur de charge*. En outre, vous pouvez affiner le routage du trafic des applications avec des *contrôleurs d’entrée*. La sécurité et le filtrage du trafic réseau pour les pods sont possibles avec des *stratégies réseau* Kubernetes.

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

- Mise en réseau *de base* : les ressources réseau sont créées et configurées quand le cluster AKS est déployé.
- Mise en réseau *avancée* : le cluster AKS est connecté à des configurations et ressources de réseau virtuel existantes.

### <a name="basic-networking"></a>Mise en réseau de base

L’option de mise en réseau *de base* est la configuration par défaut de la création de cluster AKS. La plateforme Azure gère la configuration réseau du cluster et des pods. La mise en réseau de base est appropriée pour les déploiements qui ne nécessitent pas de configuration de réseau virtuel personnalisée. Avec la mise en réseau de base, vous ne pouvez pas définir de configuration réseau, par exemple des noms de sous-réseau ou les plages d’adresses IP affectées au cluster AKS.

Les nœuds d’un cluster AKS configurés pour la mise en réseau de base utilisent le plug-in Kubernetes [kubenet][kubenet].

La mise en réseau de base fournit les fonctionnalités suivantes :

- Exposez un service Kubernetes en interne ou en externe via Azure Load Balancer.
- Les pods peuvent accéder aux ressources sur les réseaux Internet publics.

### <a name="advanced-networking"></a>Mise en réseau avancée

La mise en réseau *avancée* place vos pods dans un réseau virtuel Azure que vous configurez. Ce réseau virtuel fournit une connectivité automatique aux autres ressources Azure et une intégration à un large éventail de fonctionnalités. La mise en réseau avancée est appropriée pour les déploiements qui nécessitent des configurations de réseau virtuel spécifiques, par exemple pour utiliser un sous-réseau et une connectivité existants. Avec la mise en réseau avancée, vous pouvez définir ces noms de sous-réseaux et plages d’adresses IP.

Les nœuds d’un cluster AKS configurés pour la mise en réseau avancée utilisent le plug-in Kubernetes[Azure Container Networking Interface (CNI)][cni-networking].

![Diagramme représentant 2 nœuds avec des ponts les reliant chacun à un réseau virtuel Azure][advanced-networking-diagram]

La mise en réseau avancée fournit les fonctionnalités suivantes par rapport à la mise en réseau de base :

- Déployez votre cluster AKS sur un réseau virtuel existant ou créez un réseau virtuel et un sous-réseau pour votre cluster.
- Chaque pod dans le cluster se voit affecter une adresse IP dans le réseau virtuel. Les pods du cluster peuvent communiquer directement entre eux et avec les autres nœuds du réseau virtuel.
- Un pod peut se connecter à d’autres services dans un réseau virtuel appairé, notamment à des réseaux locaux via des connexions ExpressRoute et VPN site à site (S2S). Les pods sont également accessibles en local.
- Les pods dans un sous-réseau disposant de points de terminaison de service activés peuvent se connecter de manière sécurisée aux services Azure (tels que Stockage Azure et SQL Database).
- Vous pouvez créer des itinéraires définis par l’utilisateur pour acheminer le trafic des pods vers une appliance virtuelle réseau.

Pour plus d’informations, consultez [Configurer un réseau avancé pour un cluster AKS][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Contrôleurs d’entrée

Quand vous créez un service de type LoadBalancer, une ressource d’équilibreur de charge Azure sous-jacente est créée. L’équilibreur de charge est configuré pour distribuer le trafic vers les pods dans votre service sur un port donné. L’équilibreur de charge fonctionne uniquement au niveau de la couche 4 : le service n’a pas connaissance des applications réelles et n’intervient pas dans le routage.

Les *contrôleurs d’entrée* fonctionnent au niveau de la couche 7 ; ils peuvent utiliser des règles plus intelligentes pour distribuer le trafic des applications. Une utilisation courante d’un contrôleur d’entrée consiste à router le trafic HTTP vers les différentes applications en fonction de l’URL entrante.

![Diagramme montrant le flux de trafic d’entrée dans un cluster AKS][aks-ingress]

Dans AKS, vous pouvez créer une ressource d’entrée à l’aide de NGINX, ou d’un outil similaire, ou utiliser la fonctionnalité de routage d’application HTTP AKS. Quand vous activez le routage d’application HTTP pour un cluster AKS, la plateforme Azure crée le contrôleur d’entrée et un contrôleur *DNS externe*. Quand des ressources d’entrée sont créées dans Kubernetes, les enregistrements DNS A requis sont créés dans une zone DNS propre au cluster. Pour plus d’informations, consultez [Déployer le routage d’applications HTTP][aks-http-routing].

Une autre fonctionnalité d’entrée courante est l’arrêt SSL/TLS. Sur les grandes applications web accessibles via HTTPS, l’arrêt TLS peut être géré par la ressource d’entrée plutôt que dans l’application proprement dite. Pour fournir la configuration et la génération automatiques de la certification TLS, vous pouvez configurer la ressource d’entrée pour utiliser des fournisseurs tels que Let's Encrypt. Pour plus d’informations sur la configuration d’un contrôleur d’entrée NGINX avec Let's Encrypt, consultez [Entrée et TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Groupes de sécurité réseau

Un groupe de sécurité réseau filtre le trafic pour des machines virtuelles, telles que les nœuds AKS. Quand vous créez des services, tels qu’un équilibreur de charge, la plateforme Azure configure automatiquement toutes les règles de groupe de sécurité réseau nécessaires. Ne configurez pas manuellement des règles de groupe de sécurité réseau pour filtrer le trafic des pods dans un cluster AKS. Définissez les ports et le transfert requis dans le cadre de vos manifestes de services Kubernetes et laissez la plateforme Azure créer ou mettre à jour les règles appropriées.

Il existe des règles de groupe de sécurité réseau par défaut pour le trafic, tel que le trafic SSH. Ces règles par défaut concernent la gestion du cluster et la résolution des problèmes d’accès. La suppression de ces règles par défaut peut entraîner des problèmes liés à la gestion d’AKS et rompt l’objectif de niveau de service.

## <a name="next-steps"></a>Étapes suivantes

Pour vous familiariser avec la mise en réseau AKS, consultez [Créer et configurer une mise en réseau avancée pour un cluster AKS][aks-configure-advanced-networking].

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
[aks-configure-advanced-networking]: configure-advanced-networking.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md