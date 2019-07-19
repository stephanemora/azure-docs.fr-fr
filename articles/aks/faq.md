---
title: Forum aux questions sur Azure Kubernetes Service (AKS)
description: Recherchez des réponses à certaines des questions les plus fréquemment posées sur Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/03/2019
ms.author: iainfou
ms.openlocfilehash: d4fa365e1ed055fa8ddeb8fd475e152af84a3b71
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560442"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Forum aux questions sur Azure Kubernetes Service (AKS)

Cet article aborde les questions fréquemment posées sur Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Quelles régions Azure proposent actuellement AKS ?

Pour obtenir la liste complète des régions disponibles, consultez [Régions AKS et disponibilité][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>AKS prend-il en charge la mise à l’échelle automatique des nœuds ?

Oui, la mise à l’échelle horizontale automatique des nœuds d’agent dans AKS est actuellement disponible en préversion. Consultez [Mettre automatiquement à l'échelle un cluster pour répondre aux demandes applicatives dans AKS][aks-cluster-autoscaler] for instructions. AKS autoscaling is based on the [Kubernetes autoscaler][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Puis-je déployer AKS dans mon réseau virtuel existant ?

Oui, vous pouvez déployer un cluster AKS dans un réseau virtuel existant en utilisant la [fonctionnalité de mise en réseau avancée][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Puis-je limiter qui a accès au serveur d’API Kubernetes ?

Oui, vous pouvez limiter l’accès au serveur d’API Kubernetes en utilisant les [plages d’adresses IP autorisées du serveur d’API][api-server-authorized-ip-ranges], qui sont actuellement en préversion.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Puis-je rendre le serveur d’API Kubernetes accessible uniquement dans mon réseau virtuel ?

Pas pour l’instant, mais cela est prévu. Vous pouvez suivre l’avancement de cette option dans le [dépôt GitHub AKS][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Puis-je avoir des machines virtuelles de différentes tailles dans un même cluster ?

Oui, vous pouvez utiliser différentes tailles de machines virtuelles dans votre cluster AKS en créant [plusieurs pools de nœuds][multi-node-pools], ce qui est actuellement en préversion.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Des mises à jour sécurisées sont-elles appliquées aux nœuds de l’agent AKS ?

Azure applique automatiquement les correctifs de sécurité pour les nœuds Linux de votre cluster selon une planification nocturne. Toutefois, vous êtes chargé de vous assurer que ces nœuds Linux sont redémarrés selon les besoins. Vous avez plusieurs options pour redémarrer les nœuds :

- Manuellement, via le portail Azure ou l’interface de ligne de commande Azure.
- En mettant à niveau votre cluster AKS. Le cluster met à niveau les [nœuds d’isolation et de drainage][cordon-drain] automatically and then bring a new node online with the latest Ubuntu image and a new patch version or a minor Kubernetes version. For more information, see [Upgrade an AKS cluster][aks-upgrade].
- En utilisant [Kured](https://github.com/weaveworks/kured), un démon de redémarrage open source pour Kubernetes. Kured s’exécute en tant que [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) et analyse chaque nœud à la recherche d’un fichier indiquant qu’un redémarrage est nécessaire. Au sein du cluster, les redémarrages du système d’exploitation sont gérés par le même [processus d’isolation et de drainage][cordon-drain] que celui appliqué pour une mise à niveau de cluster.

Pour plus d’informations sur l’utilisation de kured, consultez [Appliquer les mises à jour de sécurité et de noyau aux nœuds dans AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Nœuds Windows Server

Pour les nœuds Windows Server (actuellement en préversion dans AKS), Windows Update ne s’exécute pas et n’applique pas automatiquement les dernières mises à jour. Suivez une planification régulière basée sur le cycle de mise à jour de Windows et votre propre processus de validation pour effectuer une mise à niveau sur le ou les pools de nœuds Windows Server dans votre cluster AKS. Ce processus de mise à niveau crée des nœuds qui exécutent la dernière image et les derniers correctifs de Windows Server, puis supprime les anciens nœuds. Pour plus d’informations sur ce processus, consultez [Mettre à niveau un pool de nœuds dans AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Pourquoi deux groupes de ressources sont-ils créés avec AKS ?

Chaque déploiement AKS s’étend sur deux groupes de ressources :

1. Vous créez le premier groupe de ressources. Ce groupe contient uniquement la ressource de service Kubernetes. Le fournisseur de ressources AKS crée automatiquement le second groupe de ressources au cours du déploiement. Un exemple du second groupe de ressources est *MC_myResourceGroup_myAKSCluster_eastus*. Pour obtenir des informations sur la façon de spécifier le nom de ce second groupe de ressources, consultez la section suivante.
1. Le second groupe de ressources (*MC_myResourceGroup_myAKSCluster_eastus*, par exemple) contient toutes les ressources d’infrastructure associées au cluster. Ces ressources incluent les machines virtuelles de nœud Kubernetes, la mise en réseau et le stockage. L’objectif de ce groupe de ressources est de simplifier le nettoyage des ressources.

Si vous créez les ressources à utiliser avec votre cluster AKS, telles que les comptes de stockage ou les adresses IP publiques réservées, placez-les dans le groupe de ressources généré automatiquement.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>Puis-je fournir mon propre nom pour le groupe de ressources d’infrastructure AKS ?

Oui. Par défaut, le fournisseur de ressources AKS crée automatiquement un groupe de ressources secondaire (tel que *MC_myResourceGroup_myAKSCluster_eastus*) au cours du déploiement. Pour respecter la stratégie d’entreprise, vous pouvez fournir votre propre nom pour le groupe de ressources de ce cluster géré (*MC_* ).

Pour spécifier votre propre nom de groupe de ressources, installez la version *0.3.2* ou une version ultérieure de l’extension Azure CLI [aks-preview][aks-preview-cli]. Lorsque vous créez un cluster AKS à l’aide de la commande [az aks create][az-aks-create], utilisez le paramètre *--node-resource-group* et spécifiez un nom pour le groupe de ressources. Si vous [utilisez un modèle Azure Resource Manager][aks-rm-template] pour déployer un cluster AKS, vous pouvez définir le nom du groupe de ressources à l’aide de la propriété *nodeResourceGroup*.

* Le groupe de ressources secondaire est automatiquement créé par le fournisseur de ressources Azure dans votre propre abonnement.
* Vous pouvez spécifier un nom de groupe de ressources personnalisé uniquement lorsque vous créez le cluster.

Lorsque vous travaillez avec le groupe de ressources *MC_* , n’oubliez pas que vous ne pouvez pas :

* Spécifier un groupe de ressources existant pour le groupe *MC_* .
* Spécifier un autre abonnement pour le groupe de ressources *MC_* .
* Modifier le nom du groupe de ressources *MC_* une fois que le cluster a été créé.
* Spécifier des noms pour les ressources managées au sein du groupe de ressources *MC_* .
* Modifier ou supprimer les balises des ressources managées au sein du groupe de ressources *MC_* . (Consultez des informations supplémentaires dans la section suivante.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Puis-je modifier les balises et d’autres propriétés des ressources AKS dans le groupe de ressources MC_ ?

Si vous modifiez ou supprimez des balises créées par Azure et d’autres propriétés de ressources dans le groupe de ressources *MC_* , vous pouvez obtenir des résultats inattendus tels que des erreurs de mise à l’échelle et de mise à niveau. AKS vous permet de créer et modifier des balises personnalisées. Vous souhaiterez peut-être créer ou modifier des balises personnalisées, par exemple, pour affecter une unité commerciale ou un centre de coûts. En modifiant les ressources du groupe *MC_* dans le cluster AKS, vous empêchez d'atteindre l'objectif de niveau de service (SLO). Pour plus d'informations, consultez [AKS offre-t-il un contrat de niveau de service ?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quels sont les contrôleurs d’admission Kubernetes qui sont pris en charge par AKS ? Les contrôleurs d’admission peuvent-ils être ajoutés ou supprimés ?

AKS prend en charge les [contrôleurs d’admission][admission-controllers] suivants :

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

Actuellement, vous ne pouvez pas modifier la liste des contrôleurs d’admission dans AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault est-il intégré à AKS ?

Pour l’instant, AKS n’est pas intégré nativement à Azure Key Vault. Toutefois, le [projet FlexVolume d’Azure Key Vault pour Kubernetes][keyvault-flexvolume] permet une intégration directe des pods Kubernetes aux secrets Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Puis-je exécuter des conteneurs Windows Server sur AKS ?

Oui, les conteneurs Windows Server sont disponibles en préversion. Pour exécuter des conteneurs Windows Server dans AKS, vous créez un pool de nœuds qui exécute Windows Server en tant que système d’exploitation invité. Les conteneurs Windows Server peuvent utiliser uniquement Windows Server 2019. Pour commencer, consultez [Créer un cluster AKS avec un pool de nœuds Windows Server][aks-windows-cli].

La prise en charge des pools de nœuds dans Windows Server comprend certaines limitations qui font partie du projet amont Windows Server dans Kubernetes. Pour plus d’informations sur ces limitations, consultez [Limitations des conteneurs Windows Server dans AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS offre-t-il un contrat de niveau de service ?

Dans un contrat de niveau de service (SLA), le fournisseur accepte de rembourser le coût du service au client si le niveau de service publié n’est pas respecté. Comme AKS est gratuit, aucun coût ne peut être remboursé, de sorte qu’AKS n’a aucun contrat SLA formel. Toutefois, AKS cherche à maintenir une disponibilité d’au moins 99,5 % pour le serveur d’API Kubernetes.

## <a name="why-cant-i-set-maxpods-below-30"></a>Pourquoi ne puis-je pas définir une valeur maxPods inférieure à 30 ?

Dans AKS, vous pouvez définir la valeur `maxPods` lorsque vous créez le cluster en utilisant les modèles Azure CLI et Azure Resource Manager. Toutefois, Kubenet et Azure CNI requièrent une *valeur minimale* (validée au moment de la création) :

| Mise en réseau | Minimale | Maximale |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Comme AKS est un service géré, nous déployons et gérons les modules complémentaires et les pods dans le cadre du cluster. Dans le passé, les utilisateurs pouvaient définir une valeur `maxPods` inférieure à la valeur que les pods managés nécessitaient pour s’exécuter (par exemple, 30). AKS calcule désormais le nombre minimal de pods à l’aide de cette formule : ((maxPods ou (maxPods * vm_count)) > minimum de pods de modules complémentaires managés.

Les utilisateurs ne peuvent pas remplacer la validation de la valeur minimale `maxPods`.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Puis-je appliquer des remises de réservation Azure sur mes nœuds d’agent AKS ?

Les nœuds d’agent AKS sont facturés en tant que machines virtuelles Azure standard. Par conséquent, si vous avez acheté des [réservations Azure][reservation-discounts] pour la taille de machine virtuelle que vous utilisez dans AKS, ces remises sont automatiquement appliquées.

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
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948