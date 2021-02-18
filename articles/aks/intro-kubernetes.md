---
title: Présentation d’Azure Kubernetes Service
description: Découvrez les fonctionnalités et les avantages d’Azure Kubernetes Service pour déployer et gérer des applications en conteneur dans Azure.
services: container-service
ms.topic: overview
ms.date: 02/09/2021
ms.custom: mvc
ms.openlocfilehash: 1505366d9a91eac596b21804f93abb8245a84605
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590012"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) simplifie le déploiement d’un cluster Kubernetes managé dans Azure en déchargeant une grande partie de la complexité et de la surcharge opérationnelle sur Azure. En tant que service Kubernetes hébergé, Azure gère pour vous certaines tâches critiques comme l’analyse du fonctionnement et la maintenance.  

Sachant que les maîtres Kubernetes sont gérés par Azure, seules la gestion et la maintenance des nœuds agents vous incombent. Ainsi, en qualité de service Kubernetes managé, AKS est gratuit ; vous payez uniquement pour les nœuds agents au sein de vos clusters, pas pour les maîtres.  

Vous pouvez créer un cluster AKS à partir du portail Azure, d’Azure CLI, d’Azure PowerShell ou en utilisant les options de déploiement axées sur un modèle, comme les modèles Resource Manager et Terraform. Lors du déploiement d’un cluster AKS, le maître Kubernetes et tous les nœuds sont déployés et configurés pour vous. Des fonctionnalités supplémentaires telles que la mise en réseau avancée, l’intégration d’Azure Active Directory et la surveillance peuvent aussi être configurées lors du processus de déploiement. Les conteneurs Windows Server sont pris en charge dans AKS.

Pour plus d’informations sur les principes fondamentaux de Kubernetes, consultez [Concepts de base de Kubernetes pour AKS][concepts-clusters-workloads].

