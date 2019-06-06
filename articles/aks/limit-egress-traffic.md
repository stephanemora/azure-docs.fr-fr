---
title: Restreindre le trafic de sortie dans Azure Kubernetes Service (ACS)
description: Découvrez quels ports et les adresses sont requis pour contrôler le trafic de sortie dans Azure Kubernetes Service (ACS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: b5a203150906758bde33431a1dab717e090f2e28
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475579"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Version préliminaire - le trafic de sortie de limite pour les nœuds de cluster et de contrôler l’accès aux services dans Azure Kubernetes Service (ACS) et les ports requis

Par défaut, les clusters AKS sortant (sortie) internet un accès illimité. Ce niveau d’accès réseau permet de nœuds et les services que vous exécutez pour accéder aux ressources externes en fonction des besoins. Si vous souhaitez restreindre le trafic de sortie, un nombre limité de ports et adresses doit être accessible à mettre à jour les tâches de maintenance d’intégrité du cluster. Votre cluster est ensuite configuré pour utiliser uniquement des images de conteneur système de base à partir du Registre de conteneur Microsoft (MCR) ou Azure Container Registry (ACR), les référentiels publics pas externes.

Cet article décrit en détail les ports réseau et les noms de domaine complet (FQDN) sont obligatoires et facultatifs si vous limitez le trafic de sortie dans un cluster AKS.  Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

> [!IMPORTANT]
> Fonctionnalités de préversion AKS sont en libre-service, participer. Elles sont fournies pour recueillir des commentaires et des bogues à partir de notre communauté. Dans la version préliminaire, ces fonctionnalités ne sont pas destinées à des fins de production. Fonctionnalités en version préliminaire publique relèvent du « meilleur effort » la prise en charge. L’assistance des équipes de support technique AKS est disponible pendant les heures de bureau PST fuseau horaire (PST) uniquement. Pour plus d’informations, consultez les éléments suivants prennent en charge des articles :
>
> * [Stratégies de prise en charge AKS][aks-support-policies]
> * [FAQ du Support Azure][aks-faq]

## <a name="before-you-begin"></a>Avant de commencer

Vous avez besoin d’Azure CLI version 2.0.61 ou ultérieur installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][install-azure-cli].

Pour créer un cluster AKS qui peut limiter le trafic sortant, d’abord activer un indicateur de fonctionnalité sur votre abonnement. Cette inscription de fonctionnalité configure tous les clusters AKS crées pour utiliser des images de conteneur système de base à partir de MCR ou ACR. Pour inscrire le *AKSLockingDownEgressPreview* indicateur des fonctionnalités, utilisez le [register de fonctionnalité az] [ az-feature-register] commande comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l’état d’inscription à l’aide de la [liste des fonctionnalités az] [ az-feature-list] commande :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription de le *Microsoft.ContainerService* fournisseur de ressources à l’aide de la [register de fournisseur az] [ az-provider-register] commande :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Vue d’ensemble du trafic de sortie

Pour la gestion et à des fins opérationnelles, les nœuds dans un cluster ACS doivent accéder à certains ports et les noms de domaine complet (FQDN). Ces actions peut consister à communiquer avec le serveur d’API, ou à télécharger, puis installer les composants de cluster Kubernetes et des mises à jour de sécurité de nœud. Par défaut, le trafic de sortie (sortant) internet n’est pas limité pour les nœuds dans un cluster AKS. Le cluster peut extraire système de base des images de conteneur à partir de référentiels externes.

Pour renforcer la sécurité de votre cluster AKS, vous pouvez souhaiter restreindre le trafic de sortie. Le cluster est configuré pour extraire des images de conteneur à partir de MCR ou ACR de système de base. Si vous verrouillez le trafic de sortie de cette manière, vous devez définir des noms de domaine complets pour autoriser les nœuds AKS communiquer correctement avec les services externes requis et des ports spécifiques. Sans ces ports autorisés et les noms de domaine complets, vos nœuds AKS ne peut pas communiquer avec le serveur d’API ou installer les composants principaux.

Vous pouvez utiliser [pare-feu Azure] [ azure-firewall] ou une appliance de pare-feu de 3 rd-party pour sécuriser votre trafic de sortie et de définir ces requise des ports et adresses.

