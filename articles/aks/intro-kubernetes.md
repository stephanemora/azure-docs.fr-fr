---
title: Présentation d’Azure Kubernetes Service
description: Découvrez les fonctionnalités et les avantages d’Azure Kubernetes Service pour déployer et gérer des applications en conteneur dans Azure.
services: container-service
ms.topic: overview
ms.date: 02/24/2021
ms.custom: mvc
ms.openlocfilehash: 315230a19967e4006e378b836b0ea70f6b5c19b9
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110094291"
---
# <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service (AKS) simplifie le déploiement d’un cluster Kubernetes managé dans Azure en déchargeant la surcharge opérationnelle sur Azure. En tant que service Kubernetes hébergé, Azure gère des tâches critiques telles que l’analyse de l’intégrité et la maintenance. Sachant qu’Azure gère les maîtres Kubernetes, vous n’assurer la gestion et la maintenance que des nœuds agents. En outre, AKS étant gratuit, vous payez uniquement pour les nœuds agents au sein de vos clusters, pas pour les maîtres.  

Vous créez un cluster AKS à l’aide des ressources suivantes :
* [L’interface de ligne de commande Microsoft Azure](kubernetes-walkthrough.md)
* [Le portail Azure](kubernetes-walkthrough-portal.md)
* [Azure PowerShell](kubernetes-walkthrough-powershell.md)
* Utilisation d’options de déploiement basées sur des modèles, comme les [modèles Azure Resource Manager](kubernetes-walkthrough-rm-template.md) et Terraform 

Lors du déploiement d’un cluster AKS, le maître Kubernetes et tous les nœuds sont déployés et configurés pour vous. Au cours du processus de déploiement, vous pouvez configurer la mise en réseau avancée, l’intégration d’Azure Active Directory (Azure AD), la surveillance et d’autres fonctionnalités. 

Pour plus d’informations sur les principes fondamentaux de Kubernetes, consultez [Concepts de base de Kubernetes pour AKS][concepts-clusters-workloads].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]
> AKS prend également en charge les conteneurs Windows Server.

## <a name="access-security-and-monitoring"></a>Accès, sécurité et surveillance

Pour une sécurité et une gestion améliorées, AKS vous permet d’opérer une intégration avec Azure AD aux fins suivantes :
* Utilisation du contrôle d’accès en fonction du rôle Kubernetes (RBAC Kubernetes). 
* Analyse du fonctionnement de votre cluster et de vos ressources.

### <a name="identity-and-security-management"></a>Gestion de la sécurité et identité

#### <a name="kubernetes-rbac"></a>Contrôle RBAC Kubernetes

Pour limiter l’accès aux ressources de cluster, AKS prend en charge le [contrôle RBAC Kubernetes][kubernetes-rbac]. Le RBAC Kubernetes contrôle l’accès et les autorisations en lien avec les ressources et espaces de noms Kubernetes.  

#### <a name="azure-ad"></a>Azure AD

Vous pouvez configurer un cluster AKS afin d’opérer une intégration avec Azure AD. L’intégration avec Azure AD vous permet de configurer l’accès Kubernetes en fonction de l’identité et de l’appartenance de groupe existantes. Vos utilisateurs et groupes Azure AD existants peuvent profiter d’une expérience d’authentification intégrée et accéder aux ressources AKS.  

Pour plus d’informations sur l’identité, consultez [Options d’accès et d’identité pour AKS][concepts-identity].

