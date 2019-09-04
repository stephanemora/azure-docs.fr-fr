---
title: Limiter le trafic de sortie dans Azure Kubernetes Service (AKS)
description: Découvrez les ports et adresses requis pour contrôler le trafic de sortie dans Azure Kubernetes Service (ACS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: 9476290669606f6eb6c56b51497f3026b9613698
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034958"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Préversion - Limiter le trafic de sortie des nœuds de cluster et contrôler l’accès aux ports et services requis dans Azure Kubernetes Service (AKS)

Par défaut, les clusters AKS ont un accès illimité sortant à Internet. Ce niveau d’accès réseau permet aux nœuds et services que vous exécutez d’accéder aux ressources externes en fonction des besoins. Si vous souhaitez restreindre le trafic de sortie, un nombre limité de ports et adresses doit être accessible afin de gérer les tâches de maintenance d’intégrité du cluster. Votre cluster est ensuite configuré de manière à utiliser uniquement des images de conteneur système de base issues de Microsoft Container Registry (MCR) ou d’Azure Container Registry (ACR), et non des référentiels publics pas externes. Vous devez configurer vos règles de pare-feu et de sécurité préférées pour autoriser ces ports et adresses requis.

Cet article décrit en détail les ports réseau et les noms de domaine complet (FQDN) obligatoires et facultatifs si vous limitez le trafic de sortie dans un cluster AKS.  Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et font l’objet d’un abonnement. Les versions préliminaires sont fournies « en l’état », « avec toutes les erreurs » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service (sla) et de la garantie limitée. Les versions préliminaires AKS sont partiellement couvertes par le service clientèle sur la base du meilleur effort. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour obtenir des informations supplémentaires, veuillez lire les articles de support suivants :
>
> * [Stratégies de support AKS][aks-support-policies]
> * [FAQ du support Azure][aks-faq]

## <a name="before-you-begin"></a>Avant de commencer

Azure CLI 2.0.66 (ou version ultérieure) doit être installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

Pour créer un cluster AKS qui peut limiter le trafic de sortie, commencez par activer un indicateur de fonctionnalité sur votre abonnement. L’inscription de cette fonctionnalité permet de configurer tous les clusters AKS créés pour utiliser des images de conteneur système de base à partir de MCR ou ACR. Pour enregistrer l’indicateur de fonctionnalité *AKSLockingDownEgressPreview*, utilisez la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

> [!CAUTION]
> Lorsque vous inscrivez une fonctionnalité sur un abonnement, vous ne pouvez actuellement pas désinscrire cette fonctionnalité. Après avoir activé des fonctionnalités en préversion, des valeurs par défaut peuvent être utilisées pour tous les clusters AKS créés ultérieurement dans l’abonnement. N’activez pas les fonctionnalités d’évaluation sur les abonnements de production. Utilisez un abonnement distinct pour tester les fonctionnalités d’évaluation et recueillir des commentaires.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Vue d’ensemble du trafic de sortie

Pour la gestion et à des fins opérationnelles, les nœuds d’un cluster AKS doivent accéder à certains ports et noms de domaine complet (FQDN). Ces actions peuvent consister à communiquer avec le serveur d’API, ou à télécharger, puis installer les principaux composants de cluster Kubernetes et des mises à jour de sécurité des nœuds. Par défaut, le trafic Internet de sortie (sortant) n’est pas limité pour les nœuds d’un cluster AKS. Le cluster peut extraire des images de conteneur système de base à partir de référentiels externes.

Pour renforcer la sécurité de votre cluster AKS, vous pouvez restreindre le trafic de sortie. Le cluster est configuré pour extraire des images de conteneur système de base à partir de MCR ou ACR. Si vous verrouillez le trafic de sortie de cette manière, vous devez définir des ports et des noms de domaine complets spécifiques pour autoriser les nœuds AKS à communiquer correctement avec les services externes requis. Sans ces ports et noms de domaine complets autorisés, vos nœuds AKS sans dans l’impossibilité de communiquer avec le serveur d’API ou d’installer les composants principaux.

Vous pouvez utiliser le[Pare-feu Azure][azure-firewall] ou une appliance de pare-feu tierce pour sécuriser votre trafic de sortie et définir ces ports et adresses requis. AKS ne crée pas automatiquement ces règles pour vous. Les ports et adresses suivants sont fournis à titre de référence lorsque vous créez des règles appropriées dans votre pare-feu réseau.

> [!IMPORTANT]
> Lorsque vous utilisez le Pare-feu Azure pour limiter le trafic de sortie et créer un itinéraire défini par l’utilisateur (UDR) afin de forcer tout le trafic de sortie, veillez à créer une règle DNAT appropriée dans le Pare-feu pour autoriser le trafic d’entrée. L’utilisation du Pare-feu Azure avec une UDR perturbe la configuration d’entrée en raison d’un routage asymétrique (ce problème se produit parce que le sous-réseau AKS a un itinéraire par défaut qui conduit à l’adresse IP privée du pare-feu, alors que vous utilisez un service d’équilibreur de charge public, d’entrée ou Kubernetes de type : LoadBalancer). Dans ce cas, le trafic d’équilibreur de charge entrant est reçu par le biais de son adresse IP publique, mais le chemin de retour passe par l’adresse IP privée du pare-feu. Le pare-feu étant avec état, il supprime le paquet de retour, car le pare-feu n’a pas connaissance d’une session établie. Pour découvrir comment intégrer un Pare-feu Azure avec votre équilibreur de charge d’entrée ou de service, voir [Intégrer un pare-feu Azure avec Azure Standard Load Balancer](https://docs.microsoft.com/en-us/azure/firewall/integrate-lb).
>

Dans AKS, il existe deux jeux de ports et d’adresses :

* Les [ports et adresses requis pour les clusters AKS](#required-ports-and-addresses-for-aks-clusters) décrivent en détail la configuration minimale requise pour le trafic de sortie autorisé.
* Les [adresses et ports recommandés facultatifs pour les clusters AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) ne sont pas requis pour tous les scénarios. Cependant, l’intégration à d’autres services, tels qu’Azure Monitor ne fonctionnera pas correctement en leur absence. Consultez la liste des ports et noms de domaine complets facultatifs et autorisez les services et composants utilisés dans votre cluster AKS.

> [!NOTE]
> La limitation du trafic de sortie fonctionne uniquement sur les nouveaux clusters AKS créés après avoir activé l’inscription de l’indicateur de fonctionnalité. Pour les clusters existants, [effectuez une opération de mise à niveau de cluster][aks-upgrade] à l’aide de la commande `az aks upgrade` avant de limiter le trafic sortant.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Ports et adresses requis pour les clusters AKS

Les ports de sortie/règles de réseau suivants sont requis pour un cluster AKS :

* Port TCP *443*
* Port TCP *9000* et port TCP *22* pour que le pod avant de tunnel communique avec la fin du tunnel sur le serveur d’API.
    * Pour en savoir plus, reportez-vous aux adresses * *.hcp.\<location\>.azmk8s.io* et * *.tun.\<location\>.azmk8s.io* du tableau suivant.

Les noms de domaine complets/règles d’application suivantes sont requis :

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

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Adresses et ports recommandés facultatifs pour les clusters AKS

* Port UDP *53* pour DNS

Les noms de domaine complets/règles d’application suivantes sont recommandés pour le bon fonctionnement des clusters AKS :

| FQDN                                    | Port      | Utilisation      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com                           | HTTP:80   | Cette adresse permet aux nœuds de cluster Linux de télécharger les correctifs et mises à jour de sécurité requis. |
| packages.microsoft.com                  | HTTPS:443 | Cette adresse est le référentiel de packages Microsoft utilisé pour les opérations *apt-get* mises en cache. |
| dc.services.visualstudio.com            | HTTPS:443 | Recommandé pour des mesures et une surveillance correctes à l’aide d’Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS:443 | Recommandé pour des mesures et une surveillance correctes à l’aide d’Azure Monitor. |
| *.monitoring.azure.com                  | HTTPS:443 | Recommandé pour des mesures et une surveillance correctes à l’aide d’Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Cette adresse est utilisée pour le bon fonctionnement d’Azure Policy (actuellement en préversion dans AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Cette adresse est utilisée pour la bonne installation du pilote et un bon fonctionnement sur les nœuds basés sur le processeur graphique. |
| nvidia.github.io                        | HTTPS:443 | Cette adresse est utilisée pour la bonne installation du pilote et un bon fonctionnement sur les nœuds basés sur le processeur graphique. |

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
