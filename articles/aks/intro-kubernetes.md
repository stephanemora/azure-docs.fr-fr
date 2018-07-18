---
title: Présentation d’Azure Kubernetes Service
description: Azure Kubernetes Service simplifie le déploiement et la gestion des applications en conteneur dans Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: cb38285a009d8dfba175de6e3037970e6111d929
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096125"
---
# <a name="azure-kubernetes-service-aks"></a>Service Azure Kubernetes (AKS)

Azure Kubernetes Service (AKS) simplifie le déploiement d’un cluster Kubernetes géré dans Azure. AKS permet de réduire la complexité et la surcharge opérationnelle de la gestion d’un cluster Kubernetes en déléguant une grande partie de cette responsabilité à Azure. En tant que service Kubernetes hébergé, Azure gère pour vous des tâches critiques telles que l’analyse de l'intégrité et la maintenance. En outre, le service est gratuit. Vous payez uniquement pour les nœuds de l’agent au sein de vos clusters, pas pour les maîtres.

Ce document fournit une vue d’ensemble des fonctionnalités d’Azure Kubernetes Service (AKS).

## <a name="flexible-deployment-options"></a>Options de déploiement flexibles

Azure Kubernetes Service offre des options de déploiement par portail, ligne de commande et via un modèle (modèle Resource Manager et Terraform). Lors du déploiement d’un cluster AKS, le maître Kubernetes et tous les nœuds sont déployés et configurés pour vous. Des fonctionnalités supplémentaires telles que la mise en réseau avancée, l’intégration d’Azure Active Directory et la surveillance peuvent aussi être configurées lors du processus de déploiement.

Pour en savoir plus, consultez le [Guide de démarrage rapide pour le portail AKS][aks-portal] ou le [Guide de démarrage rapide de l’interface de ligne de commande AKS][aks-cli].

## <a name="identity-and-security-management"></a>Gestion de la sécurité et identité

