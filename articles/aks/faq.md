---
title: Forum aux questions sur Azure Kubernetes Service (AKS)
description: Trouvez des réponses à certaines questions courantes sur Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.openlocfilehash: 1cc03cbcffc5253e8b357b6702cd21c45740ff81
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514486"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Forum aux questions sur Azure Kubernetes Service (AKS)

Cet article aborde les questions fréquemment posées sur Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Quelles régions Azure bénéficient actuellement AKS ?

Pour obtenir une liste complète des régions disponibles, consultez [AKS régions et disponibilité][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>AKS prend-il en charge la mise à l’échelle automatique des nœuds ?

Oui, à l’échelle automatique est disponible via le [Kubernetes autoscaler] [ auto-scaler] depuis 1.10 de Kubernetes. Pour plus d’informations sur comment configurer et utiliser le cluster autoscaler manuellement, consultez [mise à l’échelle de Cluster sur AKS][aks-cluster-autoscale].

Vous pouvez également utiliser le cluster intégrés autoscaler (actuellement en version préliminaire dans ACS) pour gérer la mise à l’échelle de nœuds. Pour plus d’informations, consultez [automatiquement à l’échelle un cluster pour répondre aux demandes d’application dans AKS][aks-cluster-autoscaler].

## <a name="does-aks-support-kubernetes-rbac"></a>AKS prend-il en charge Kubernetes RBAC ?

Oui, le contrôle d’accès en fonction du rôle Kubernetes (RBAC) est activé par défaut lors de la création de clusters avec Azure CLI. Vous pouvez activer le RBAC pour les clusters qui ont été créés à l’aide du portail Azure ou les modèles.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Puis-je déployer AKS dans mon réseau virtuel existant ?

Oui, vous pouvez déployer un cluster AKS dans un réseau virtuel existant à l’aide de la [fonctionnalité de mise en réseau avancée][aks-advanced-networking].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Faire le serveur d’API Kubernetes accessible uniquement au sein de mon réseau virtuel ?

Pas pour l'instant. Le serveur d’API Kubernetes est exposé en tant que nom de domaine complet (FQDN) public. Vous pouvez contrôler l’accès à votre cluster à l’aide de [Kubernetes RBAC et Azure Active Directory (Azure AD)][aks-rbac-aad].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Des mises à jour sécurisées sont-elles appliquées aux nœuds de l’agent AKS ?

Azure applique automatiquement les correctifs de sécurité pour les nœuds Linux dans votre cluster selon une planification nocturne. Toutefois, vous êtes chargé de s’assurer que ces nœuds sont redémarrés en tant que de Linux requis. Vous avez plusieurs options pour le redémarrage des nœuds :

- Manuellement, via le portail Azure ou l’interface de ligne de commande Azure.
- En mettant à niveau votre cluster AKS. Les mises à niveau de cluster [nœuds DRAIN et] [ cordon-drain] automatiquement, puis remettre en ligne un nouveau nœud avec la dernière image Ubuntu et une nouvelle version de correctif ou une version mineure de Kubernetes. Pour plus d’informations, consultez [Mettre à niveau un cluster AKS][aks-upgrade].
- À l’aide de [Kured](https://github.com/weaveworks/kured), un démon de redémarrage open source pour Kubernetes. Kured s’exécute comme un [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) et analyse chaque nœud à la présence d’un fichier qui indique qu’un redémarrage est nécessaire. Au sein du cluster, les redémarrages du système d’exploitation sont gérés par le même [DRAIN et processus] [ cordon-drain] comme une mise à niveau de cluster.

Pour plus d’informations sur l’utilisation de kured, consultez [Appliquer les mises à jour de sécurité et de noyau aux nœuds dans AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Nœuds Windows Server

Pour les nœuds de Windows Server (actuellement en version préliminaire dans ACS), mise à jour de Windows n’exécute pas automatiquement et appliquer les dernières mises à jour. Selon une planification régulière autour de cycle de mise à jour de Windows et votre propre processus de validation, vous devez effectuer une mise à niveau sur l’ou les pools nœud Windows Server dans votre cluster AKS. Ce processus de mise à niveau crée des nœuds qui exécutent la dernière image Windows Server et les correctifs, puis supprime les nœuds plus anciens. Pour plus d’informations sur ce processus, consultez [mise à niveau d’un pool de nœuds dans ACS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Pourquoi deux groupes de ressources sont-ils créés avec AKS ?

Chaque déploiement AKS s’étend sur deux groupes de ressources :

1. Vous créez le premier groupe de ressources. Ce groupe contient uniquement la ressource du service Kubernetes. Le fournisseur de ressources AKS crée automatiquement le deuxième groupe de ressources pendant le déploiement. Un exemple du deuxième groupe de ressources est *MC_myResourceGroup_myAKSCluster_eastus*. Pour plus d’informations sur la façon de spécifier le nom de ce deuxième groupe de ressources, consultez la section suivante.
1. Le deuxième groupe de ressources, telles que *MC_myResourceGroup_myAKSCluster_eastus*, contient toutes les ressources d’infrastructure associés au cluster. Ces ressources incluent les machines virtuelles de nœud Kubernetes, la mise en réseau et le stockage. L’objectif de ce groupe de ressources consiste à simplifier le nettoyage des ressources.

Si vous créez des ressources à utiliser avec votre cluster AKS, tels que les comptes de stockage ou des adresses IP publiques réservées, placez-les dans le groupe de ressources généré automatiquement.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>Puis-je fournir ma propre nom pour le groupe de ressources d’infrastructure AKS ?

Oui. Par défaut, le fournisseur de ressources AKS crée automatiquement un groupe de ressources secondaire (tel que *MC_myResourceGroup_myAKSCluster_eastus*) au cours du déploiement. Pour respecter la stratégie d’entreprise, vous pouvez fournir votre propre nom pour ce cluster géré (*MC_* ) groupe de ressources.

Pour spécifier votre propre nom de groupe de ressources, vous devez installer le [aks-preview] [ aks-preview-cli] version de l’extension Azure CLI *0.3.2* ou version ultérieure. Lorsque vous créez un cluster AKS à l’aide de la [créer az aks] [ az-aks-create] commande, utilisez le *--groupe de ressources de nœud* paramètre et spécifiez un nom pour le groupe de ressources. Si vous [utiliser un modèle Azure Resource Manager] [ aks-rm-template] pour déployer un cluster AKS, vous pouvez définir le nom de groupe de ressources à l’aide de la *nodeResourceGroup* propriété.

* Le groupe de ressources secondaire est automatiquement créé par le fournisseur de ressources Azure dans votre propre abonnement.
* Vous pouvez spécifier un nom de groupe de ressources personnalisées uniquement lorsque vous créez le cluster.

Lorsque vous travaillez avec le *MC_* groupe de ressources, n’oubliez pas que vous ne pouvez pas :

* Spécifiez un groupe de ressources existant pour le *MC_* groupe.
* Spécifiez un autre abonnement pour le *MC_* groupe de ressources.
* Modifier le *MC_* nom du groupe de ressources une fois que le cluster a été créé.
* Spécifier des noms pour les ressources managées dans le *MC_* groupe de ressources.
* Modifier ou supprimer des balises de ressources gérées dans le *MC_* groupe de ressources. (Consultez des informations supplémentaires dans la section suivante.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Puis-je modifier les balises et autres propriétés des ressources dans le groupe de ressources MC_ AKS ?

Si vous modifiez ou supprimer des balises créé par Azure et autres propriétés de ressource dans le *MC_* groupe de ressources, vous pouvez obtenir des résultats inattendus, tels que la mise à l’échelle et la mise à niveau des erreurs. AKS permet de créer et modifier des balises personnalisées. Vous souhaiterez peut-être créer ou modifier des balises personnalisées, par exemple, pour affecter un business unit ou centre de coûts. En modifiant les ressources sous le *MC_* dans le cluster AKS, vous rompez l’objectif de niveau de service (SLO). Pour plus d’informations, consultez [AKS est offrent un contrat de niveau de service ?](#does-aks-offer-a-service-level-agreement)

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

Actuellement, vous ne pouvez pas modifier la liste des contrôleurs d’admission dans ACS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault est-il intégré à AKS ?

ACS n’est pas actuellement en mode natif intégrée à Azure Key Vault. Toutefois, le [FlexVolume de coffre de clé Azure pour le projet de Kubernetes] [ keyvault-flexvolume] permet de diriger l’intégration de pods Kubernetes aux secrets du coffre de clés.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Puis-je exécuter des conteneurs Windows Server sur AKS ?

Oui, les conteneurs Windows Server sont disponibles en version préliminaire. Pour exécuter des conteneurs Windows Server dans ACS, vous créez un pool de nœud qui exécute Windows Server en tant que le système d’exploitation invité. Conteneurs Windows Server peuvent utiliser uniquement les 2019 Windows Server. Pour commencer, consultez [créer un cluster AKS avec un pool de nœud Windows Server][aks-windows-cli].

Prise en charge du serveur de fenêtre de nœuds du pool inclut certaines limitations qui font partie de Windows Server en amont dans le projet de Kubernetes. Pour plus d’informations sur ces limitations, consultez [conteneurs Windows Server dans les limites de AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>ACS offre un contrat de niveau de service ?

Dans un contrat de niveau de service (SLA), le fournisseur s’engage à rembourser le client pour le coût du service si le niveau de service publié n’est pas remplie. Étant donné que AKS est gratuit, sans coût est disponible à rembourser, ACS n’a aucun contrat SLA formel. Toutefois, AKS cherche à maintenir la disponibilité d’au moins 99,5 % pour le serveur d’API Kubernetes.

## <a name="why-cant-i-set-maxpods-below-30"></a>Pourquoi ne puis-je pas définir maxPods ci-dessous 30 ?

Dans ACS, vous pouvez définir le `maxPods` valeur lorsque vous créez le cluster en utilisant les modèles Azure CLI et Azure Resource Manager. Toutefois, Kubenet et Azure CNI requièrent un *valeur minimale* (validée au moment de la création) :

| Mise en réseau | Minimale | Maximale |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Car ACS est un service géré, nous déployer et gérer les modules complémentaires et les blocs en tant que partie du cluster. Dans le passé, les utilisateurs peuvent définir un `maxPods` valeur inférieure à la valeur les pods managés nécessaire à l’exécution (par exemple, 30). AKS calcule maintenant le nombre minimal de pods à l’aide de cette formule : ((maxPods ou (maxPods * vm_count)) > minimum de pods complémentaire.

Les utilisateurs ne peuvent pas remplacer la valeur minimale `maxPods` validation.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Puis-je appliquer des remises de réservation Azure sur mon nœuds d’agent AKS ?

Les nœuds de l’agent AKS sont facturés en tant que machines virtuelles Azure standards, par conséquent si vous avez acheté [réservations Azure] [ reservation-discounts] pour la taille de machine virtuelle que vous utilisez dans ACS, ces remises sont automatiquement appliquées.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /rest/api/aks/managedclusters/createorupdate#managedcluster
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
