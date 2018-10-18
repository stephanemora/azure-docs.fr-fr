---
title: Forum aux questions sur Azure Kubernetes Service (AKS)
description: Apporte des réponses à certaines des questions les plus fréquemment posées sur Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.openlocfilehash: 1e101e308ec350e9900c1347da730ca02b16c7bb
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377462"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Forum aux questions sur Azure Kubernetes Service (AKS)

Cet article aborde les questions fréquemment posées sur Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Quelles régions Azure bénéficient d’Azure Kubernetes Service (AKS) aujourd’hui ?

Pour obtenir la liste complète des régions disponibles, consultez [AKS Regions and availability][aks-regions] (Régions AKS et disponibilité).

## <a name="does-aks-support-node-autoscaling"></a>AKS prend-il en charge la mise à l’échelle automatique des nœuds ?

Oui, la mise à l'échelle automatique est disponible via [l’outil de mise à l'échelle automatique de Kubernetes][auto-scaler] dans Kubernetes 1.10 et versions ultérieures. Pour plus d’informations sur la façon de configurer et d’utiliser l’autoscaler de cluster, consultez [Cluster autoscale on AKS][aks-cluster-autoscale] (Mise à l’échelle automatique de cluster sur AKS).

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS prend-il en charge le contrôle d’accès par rôle Kubernetes (RBAC) ?

Oui, le contrôle d’accès en fonction du rôle (RBAC) Kubernetes est activé par défaut lors de la création de clusters avec Azure CLI. Le contrôle d’accès en fonction du rôle (RBAC) peut être activé pour les clusters créés à l’aide de modèles ou du Portail Azure.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Puis-je déployer AKS dans mon réseau virtuel existant ?

Oui, vous pouvez déployer un cluster AKS dans un réseau virtuel existant à l’aide de la [fonctionnalité de mise en réseau avancée][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Puis-je restreindre le serveur d’API Kubernetes pour le rendre accessible seulement dans mon réseau virtuel ?

Pas pour l'instant. Le serveur d’API Kubernetes est exposé en tant que nom de domaine complet (FQDN) public. Vous pouvez contrôler l’accès à votre cluster avec le [contrôle d’accès en fonction du rôle (RBAC) Kubernetes et Azure Active Directory (AAD)][aks-rbac-aad].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Des mises à jour sécurisées sont-elles appliquées aux nœuds de l’agent AKS ?

Oui. Azure applique automatiquement les correctifs de sécurité pour les nœuds de votre cluster selon une planification nocturne. Toutefois, vous êtes chargé de vous assurer que les nœuds sont redémarrés selon les besoins. Vous avez plusieurs options pour effectuer des redémarrages de nœud :

- Manuellement, via le portail Azure ou l’interface de ligne de commande Azure.
- En mettant à niveau votre cluster AKS. Le cluster met automatiquement à niveau des [nœuds drain et cordon][cordon-drain], puis les sauvegarde à nouveau avec la dernière image Ubuntu et une nouvelle version de correctif ou une version mineure de Kubernetes. Pour plus d’informations, consultez [Mettre à niveau un cluster AKS][aks-upgrade].
- Utilisation de [Kured](https://github.com/weaveworks/kured), un démon de redémarrage Open Source pour Kubernetes. Kured s’exécute comme un [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) et analyse chaque nœud à la recherche d’un fichier indiquant qu’un redémarrage est nécessaire. Les redémarrages du système d’exploitation sont gérés au sein du cluster à l’aide du même [processus drain et cordon][cordon-drain] que celui appliqué pour la mise à niveau de cluster.

## <a name="why-are-two-resource-groups-created-with-aks"></a>Pourquoi deux groupes de ressources sont-ils créés avec AKS ?

Chaque déploiement AKS s’étend sur deux groupes de ressources :

- Le premier, créé par vous, contient uniquement la ressource du service Kubernetes. Le fournisseur de ressources AKS crée automatiquement le second au cours du déploiement avec un nom tel que *MC_myResourceGroup_myAKSCluster_eastus*.
- Le second groupe de ressources (*MC_myResourceGroup_myAKSCluster_eastus*, par exemple) contient toutes les ressources d’infrastructure associées au cluster. Ces ressources incluent les machines virtuelles de nœud Kubernetes, la mise en réseau et le stockage. Ce groupe de ressources distinct est créé pour simplifier le nettoyage des ressources.

Si vous créez des ressources qui seront utilisées avec votre cluster AKS, tels que les comptes de stockage ou les adresses IP publiques réservées, vous devez les placer dans le groupe de ressources généré automatiquement.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Puis-je modifier les balises et d’autres propriétés des ressources AKS dans le groupe de ressources MC_* ?

La modification et la suppression de balises créées par Azure et d’autres propriétés de ressources dans le groupe de ressources *MC_** peuvent entraîner des résultats inattendus, notamment des erreurs de mise à l’échelle et de mise à niveau. La création et la modification de balises personnalisées supplémentaires, par exemple, pour affecter une unité commerciale ou un centre de coûts, sont prises en charge. La modification des ressources sous *MC_** dans le cluster AKS interrompt le SLO.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quels sont les contrôleurs d’admission Kubernetes qui sont pris en charge par AKS ? Les contrôleurs d’admission peuvent-ils être ajoutés ou supprimés ?

AKS prend en charge les [contrôleurs d’admission][admission-controllers] suivants :

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Il n’est pas encore possible de modifier la liste des contrôleurs d’admission dans AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault est-il intégré à AKS ?

Pour l’instant, AKS n’est pas intégré nativement à Azure Key Vault. Toutefois, le [projet FlexVolume d’Azure Key Vault pour Kubernetes][keyvault-flexvolume] permet une intégration directe des pods Kubernetes aux secrets KeyVault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Puis-je exécuter des conteneurs Windows Server sur AKS ?

Pour exécuter des conteneurs Windows Server, vous devez exécuter des nœuds Windows Server. Pour l’instant, les nœuds Windows Server ne sont pas disponibles dans AKS. Toutefois, vous pouvez utiliser Virtual Kubelet pour planifier les conteneurs Windows dans Azure Container Instances, et les gérer dans le cadre de votre cluster AKS. Pour plus d’informations, consultez [Utiliser Virtual Kubelet avec AKS][virtual-kubelet].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS offre-t-il un contrat de niveau de service ?

Dans un contrat de niveau de service (SLA), le fournisseur accepte de rembourser le client si le niveau du service publié n’est pas rempli. AKS étant gratuit, il n’y a aucuns frais à rembourser et par conséquent aucun contrat SLA formel. Toutefois, AKS cherche à maintenir une disponibilité d’au moins 99,5 % pour le serveur d’API Kubernetes.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-advanced-networking.md
[aks-rbac-aad]: ./aad-integration.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