Les clusters AKS prennent en charge le [contrôle d'accès en fonction du rôle (RBAC)][kubernetes-rbac]. Un cluster AKS peut aussi être configuré pour s’intégrer à Azure Active Directory. Dans cette configuration, l’accès à Kubernetes peut être configuré en fonction de l’identité et de l’appartenance du groupe d’Azure Active Directory.

Pour en savoir plus, consultez [Intégrer Azure Active Directory à AKS][aks-aad].

## <a name="integrated-logging-and-monitoring"></a>Fonctions de journalisation et de surveillance intégrées

L’intégrité du conteneur vous offre une visibilité des performances en collectant des métriques sur la mémoire et le processeur à partir des contrôleurs, nœuds et conteneurs. Les journaux de conteneur sont aussi collectés. Ces données sont stockées dans l’espace de travail Log Analytics, et sont disponibles via le portail Azure, Azure CLI ou un point de terminaison REST.

Pour plus d’informations, voir [Analyser le fonctionnement des conteneurs Azure Kubernetes Service][container-health].

## <a name="cluster-node-scaling"></a>Mise à l'échelle du nœud de cluster

Avec la hausse de la demande en ressources, les nœuds d’un cluster AKS peuvent être augmentés en fonction des besoins. Si la demande en ressources diminue, les nœuds peuvent être supprimés en redimensionnant le cluster. Les opérations de mise à l'échelle d’AKS peuvent être effectuées via le portail Azure ou Azure CLI.

Pour plus d’informations, consultez [Mettre à l’échelle un cluster Azure Kubernetes Service (AKS)][aks-scale].

## <a name="cluster-node-upgrades"></a>Mises à niveau du nœud de cluster

Azure Kubernetes Service offre plusieurs versions de Kubernetes. Au fur et à mesure que de nouvelles versions sont disponibles dans AKS, votre cluster peut être mis à niveau via le portail Azure ou Azure CLI. Pendant le processus de mise à niveau, les nœuds sont soigneusement coordonnés et purgés afin de limiter les perturbations pour les applications en cours d’exécution.

Pour plus d’informations, consultez [Mettre à niveau un cluster Azure Kubernetes Service (AKS)][aks-upgrade].

## <a name="http-application-routing"></a>Routage d’applications HTTP

La solution de routage des applications HTTP permet d’accéder facilement aux applications déployées sur votre cluster AKS. Lorsqu’elle est activée, la solution de routage d’application HTTP configure un contrôleur d’entrée de votre cluster AKS. Lorsque les applications sont déployées, les noms de DNS accessibles publiquement sont configurés automatiquement.

Pour plus d’informations, consultez [Routage d’application HTTP][aks-http-routing].

## <a name="gpu-enabled-nodes"></a>Nœuds avec processeur graphique (GPU)

AKS prend en charge la création de pools de nœuds avec processeur graphique (GPU). Azure fournit actuellement une ou plusieurs machines virtuelles avec processeur graphique (GPU). Les machines virtuelles avec processeur graphique (GPU) sont conçues pour des charges de travail de visualisation, mais également de calcul et d’affichage graphique intensifs.

Pour plus d’informations, consultez [Utilisation de processeurs graphiques (GPU) sur AKS][aks-gpu].

## <a name="development-tooling-integration"></a>Intégration d’outils de développement

Kubernetes dispose d’un écosystème riche en outils de gestion et de développement, tels que Helm,Draft et l’extension Kubernetes pour Visual Studio Code. Ces outils fonctionnent de façon fluide avec Azure Kubernetes Service.

De plus, Azure Dev Spaces offre aux équipes une expérience de développement Kubernetes rapide et itérative. Avec une configuration minimale, vous pouvez exécuter et déboguer directement dans Azure Kubernetes Service (AKS).

Pour plus d’informations, consultez [Azure Dev Spaces][azure-dev-spaces].

Le projet Azure DevOps fournit une solution simple pour apporter le code et le référentiel Git existant dans Azure. Le projet DevOps crée automatiquement des ressources Azure telles qu’AKS, un pipeline de mise en production dans VSTS comprenant une définition de build pour intégration continue, configure une définition de mise en production pour déploiement continu, puis crée une ressource Azure Application Insights pour la surveillance.

Pour plus d’informations, consultez [Projet Azure DevOps][azure-devops].

## <a name="virtual-network-integration"></a>Intégration du réseau virtuel

Un cluster AKS peut être déployé dans un réseau virtuel existant. Dans cette configuration, chaque pod du cluster se voit affecter une adresse IP dans le réseau virtuel et peut communiquer directement avec les autres pods du cluster et les autres nœuds du réseau virtuel. Les pods peuvent se connecter à d’autres services dans un réseau virtuel appairé, et à d’autres réseaux locaux via des connexions ExpressRoute et VPN site à site (S2S).

Pour plus d’informations, consultez [Vue d’ensemble de la mise en réseau AKS][aks-networking].

## <a name="private-container-registry"></a>Registre de conteneurs privé

Intégrez à Azure Container Registry (ACR) pour stocker vos images Docker en privé.

Pour plus d’informations, consultez [Azure Container Registry (ACR)][acr-docs].

## <a name="storage-volume-support"></a>Prise en charge du volume de stockage

Azure Kubernetes Service (AKS) prend en charge le montage de volumes de stockage pour des données persistantes. Les clusters AKS sont créés avec une prise en charge d’Azure Files et Azure Disks.

Pour plus d’informations, consultez [Azure Files][azure-files] et [Azure Disks][azure-disk].

## <a name="docker-image-support"></a>Prise en charge d’images Docker

Azure Kubernetes Service prend en charge le format image Docker.

## <a name="kubernetes-certification"></a>Certification Kubernetes

Azure Kubernetes Service (AKS) a été certifié conforme à Kubernetes par CNCF.

## <a name="regulatory-compliance"></a>Conformité aux normes

Azure Kubernetes Service (AKS) est conforme aux systèmes sur puce et ISO/HIPAA/HITRUST.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le déploiement et la gestion des ACS avec ACS démarrage rapide.

> [!div class="nextstepaction"]
> [Guide de démarrage rapide AKS][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/vsts/pipelines/actions/azure-devops-project-aks?view=vsts
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