Dans ACS, il existe deux jeux de ports et adresses :

* Le [ports et adresse requis pour les clusters AKS](#required-ports-and-addresses-for-aks-clusters) décrit en détail la configuration minimale requise pour le trafic sortant autorisé.
* Le [recommandé facultatif adresses et ports pour les clusters AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) ne sont pas requis pour tous les scénarios, mais l’intégration avec d’autres services tels que Azure Monitor ne fonctionnera pas correctement. Vérifiez la liste des ports facultatifs et les noms de domaine complets et autoriser un des services et composants utilisés dans votre cluster AKS.

> [!NOTE]
> Limiter le trafic de sortie fonctionne uniquement sur les nouveaux clusters AKS créés après avoir activé l’inscription d’indicateur de fonctionnalité. Pour les clusters existants, [effectuer une opération de mise à niveau de cluster] [ aks-upgrade] à l’aide de la `az aks upgrade` commande avant de vous limiter le trafic sortant.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Ports requis et les adresses pour les clusters AKS

Les ports de sortie suivantes ou les règles de réseau sont requises pour un cluster ACS :

* Le port TCP *443*
* Le port TCP *9000* et le port TCP *22* pour le pod avant de tunnel communiquer avec la fin du tunnel sur le serveur d’API.

Le nom de domaine complet suivant ou les règles d’application sont requis :

| FQDN                      | Port      | Utilisation      |
|---------------------------|-----------|----------|
| *.azmk8s.io               | HTTPS:443,22,9000 | Cette adresse est le point de terminaison de serveur API. |
| aksrepos.azurecr.io       | HTTPS:443 | Cette adresse est nécessaire pour l’accès aux images dans Azure Container Registry (ACR). |
| *.blob.core.windows.net   | HTTPS:443 | Cette adresse est le magasin principal pour les images stockées dans l’ACR. |
| mcr.microsoft.com         | HTTPS:443 | Cette adresse est nécessaire pour l’accès aux images dans le Registre de conteneur Microsoft (MCR). |
| management.azure.com      | HTTPS:443 | Cette adresse est obligatoire pour les opérations de Kubernetes GET/PUT. |
| login.microsoftonline.com | HTTPS:443 | Cette adresse est requise pour l’authentification Azure Active Directory. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Facultatif recommandé adresses et ports pour les clusters AKS

Les ports de sortie suivants / règles de réseau ne sont pas nécessaires pour les clusters AKS de fonctionner correctement, mais sont recommandées :

* Le port UDP *123* pour la synchronisation de temps NTP
* Le port UDP *53* pour DNS

Le nom de domaine complet suivant / règles d’application sont recommandés pour les clusters AKS de fonctionner correctement :

| FQDN                                    | Port      | Utilisation      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Cette adresse permet de télécharger les correctifs de sécurité requis et les mises à jour les nœuds de cluster Linux. |
| packages.microsoft.com                  | HTTPS:443 | Cette adresse est le référentiel de packages Microsoft utilisé pour la mise en cache *apt-get* operations. |
| dc.services.visualstudio.com            | HTTPS:443 | Recommandé pour les mesures correctes et de surveillance à l’aide d’Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS:443 | Recommandé pour les mesures correctes et de surveillance à l’aide d’Azure Monitor. |
| *.monitoring.azure.com                  | HTTPS:443 | Recommandé pour les mesures correctes et de surveillance à l’aide d’Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Cette adresse est utilisée pour le bon fonctionnement d’Azure Policy (actuellement en version préliminaire dans ACS). |
| apt.dockerproject.org                   | HTTPS:443 | Cette adresse est utilisée pour l’installation du pilote approprié et l’opération sur les nœuds basés sur le GPU. |
| nvidia.github.io                        | HTTPS:443 | Cette adresse est utilisée pour l’installation du pilote approprié et l’opération sur les nœuds basés sur le GPU. |

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris les ports et les adresses pour autoriser si vous restreindre le trafic de sortie pour le cluster. Vous pouvez également définir la façon dont les pods eux-mêmes peuvent communiquer et quelles restrictions qu’ils ont au sein du cluster. Pour plus d’informations, consultez [sécuriser le trafic entre les pods dans ACS à l’aide de stratégies de réseau][network-policy].

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