Pour commencer, effectuez le démarrage rapide AKS [sur le portail Azure][aks-portal] ou [avec Azure CLI][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Accès, sécurité et surveillance

Pour une sécurité et une gestion améliorées, AKS permet une intégration à Azure Active Directory (Azure AD) et l’exécution des tâches suivantes :
* Utilisation du contrôle d’accès en fonction du rôle Kubernetes (RBAC Kubernetes). 
* Analyse du fonctionnement de votre cluster et de vos ressources.

### <a name="identity-and-security-management"></a>Gestion de la sécurité et identité

Pour limiter l’accès aux ressources de cluster, AKS prend en charge le [contrôle RBAC Kubernetes][kubernetes-rbac]. Le contrôle RBAC Kubernetes vous permet de contrôler l’accès et les autorisations au niveau des ressources et des espaces de noms Kubernetes.  

Vous pouvez aussi configurer un cluster AKS afin de l’intégrer à Azure AD. Avec l’intégration Azure AD, vous pouvez configurer l’accès Kubernetes en fonction de l’identité et de l’appartenance de groupe existantes. Vos utilisateurs et groupes Azure AD existants peuvent profiter d’une expérience d’authentification intégrée et accéder aux ressources AKS.  

Pour plus d’informations sur l’identité, consultez [Options d’accès et d’identité pour AKS][concepts-identity].

Pour sécuriser vos clusters AKS, consultez [Intégrer Azure Active Directory à AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Fonctions de journalisation et de surveillance intégrées

Azure Monitor pour l’intégrité des conteneurs collecte des métriques de performances relatives au processeur et à la mémoire auprès des conteneurs, des nœuds et des contrôleurs au sein de votre cluster AKS et des applications déployées. Vous pouvez passer en revue les journaux des conteneurs et [les journaux du maître Kubernetes][aks-master-logs]. Ces données de supervision sont stockées dans un espace de travail Azure Log Analytics et sont disponibles via le portail Azure, Azure CLI ou un point de terminaison REST.

Pour plus d’informations, consultez [Superviser l’intégrité des conteneurs Azure Kubernetes Service][container-health].

## <a name="clusters-and-nodes"></a>Clusters et nœuds

Les nœuds AKS s’exécutent sur des machines virtuelles Azure. Avec des nœuds AKS, vous pouvez connecter le stockage à des nœuds et des pods, mettre à niveau les composants du cluster et utiliser des GPU. AKS prend en charge les clusters Kubernetes qui exécutent plusieurs pools de nœuds pour prendre en charge des systèmes d’exploitation mixtes et des conteneurs Windows Server.  

Pour plus d’informations sur les capacités des clusters, des nœuds et des pools de nœuds Kubernetes, consultez [Concepts de base de Kubernetes pour AKS][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Nœud de cluster et la mise à l’échelle de pod

Lorsque la demande des ressources change, le nombre de pods ou de nœuds du cluster qui exécutent vos services peut automatiquement monter ou descendre en puissance. Vous pouvez utiliser l’autoscaler de pod horizontal ou l’autoscaler de cluster. Cette approche de la mise à l’échelle permet au cluster AKS de s’ajuster automatiquement aux demandes et d’exécuter uniquement les ressources nécessaires.

Pour plus d’informations, consultez [Mettre à l’échelle un cluster Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Mises à niveau du nœud de cluster

AKS offre plusieurs versions de Kubernetes. Au fur et à mesure que de nouvelles versions sont disponibles dans AKS, votre cluster peut être mis à niveau via le portail Azure ou Azure CLI. Pendant le processus de mise à niveau, les nœuds sont soigneusement coordonnés et purgés afin de limiter les perturbations pour les applications en cours d’exécution.  

Pour plus d’informations sur les versions du cycle de vie, consultez [Versions de Kubernetes prises en charge dans AKS][aks-supported versions]. Pour connaître les étapes de la mise à niveau, consultez [Mettre à niveau un cluster Azure Kubernetes Service (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Nœuds avec processeur graphique (GPU)

AKS prend en charge la création de pools de nœuds avec GPU. Azure fournit actuellement une ou plusieurs machines virtuelles avec GPU. Les machines virtuelles avec GPU sont conçues pour des charges de travail de visualisation qui sont gourmandes en calcul et en graphisme.

Pour plus d’informations, consultez [Utilisation des GPU sur AKS][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Nœuds d’informatique confidentielle (préversion publique)

AKS prend en charge la création de pools de nœuds d’informatique confidentielle basés sur Intel SGX (machines virtuelles DCSv2). Les nœuds d’informatique confidentielle permettent aux conteneurs de s’exécuter dans un environnement d’exécution approuvé basé sur du matériel (enclaves). L’isolement entre conteneurs, combiné à l’intégrité du code par le biais de l’attestation, peut être utile pour votre stratégie de sécurité de conteneur de défense en profondeur. Les nœuds d’informatique confidentielle prennent en charge à la fois les conteneurs confidentiels (applications Docker existantes) et les conteneurs prenant en charge les enclaves.

Pour plus d’informations, consultez [Nœuds d’informatique confidentielle sur AKS][conf-com-node].

### <a name="storage-volume-support"></a>Prise en charge du volume de stockage

Pour supporter les charges de travail d’application, vous pouvez monter des volumes de stockage pour les données persistantes. Vous pouvez utiliser des volumes statiques et dynamiques. Selon le nombre de pods connectés censés partager les volumes de stockage, vous pouvez utiliser un stockage soutenu par Azure Disks pour l’accès d’un pod unique ou Azure Files pour l’accès simultané de plusieurs pods.

Pour plus d’informations, consultez [Options de stockage pour les applications dans AKS][concepts-storage].

Démarrez avec les volumes persistants dynamiques en utilisant [Disques Azure][azure-disk] ou [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Réseaux virtuels et entrée

Un cluster AKS peut être déployé dans un réseau virtuel existant. Dans cette configuration, chaque pod du cluster se voit attribuer une adresse IP dans le réseau virtuel et peut communiquer directement avec d’autres pods du cluster et d’autres nœuds du réseau virtuel. Les pods peuvent aussi se connecter à d’autres services dans un réseau virtuel appairé et à des réseaux locaux via ExpressRoute ou des connexions VPN site à site (S2S).  

Pour plus d’informations, consultez les [Concepts réseau pour les applications dans AKS][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Entrée avec un routage d’applications HTTP

Le module complémentaire de routage des applications HTTP permet d’accéder facilement aux applications déployées sur votre cluster AKS. Lorsqu’elle est activée, la solution de routage d’application HTTP configure un contrôleur d’entrée de votre cluster AKS.  

Des noms DNS accessibles publiquement sont configurés automatiquement à mesure que des applications sont déployées. Le routage d’applications HTTP configure une zone DNS et l’intègre au cluster AKS. Vous pouvez ensuite déployer des ressources d’entrée Kubernetes comme d’habitude.  

Pour vous familiariser avec le trafic d’entrée, consultez [Routage d’applications HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Intégration d’outils de développement

Kubernetes dispose d’un riche écosystème d’outils de développement et de gestion qui fonctionnent de façon harmonieuse avec AKS. Parmi ces outils figurent Helm et l’extension Kubernetes pour Visual Studio Code. Ces outils fonctionnent parfaitement avec AKS.  

Par ailleurs, Azure propose plusieurs outils qui permettent de rationaliser Kubernetes, comme Azure Dev Spaces et DevOps Starter.  

Azure Dev Spaces offre aux équipes une expérience de développement Kubernetes rapide et itérative. Avec une configuration minimale, vous pouvez exécuter et déboguer des conteneurs directement dans AKS. Pour commencer, consultez [Azure Dev Spaces][azure-dev-spaces].

DevOps Starter fournit une solution simple pour apporter le code et les dépôts Git existants dans Azure. DevOps Starter effectue automatiquement les tâches suivantes :
* Création de ressources Azure (comme AKS). 
* Configuration d’un pipeline de mise en production dans Azure DevOps Services qui inclut un pipeline de build pour CI. 
* Configuration d’un pipeline de mise en production pour CD. 
* Génération d’une ressource Azure Application Insights pour la supervision. 

Pour plus d’informations, consultez [DevOps Starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Prise en charge des images Docker et Registre de conteneurs privé

AKS prend en charge le format d’image Docker. Vous pouvez intégrer AKS à Azure Container Registry (ACR) pour stocker vos images Docker en privé.

Pour créer un magasin d’images privé, consultez [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Certification Kubernetes

AKS a été certifié conforme à Kubernetes par CNCF.

## <a name="regulatory-compliance"></a>Conformité aux normes

AKS est conforme aux normes SOC, ISO, PCI DSS et HIPAA. Pour plus d’informations, consultez [Présentation de la conformité Microsoft Azure][compliance-doc].

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le déploiement et la gestion d’AKS avec le démarrage rapide Azure CLI.

> [!div class="nextstepaction"]
> [Guide de démarrage rapide AKS][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
