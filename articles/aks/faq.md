---
title: Forum aux questions sur Azure Kubernetes Service
description: Apporte des réponses à certaines des questions les plus fréquemment posées sur Azure Kubernetes Service.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/08/2018
ms.author: nepeters
ms.openlocfilehash: 79236ae7134a27b9a5b89ee8151803befa7b51e1
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260790"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Forum aux questions sur Azure Kubernetes Service (AKS)

Cet article aborde les questions fréquemment posées sur Azure Kubernetes Service (AKS).

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) est actuellement en **préversion**. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.
>

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Quelles régions Azure bénéficient d’Azure Kubernetes Service (AKS) aujourd’hui ?

- Centre du Canada
- Est du Canada
- Centre des États-Unis
- Est des États-Unis
- Europe de l'Ouest

## <a name="when-will-additional-regions-be-added"></a>Quand d’autres régions seront-elles ajoutées ?

D’autres régions sont ajoutées au fur et à mesure de l’augmentation de la demande.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Des mises à jour sécurisées sont-elles appliquées aux nœuds de l’agent AKS ?

Azure applique automatiquement les correctifs de sécurité pour les nœuds de votre cluster selon une planification nocturne. Toutefois, vous êtes chargé de vous assurer que les nœuds sont redémarrés selon les besoins. Vous avez plusieurs options pour effectuer des redémarrages de nœud :

- Manuellement, via le portail Azure ou l’interface de ligne de commande Azure.
- En mettant à niveau votre cluster AKS. Le cluster met automatiquement à niveau des [nœuds drain et cordon](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), puis les sauvegarde à nouveau avec la dernière image Ubuntu. Mettez à jour l’image du système d’exploitation sur vos nœuds sans modifier les versions Kubernetes en spécifiant la version actuelle du cluster dans `az aks upgrade`.
- Utilisation de [Kured](https://github.com/weaveworks/kured), un démon de redémarrage Open Source pour Kubernetes. Kured s’exécute comme un [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) et analyse chaque nœud à la recherche d’un fichier indiquant qu’un redémarrage est nécessaire. Ensuite, il orchestre ces redémarrages au sein du cluster, en suivant le même processus drain et cordon décrit plus haut.

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>Recommandez-vous aux clients d’utiliser ACS ou AKS ?

Tandis qu’AKS reste dans l’aperçu, nous vous recommandons de créer des clusters de production à l’aide des services ACS-Kubernetes ou [acs-engine](https://github.com/azure/acs-engine). Utilisez AKS pour des déploiements de preuve de concept et des environnements de développement/test.

## <a name="when-will-acs-be-deprecated"></a>Quand le service ACS sera-t-il déconseillé ?

ACS sera déconseillé à peu près au moment de la publication de la version finale d’AKS. Vous aurez 12 mois après cette date pour migrer vos clusters sur AKS. Pendant cette période, vous pourrez exécuter toutes les opérations ACS.

## <a name="does-aks-support-node-autoscaling"></a>AKS prend-il en charge la mise à l’échelle automatique des nœuds ?

La mise à l’échelle automatique des nœuds n’est pas prise en charge, mais elle est intégrée à la feuille de route. Nous vous proposons d’aller voir du côté de cette [implémentation de la mise à l’échelle automatique][auto-scaler].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS prend-il en charge le contrôle d’accès par rôle Kubernetes (RBAC) ?

Non, RBAC n’est actuellement pas pris en charge dans AKS mais sera bientôt disponible.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Puis-je déployer AKS dans mon réseau virtuel existant ?

Oui, cette opération est prise en charge via la [fonctionnalité de mise en réseau avancée](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault est-il intégré à AKS ?

Non, mais cette intégration est planifiée. En attendant, essayez la solution [Hexadite][hexadite] ci-après.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Puis-je exécuter des conteneurs Windows Server sur AKS ?

Pour exécuter des conteneurs Windows Server, vous devez exécuter des nœuds Windows Server. Les nœuds Windows Server sont actuellement en [préversion privée](https://azure.microsoft.com/en-us/blog/kubernetes-on-azure/). Si vous avez besoin d’exécuter des conteneurs Windows Server sur Kubernetes dans Azure en dehors de la préversion, consultez la [documentation sur acs-engine](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Pourquoi deux groupes de ressources sont-ils créés avec AKS ?

Chaque déploiement AKS s’étend sur deux groupes de ressources. Le premier, créé par vous, contient uniquement la ressource Azure Container Service. Le fournisseur de ressources AKS crée automatiquement le second au cours du déploiement avec un nom tel que *MC_myResourceGroup_myAKSCluster_eastus*. Le second groupe de ressources contient toutes les ressources d’infrastructure associées au cluster, tels que des machines virtuelles, la mise en réseau et le stockage. Il est créé pour simplifier le nettoyage des ressources.

Si vous créez des ressources qui seront utilisées avec votre cluster AKS, tels que les comptes de stockage ou l’adresse IP publique réservée, vous devez les placer dans le groupe de ressources généré automatiquement.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS offre-t-il un contrat de niveau de service ?

Dans un contrat de niveau de service (SLA), le fournisseur accepte de rembourser le client si le niveau du service publié n’est pas rempli. AKS étant gratuit, il n’y a aucuns frais à rembourser et par conséquent aucun contrat SLA formel. Toutefois, nous cherchons à maintenir une disponibilité d’au moins 99,5 % pour le serveur d’API Kubernetes.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
