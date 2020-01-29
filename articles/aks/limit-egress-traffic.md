---
title: Limiter le trafic de sortie dans Azure Kubernetes Service (AKS)
description: Découvrez les ports et adresses requis pour contrôler le trafic de sortie dans Azure Kubernetes Service (ACS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/21/2020
ms.author: mlearned
ms.openlocfilehash: df8b4d7ea44f885ee0fed0479ba87a4bc9ba1a29
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310167"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Contrôler le trafic de sortie pour les nœuds de cluster dans Azure Kubernetes Service (AKS)

Par défaut, les clusters AKS ont un accès illimité sortant à Internet. Ce niveau d’accès réseau permet aux nœuds et services que vous exécutez d’accéder aux ressources externes en fonction des besoins. Si vous souhaitez restreindre le trafic de sortie, un nombre limité de ports et adresses doit être accessible afin de gérer les tâches de maintenance d’intégrité du cluster. Votre cluster est configuré par défaut de manière à utiliser uniquement des images conteneur système de base provenant de Microsoft Container Registry (MCR) ou d’Azure Container Registry (ACR). Configurez vos règles de pare-feu et de sécurité préférées pour autoriser ces ports et adresses obligatoires.

Cet article décrit en détail les ports réseau et les noms de domaine complet (FQDN) obligatoires et facultatifs si vous limitez le trafic de sortie dans un cluster AKS.

> [!IMPORTANT]
> Ce document explique uniquement comment verrouiller le trafic sortant du sous-réseau AKS. AKS n’a aucun besoin d’entrée.  Le blocage du trafic de sous-réseau interne à l’aide de groupes de sécurité réseau (groupes NSG) et de pare-feu n’est pas pris en charge. Pour contrôler et bloquer le trafic au sein du cluster, utilisez des [stratégies réseau][network-policy].

## <a name="before-you-begin"></a>Avant de commencer

Azure CLI 2.0.66 (ou version ultérieure) doit être installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

## <a name="egress-traffic-overview"></a>Vue d’ensemble du trafic de sortie

Pour la gestion et à des fins opérationnelles, les nœuds d’un cluster AKS doivent accéder à certains ports et noms de domaine complet (FQDN). Ces actions peuvent consister à communiquer avec le serveur d’API, ou à télécharger, puis installer les principaux composants de cluster Kubernetes et des mises à jour de sécurité des nœuds. Par défaut, le trafic Internet de sortie (sortant) n’est pas limité pour les nœuds d’un cluster AKS. Le cluster peut extraire des images de conteneur système de base à partir de référentiels externes.

Pour renforcer la sécurité de votre cluster AKS, vous pouvez restreindre le trafic de sortie. Le cluster est configuré pour extraire des images de conteneur système de base à partir de MCR ou ACR. Si vous verrouillez le trafic de sortie de cette manière, définissez des ports et des noms de domaine complets spécifiques pour autoriser les nœuds AKS à communiquer correctement avec les services externes requis. Sans ces ports et noms de domaine complets autorisés, vos nœuds AKS sans dans l’impossibilité de communiquer avec le serveur d’API ou d’installer les composants principaux.

Vous pouvez utiliser le[Pare-feu Azure][azure-firewall] ou une appliance de pare-feu tierce pour sécuriser votre trafic de sortie et définir ces ports et adresses requis. AKS ne crée pas automatiquement ces règles pour vous. Les ports et adresses suivants sont fournis à titre de référence lorsque vous créez des règles appropriées dans votre pare-feu réseau.

> [!IMPORTANT]
> Lorsque vous utilisez le Pare-feu Azure pour limiter le trafic de sortie et créer un itinéraire défini par l’utilisateur (UDR) afin de forcer tout le trafic de sortie, veillez à créer une règle DNAT appropriée dans le Pare-feu pour autoriser le trafic d’entrée. L’utilisation du Pare-feu Azure avec une UDR perturbe la configuration d’entrée en raison d’un routage asymétrique (ce problème se produit si le sous-réseau AKS a un itinéraire par défaut qui conduit à l’adresse IP privée du pare-feu, alors que vous utilisez un service d’équilibreur de charge public, d’entrée ou Kubernetes de type : LoadBalancer). Dans ce cas, le trafic d’équilibreur de charge entrant est reçu par le biais de son adresse IP publique, mais le chemin de retour passe par l’adresse IP privée du pare-feu. Le pare-feu étant avec état, il supprime le paquet de retour, car le pare-feu n’a pas connaissance d’une session établie. Pour découvrir comment intégrer un Pare-feu Azure avec votre équilibreur de charge d’entrée ou de service, voir [Intégrer un pare-feu Azure avec Azure Standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Vous pouvez verrouiller le trafic pour le port TCP 9000 et le port TCP 22 à l’aide d’une règle de réseau entre les adresses IP du nœud Worker de sortie et l’adresse IP du serveur d’API.

Dans AKS, il existe deux jeux de ports et d’adresses :

* Les [ports et adresses requis pour les clusters AKS](#required-ports-and-addresses-for-aks-clusters) décrivent en détail la configuration minimale requise pour le trafic de sortie autorisé.
* Les [adresses et ports recommandés facultatifs pour les clusters AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) ne sont pas requis pour tous les scénarios. Cependant, l’intégration à d’autres services, tels qu’Azure Monitor ne fonctionnera pas correctement en leur absence. Consultez la liste des ports et noms de domaine complets facultatifs et autorisez les services et composants utilisés dans votre cluster AKS.

> [!NOTE]
> La limitation du trafic de sortie fonctionne uniquement sur les nouveaux clusters AKS. Pour les clusters existants, [effectuez une opération de mise à niveau de cluster][aks-upgrade] à l’aide de la commande `az aks upgrade` avant de limiter le trafic sortant.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Ports et adresses requis pour les clusters AKS

Les ports de sortie/règles de réseau suivants sont requis pour un cluster AKS :

* Port TCP *443*
* Le port TCP [IPAddrOfYourAPIServer]:443 est requis si vous avez une application qui doit communiquer avec le serveur d’API.  Cette modification peut être définie après la création du cluster.
* Port TCP *9000* et port TCP *22* pour que le pod avant de tunnel communique avec la fin du tunnel sur le serveur d’API.
    * Pour en savoir plus, reportez-vous aux adresses * *.hcp.\<location\>.azmk8s.io* et * *.tun.\<location\>.azmk8s.io* du tableau suivant.
* Port UDP *123* pour la synchronisation temporelle NTP (Network Time Protocol) (nœuds Linux).
* Le port UDP *53* pour DNS est également requis si vous avez des pods qui accèdent directement au serveur d’API.

Les noms de domaine complets/règles d’application suivantes sont requis :
- Azure Global

| FQDN                       | Port      | Utilisation      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Cette adresse est le point de terminaison du serveur d’API. Remplacez *\<location\>* par la région où votre cluster AKS est déployé. |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Cette adresse est le point de terminaison du serveur d’API. Remplacez *\<location\>* par la région où votre cluster AKS est déployé. |
| aksrepos.azurecr.io        | HTTPS:443 | Cette adresse est nécessaire pour l’accès aux images dans Azure Container Registry (ACR). Ce registre contient des images/graphiques tiers (par exemple, un serveur de métriques, un serveur DNS de base, etc.) nécessaires au fonctionnement du cluster pendant la mise à niveau et la mise à l’échelle du cluster.|
| *.blob.core.windows.net    | HTTPS:443 | Cette adresse est le magasin backend des images stockées dans ACR. |
| mcr.microsoft.com          | HTTPS:443 | Cette adresse est nécessaire pour l’accès aux images dans Microsoft Container Registry (MCR). Ce registre contient des images/graphiques internes (par exemple, moby, etc.) nécessaires au fonctionnement du cluster pendant la mise à niveau et la mise à l’échelle du cluster. |
| *.cdn.mscr.io              | HTTPS:443 | Cette adresse est requise pour le stockage MCR assuré par le réseau de distribution de contenu Azure (CDN). |
| management.azure.com       | HTTPS:443 | Cette adresse est requises pour les opérations GET/PUT de Kubernetes. |
| login.microsoftonline.com  | HTTPS:443 | Cette adresse est requise pour l’authentification Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Cette adresse est requise pour la synchronisation temporelle NTP sur des nœuds Linux. |
| packages.microsoft.com     | HTTPS:443 | Cette adresse est le référentiel de packages Microsoft utilisé pour les opérations *apt-get* mises en cache.  Moby, PowerShell et Azure CLI sont des exemples de packages. |
| acs-mirror.azureedge.net   | HTTPS:443 | Cette adresse correspond au référentiel requis pour installer les fichiers binaires requis comme kubenet et Azure CNI. |
- Azure China 21Vianet

| FQDN                       | Port      | Utilisation      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000 | Cette adresse est le point de terminaison du serveur d’API. Remplacez *\<location\>* par la région où votre cluster AKS est déployé. |
| *.tun.\<location\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000 | Cette adresse est le point de terminaison du serveur d’API. Remplacez *\<location\>* par la région où votre cluster AKS est déployé. |
| *.azk8s.cn        | HTTPS:443 | Cette adresse est nécessaire pour le téléchargement des fichiers binaires et des images requis|
| mcr.microsoft.com          | HTTPS:443 | Cette adresse est nécessaire pour l’accès aux images dans Microsoft Container Registry (MCR). Ce registre contient des images/graphiques internes (par exemple, moby, etc.) nécessaires au fonctionnement du cluster pendant la mise à niveau et la mise à l’échelle du cluster. |
| *.cdn.mscr.io              | HTTPS:443 | Cette adresse est requise pour le stockage MCR assuré par le réseau de distribution de contenu Azure (CDN). |
| management.chinacloudapi.cn       | HTTPS:443 | Cette adresse est requises pour les opérations GET/PUT de Kubernetes. |
| .chinacloudapi.cn  | HTTPS:443 | Cette adresse est requise pour l’authentification Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Cette adresse est requise pour la synchronisation temporelle NTP sur des nœuds Linux. |
| packages.microsoft.com     | HTTPS:443 | Cette adresse est le référentiel de packages Microsoft utilisé pour les opérations *apt-get* mises en cache.  Moby, PowerShell et Azure CLI sont des exemples de packages. |
- Azure Government

| FQDN                       | Port      | Utilisation      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000 | Cette adresse est le point de terminaison du serveur d’API. Remplacez *\<location\>* par la région où votre cluster AKS est déployé. |
| *.tun.\<location\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000 | Cette adresse est le point de terminaison du serveur d’API. Remplacez *\<location\>* par la région où votre cluster AKS est déployé. |
| aksrepos.azurecr.io        | HTTPS:443 | Cette adresse est nécessaire pour l’accès aux images dans Azure Container Registry (ACR). Ce registre contient des images/graphiques tiers (par exemple, un serveur de métriques, un serveur DNS de base, etc.) nécessaires au fonctionnement du cluster pendant la mise à niveau et la mise à l’échelle du cluster.|
| *.blob.core.windows.net    | HTTPS:443 | Cette adresse est le magasin backend des images stockées dans ACR. |
| mcr.microsoft.com          | HTTPS:443 | Cette adresse est nécessaire pour l’accès aux images dans Microsoft Container Registry (MCR). Ce registre contient des images/graphiques internes (par exemple, moby, etc.) nécessaires au fonctionnement du cluster pendant la mise à niveau et la mise à l’échelle du cluster. |
| *.cdn.mscr.io              | HTTPS:443 | Cette adresse est requise pour le stockage MCR assuré par le réseau de distribution de contenu Azure (CDN). |
| management.usgovcloudapi.net       | HTTPS:443 | Cette adresse est requises pour les opérations GET/PUT de Kubernetes. |
| login.microsoftonline.us  | HTTPS:443 | Cette adresse est requise pour l’authentification Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Cette adresse est requise pour la synchronisation temporelle NTP sur des nœuds Linux. |
| packages.microsoft.com     | HTTPS:443 | Cette adresse est le référentiel de packages Microsoft utilisé pour les opérations *apt-get* mises en cache.  Moby, PowerShell et Azure CLI sont des exemples de packages. |
| acs-mirror.azureedge.net   | HTTPS:443 | Cette adresse correspond au référentiel requis pour installer les fichiers binaires requis comme kubenet et Azure CNI. |
## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Adresses et ports recommandés facultatifs pour les clusters AKS

Les ports de sortie/règles de réseau suivants sont facultatifs pour un cluster AKS :

Les noms de domaine complets/règles d’application suivantes sont recommandés pour le bon fonctionnement des clusters AKS :

| FQDN                                    | Port      | Utilisation      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Cette adresse permet aux nœuds de cluster Linux de télécharger les correctifs et mises à jour de sécurité requis. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Adresses et ports requis pour les clusters AKS avec processeur graphique (GPU)

Les noms de domaine complets/règles d’application suivants sont requis pour les clusters AKS avec processeur graphique (GPU) activé :

| FQDN                                    | Port      | Utilisation      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Cette adresse est utilisée pour la bonne installation du pilote et un bon fonctionnement sur les nœuds basés sur le processeur graphique. |
| us.download.nvidia.com | HTTPS:443 | Cette adresse est utilisée pour la bonne installation du pilote et un bon fonctionnement sur les nœuds basés sur le processeur graphique. |
| apt.dockerproject.org | HTTPS:443 | Cette adresse est utilisée pour la bonne installation du pilote et un bon fonctionnement sur les nœuds basés sur le processeur graphique. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Adresses et ports requis avec Azure Monitor pour conteneurs activé

Les noms de domaine complets/règles d’application suivants sont requis pour les clusters AKS avec Azure Monitor pour conteneurs activé :

| FQDN                                    | Port      | Utilisation      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443  | Permet des métriques correctes et une bonne supervision des données de télémétrie à l’aide d’Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS:443 | Utilisé par Azure Monitor pour l’ingestion des données Log Analytics. |
| *.oms.opinsights.azure.com | HTTPS:443 | Cette adresse est utilisée par omsagent pour authentifier le service Log Analytics. |
|*.microsoftonline.com | HTTPS:443 | Permet d’authentifier et d’envoyer des métriques à Azure Monitor. |
|*.monitoring.azure.com | HTTPS:443 | Utilisé pour envoyer des données de métriques à Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Adresses et ports requis avec Azure Dev Spaces activé

Les règles de nom de domaine complet/d’application suivantes sont requises pour les clusters AKS avec Azure Dev Spaces activé :

| FQDN                                    | Port      | Utilisation      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Cette adresse est utilisée pour extraire des images Linux Alpine et d’autres Azure Dev Spaces Alpine |
| gcr.io | HTTP:443 | Cette adresse est utilisée pour extraire les images Helm/Tiller |
| storage.googleapis.com | HTTP:443 | Cette adresse est utilisée pour extraire les images Helm/Tiller |
| azds-<guid>.<location>.azds.io | HTTPS:443 | Pour communiquer avec les services back-end Azure Dev Spaces pour votre contrôleur. Le nom de domaine complet exact se trouve dans « dataplaneFqdn » dans %USERPROFILE%\.azds\settings.json |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Adresses et ports requis pour les clusters AKS avec Azure Policy (en préversion publique) activé

> [!CAUTION]
> Certaines des fonctionnalités ci-dessous sont en préversion.  Les suggestions de cet article sont susceptibles de changer à mesure que la fonctionnalité passe à la préversion publique et aux étapes de mise en production ultérieures.

Les noms de domaine complets/règles d’application suivants sont requis pour les clusters AKS avec Azure Policy activé.

| FQDN                                    | Port      | Utilisation      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Cette adresse est utilisée pour le bon fonctionnement d’Azure Policy. (actuellement en préversion dans AKS) |
| raw.githubusercontent.com | HTTPS:443 | Cette adresse est utilisée pour extraire les stratégies intégrées de GitHub afin de garantir le bon fonctionnement d’Azure Policy. (actuellement en préversion dans AKS) |
| *.gk.<location>.azmk8s.io | HTTPS:443 | Le module complémentaire Azure Policy communique avec le point de terminaison d’audit d’opérateur de contrôle en cours d’exécution sur le serveur maître pour obtenir les résultats d’audit. |
| dc.services.visualstudio.com | HTTPS:443 | Le module complémentaire Azure Policy envoie des données de télémétrie au point de terminaison Applications Insights. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Requis par les nœuds basés sur Windows Server (en préversion publique) activés

> [!CAUTION]
> Certaines des fonctionnalités ci-dessous sont en préversion.  Les suggestions de cet article sont susceptibles de changer à mesure que la fonctionnalité passe à la préversion publique et aux étapes de mise en production ultérieures.

Les noms de domaine complets/règles d’application suivants sont requis pour les clusters AKS basés sur Windows Server :

| FQDN                                    | Port      | Utilisation      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Pour installer les fichiers binaires liés à Windows |
| mp.microsoft.com, www<span></span>.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Pour installer les fichiers binaires liés à Windows |
| kms.core.windows.net | TCP:1688 | Pour installer les fichiers binaires liés à Windows |


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les ports et adresses à autoriser pour limiter le trafic de sortie pour le cluster. Vous pouvez également définir la manière dont les pods eux-mêmes peuvent communiquer et leurs restrictions au sein du cluster. Pour plus d’informations, consultez [Sécuriser le trafic entre les pods avec des stratégies réseau dans AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