Pour sécuriser vos clusters AKS, consultez [Intégrer Azure Active Directory à AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Fonctions de journalisation et de surveillance intégrées

Azure Monitor pour l’intégrité des conteneurs collecte des métriques de performances relatives au processeur et à la mémoire auprès des conteneurs, des nœuds et des contrôleurs au sein de votre cluster AKS et des applications déployées. Vous pouvez consulter les journaux de conteneurs et les [journaux maîtres Kubernetes][aks-master-logs] qui sont :
* stockés dans un espace de travail Azure Log Analytics ;
* disponibles via le portail Azure, Azure CLI ou un point de terminaison REST.

Pour plus d’informations, consultez [Superviser l’intégrité des conteneurs Azure Kubernetes Service][container-health].

## <a name="clusters-and-nodes"></a>Clusters et nœuds

Les nœuds AKS s’exécutent sur des machines virtuelles Azure. Avec des nœuds AKS, vous pouvez connecter le stockage à des nœuds et des pods, mettre à niveau les composants du cluster et utiliser des GPU. AKS prend en charge les clusters Kubernetes qui exécutent plusieurs pools de nœuds pour prendre en charge des systèmes d’exploitation mixtes et des conteneurs Windows Server.  

Pour plus d’informations sur les fonctionnalités de cluster, de nœud et des pool de nœuds Kubernetes, consultez [Concepts de base de Kubernetes pour AKS][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Nœud de cluster et la mise à l’échelle de pod

À mesure que la demande de ressources change, le nombre de nœuds de cluster ou de pods qui exécutent vos services est mis à l’échelle automatiquement. Vous pouvez ajuster le module de mise à l’échelle automatique du pod horizontal ou du cluster en fonction des demandes et exécuter uniquement les ressources nécessaires.

Pour plus d’informations, consultez [Mettre à l’échelle un cluster Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Mises à niveau du nœud de cluster

AKS offre plusieurs versions de Kubernetes. Au fur et à mesure de la disponibilité de nouvelles versions dans AKS, vous pouvez mettre à niveau votre cluster à l’aide du portail Azure ou d’Azure CLI. Pendant le processus de mise à niveau, les nœuds sont soigneusement coordonnés et purgés afin de limiter les perturbations pour les applications en cours d’exécution.  

Pour plus d’informations sur les versions du cycle de vie, consultez [Versions de Kubernetes prises en charge dans AKS][aks-supported versions]. Pour connaître les étapes de la mise à niveau, consultez [Mettre à niveau un cluster Azure Kubernetes Service (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Nœuds avec processeur graphique (GPU)

AKS prend en charge la création de pools de nœuds avec GPU. Azure fournit actuellement une ou plusieurs machines virtuelles avec GPU. Les machines virtuelles avec GPU sont conçues pour des charges de travail de visualisation qui sont gourmandes en calcul et en graphisme.

Pour plus d’informations, consultez [Utilisation des GPU sur AKS][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Nœuds d’informatique confidentielle (préversion publique)

AKS prend en charge la création de pools de nœuds d’informatique confidentielle basés sur Intel SGX (machines virtuelles DCSv2). Les nœuds d’informatique confidentielle permettent aux conteneurs de s’exécuter dans un environnement d’exécution approuvé basé sur du matériel (enclaves). L’isolement entre conteneurs, combiné à l’intégrité du code par le biais de l’attestation, peut être utile pour votre stratégie de sécurité de conteneur de défense en profondeur. Les nœuds d’informatique confidentielle prennent en charge à la fois les conteneurs confidentiels (applications Docker existantes) et les conteneurs prenant en charge les enclaves.

Pour plus d’informations, consultez [Nœuds d’informatique confidentielle sur AKS][conf-com-node].

### <a name="storage-volume-support"></a>Prise en charge du volume de stockage

Pour les charges de travail d’application, vous pouvez monter des volumes de stockage statiques ou dynamiques pour les données persistantes. Selon le nombre de pods connectés appelés à partager les volumes de stockage, vous pouvez utiliser un stockage adossé aux ressources suivantes :
* disques Azure pour l’accès à un seul pod ; 
* Azure Files pour l’accès simultané à plusieurs pods.

Pour plus d’informations, consultez [Options de stockage pour les applications dans AKS][concepts-storage].

Démarrez avec les volumes persistants dynamiques en utilisant [Disques Azure][azure-disk] ou [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Réseaux virtuels et entrée

Un cluster AKS peut être déployé dans un réseau virtuel existant. Dans cette configuration, chaque pod du cluster se voit attribuer une adresse IP dans le réseau virtuel et peut communiquer directement avec les ressources suivantes :
* autres pods dans le cluster ; 
* autres nœuds dans le réseau virtuel. 

Les pods peuvent aussi se connecter à d’autres services dans un réseau virtuel appairé et à des réseaux locaux via ExpressRoute ou des connexions VPN site à site (S2S).  

Pour plus d’informations, consultez les [Concepts réseau pour les applications dans AKS][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Entrée avec un routage d’applications HTTP

Le module complémentaire de routage d’application HTTP vous permet d’accéder facilement aux applications déployées sur votre cluster AKS. Lorsqu’elle est activée, la solution de routage d’application HTTP configure un contrôleur d’entrée de votre cluster AKS.  

Des noms DNS accessibles publiquement sont configurés automatiquement à mesure que des applications sont déployées. Le routage d’applications HTTP configure une zone DNS et l’intègre au cluster AKS. Vous pouvez ensuite déployer des ressources d’entrée Kubernetes comme d’habitude.  

Pour vous familiariser avec le trafic d’entrée, consultez [Routage d’applications HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Intégration d’outils de développement

Kubernetes dispose d’un riche écosystème d’outils de développement et de gestion qui fonctionnent de façon harmonieuse avec AKS. Parmi ces outils figurent Helm et l’extension Kubernetes pour Visual Studio Code. 

Azure propose plusieurs outils qui permettent de rationaliser Kubernetes, comme DevOps Starter.  

### <a name="devops-starter"></a>DevOps Starter

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
> [Déployer un cluster AKS à l’aide d’Azure CLI][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://azure.microsoft.com/en-us/overview/trusted-cloud/compliance/

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
[aks-master-logs]: ./view-control-plane-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
