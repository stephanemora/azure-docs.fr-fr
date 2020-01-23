---
title: Configurer la mise en réseau pour Azure Dev Spaces dans différentes topologies de réseau
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: Décrit la configuration réseau requise pour l’exécution d’Azure Dev Spaces dans Azure Kubernetes Service
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, CNI, kubenet, SDN, réseau
ms.openlocfilehash: 9e32e3b65451dceefaeeaf7faed7c8337797e0b8
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044994"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Configurer la mise en réseau pour Azure Dev Spaces dans différentes topologies de réseau

Azure Dev Spaces s’exécute sur les clusters Azure Kubernetes Service (AKS) avec la configuration de mise en réseau par défaut. Si vous souhaitez modifier la configuration de mise en réseau de votre cluster AKS, par exemple, placer le cluster derrière un pare-feu, utiliser des groupes de sécurité réseau ou utiliser des stratégies réseau, vous devez incorporer des considérations supplémentaires pour l’exécution d’Azure Dev Spaces.

![Configuration du réseau virtuel](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Configurations de réseau virtuel ou de sous-réseau

Votre cluster AKS peut avoir une configuration de réseau virtuel ou de sous-réseau différente pour limiter le trafic d’entrée ou de sortie pour votre cluster AKS. Par exemple, votre cluster peut se trouver derrière un pare-feu, tel qu’un Pare-feu Azure, ou vous pouvez utiliser des groupes de sécurité réseau ou des rôles personnalisés pour restreindre le trafic réseau.

Azure Dev Spaces présente certaines exigences en matière de trafic réseau *d’entrée et de sortie*, ainsi que de trafic *d’entrée uniquement*. Si vous utilisez Azure Dev Spaces sur un cluster AKS avec une configuration de réseau virtuel ou de sous-réseau qui limite le trafic pour votre cluster AKS, vous devez suivre les exigences de trafic d’entrée uniquement et de trafic d’entrée et de sortie suivantes pour qu’Azure Dev Spaces fonctionne correctement.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Exigences en matière de trafic réseau d’entrée et de sortie

Azure Dev Spaces a besoin d’un trafic d’entrée et de sortie pour les noms de domaine complets suivants :

| FQDN                       | Port       | Utilisation      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS : 443 | Pour tirer (pull) des images Docker pour Azure Dev Spaces |
| gcr.io                     | HTTPS : 443 | Pour tirer (pull) des images Helm pour Azure Dev Spaces |
| storage.googleapis.com     | HTTPS : 443 | Pour tirer (pull) des images Helm pour Azure Dev Spaces |
| azds-*.azds.io             | HTTPS : 443 | Pour communiquer avec les services back-end Azure Dev Spaces pour le contrôleur Azure Dev Spaces. Le nom de domaine complet exact est disponible dans *dataplaneFqdn*, dans `USERPROFILE\.azds\settings.json` |

Mettez à jour votre configuration de pare-feu ou de sécurité pour autoriser le trafic réseau vers et depuis tous les noms de domaine complets ci-dessus. Par exemple, si vous utilisez un pare-feu pour sécuriser votre réseau, les noms de domaine complets (FQDN) ci-dessus doivent être ajoutés à la règle d’application du pare-feu pour autoriser le trafic à destination et en provenance de ces domaines.

### <a name="ingress-only-network-traffic-requirements"></a>Exigences en matière de trafic réseau d’entrée uniquement

Azure Dev Spaces fournit un routage au niveau de l’espace de noms Kubernetes ainsi qu’un accès public aux services à l’aide de son propre nom de domaine complet. Pour que ces deux fonctionnalités fonctionnent, mettez à jour votre configuration de pare-feu ou réseau pour autoriser l’entrée publique à l’adresse IP externe du contrôleur d’entrée Azure Dev Spaces sur votre cluster. Vous pouvez également créer un [équilibreur de charge interne][aks-internal-lb] et ajouter une règle NAT dans votre pare-feu pour convertir l’adresse IP publique de votre pare-feu en adresse IP de votre équilibreur de charge interne. Vous pouvez également utiliser [traefik][traefik-ingress] ou [NGINX][nginx-ingress] pour créer un contrôleur d’entrée personnalisé.

## <a name="aks-cluster-network-requirements"></a>Configuration réseau requise pour le cluster AKS

AKS vous permet d’utiliser des [stratégies réseau][aks-network-policies] pour contrôler le trafic d’entrée et de sortie entre les pods sur un cluster, ainsi que le trafic de sortie depuis un pod. Azure Dev Spaces présente certaines exigences en matière de trafic réseau *d’entrée et de sortie*, ainsi que de trafic *d’entrée uniquement*. Si vous utilisez Azure Dev Spaces sur un cluster AKS avec des stratégies réseau AKS, vous devez suivre les exigences de trafic d’entrée uniquement et de trafic d’entrée et de sortie suivantes pour qu’Azure Dev Spaces fonctionne correctement.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Exigences en matière de trafic réseau d’entrée et de sortie

Azure Dev Spaces vous permet de communiquer directement avec un pod dans un espace de développement sur votre cluster à des fins de débogage. Pour que cette fonctionnalité fonctionne, ajoutez une stratégie réseau qui autorise la communication d’entrée et de sortie vers les adresses IP de l’infrastructure Azure Dev Spaces, qui [varient selon la région][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Exigences en matière de trafic réseau d’entrée uniquement

Azure Dev Spaces assure le routage entre les pods dans les espaces de noms. Par exemple, les espaces de noms où Azure Dev Spaces est activé peuvent avoir une relation parent/enfant, ce qui permet de router le trafic réseau entre les pods dans les espaces de noms parent et enfant. Pour que cette fonctionnalité fonctionne, ajoutez une stratégie réseau autorisant le trafic entre les espaces de noms où le trafic réseau est routé, tels que les espaces de noms parent/enfant. En outre, si le contrôleur d’entrée est déployé sur l’espace de noms *azds*, le contrôleur d’entrée doit communiquer avec les pods instrumentés par un espace Azure Dev Spaces dans un autre espace de noms. Pour que le contrôleur d’entrée fonctionne correctement, le trafic réseau doit être autorisé depuis l’espace de noms *azds* vers l’espace de noms où les pods instrumentés sont en cours d’exécution.

## <a name="using-azure-cni"></a>Utilisation d’Azure CNI

Par défaut, les clusters AKS sont configurés pour utiliser [kubenet][aks-kubenet] pour la mise en réseau, lequel fonctionne avec Azure Dev Spaces. Vous pouvez également configurer votre cluster AKS pour qu’il utilise [Azure Container Networking Interface (CNI)][aks-cni]. Pour utiliser Azure Dev Spaces avec Azure CNI sur votre cluster AKS, autorisez vos espaces d’adressage de réseau virtuel et de sous-réseau jusqu’à 10 adresses IP privées pour les pods déployés par Azure Dev Spaces. Plus d’informations sur les adresses IP privées sont disponibles dans la [documentation AKS Azure CNI][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Utilisation des plages d’adresses IP autorisées du serveur d’API

Les clusters AKS vous permettent de configurer une sécurité supplémentaire qui limite l’adresse IP pouvant interagir avec vos clusters, par exemple, en utilisant des réseaux virtuels personnalisés ou [en sécurisant l’accès au serveur d’API à l’aide de plages d’adresses IP autorisées][aks-ip-auth-ranges]. Pour utiliser Azure Dev Spaces dans le cadre de l’utilisation de cette sécurité supplémentaire lors de la [création][aks-ip-auth-range-create] de votre cluster, vous devez [autoriser des plages supplémentaires en fonction de votre région][dev-spaces-ip-auth-range-regions]. Vous pouvez également [mettre à jour][aks-ip-auth-range-update] un cluster existant pour autoriser ces plages supplémentaires. Vous devez également autoriser l’adresse IP de toutes les machines de développement qui se connectent à votre cluster AKS à des fins de débogage, à se connecter à votre serveur d’API.

## <a name="using-aks-private-clusters"></a>Utilisation des clusters privés AKS

À ce stade, Azure Dev Spaces n’est pas pris en charge avec des [clusters privés AKS][aks-private-clusters].

## <a name="client-requirements"></a>Configuration requise des clients

Azure Dev Spaces utilise des outils côté client, tels que l’extension d’interface CLI Azure Dev Spaces, l’extension Visual Studio Code et l’extension Visual Studio, pour communiquer avec votre cluster AKS à des fins de débogage. Pour utiliser les outils côté client Azure Dev Spaces, autorisez le trafic à partir des machines de développement vers le domaine *azds-\*.azds.io*. Consultez *dataplaneFqdn* dans `USERPROFILE\.azds\settings.json` pour connaître le nom de domaine complet exact. Si vous utilisez des [plages d’adresses IP autorisées du serveur d’API][auth-range-section], vous devez également autoriser l’adresse IP de toutes les machines de développement qui se connectent à votre cluster AKS à des fins de débogage, à se connecter à votre serveur d’API.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment Azure Dev Spaces vous aide à développer des applications plus complexes sur plusieurs conteneurs, et comment vous pouvez simplifier le développement collaboratif en utilisant différentes versions ou branches de votre code dans différents espaces.

> [!div class="nextstepaction"]
> [Développement en équipe dans Azure Dev Spaces][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md