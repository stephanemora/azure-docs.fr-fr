---
title: Forum aux questions sur Azure Kubernetes Service
description: Apporte des réponses à certaines des questions les plus fréquemment posées sur Azure Kubernetes Service.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/25/2018
ms.author: nepeters
ms.openlocfilehash: 5155d0c85e5b3698b0a13d2d5256a235858f0e82
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939830"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Forum aux questions sur Azure Kubernetes Service (AKS)

Cet article aborde les questions fréquemment posées sur Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Quelles régions Azure bénéficient d’Azure Kubernetes Service (AKS) aujourd’hui ?

- Est de l’Australie
- Centre du Canada
- Est du Canada
- Centre des États-Unis
- Est des États-Unis
- Est des États-Unis 2
- Europe du Nord
- Sud du Royaume-Uni
- Europe de l'Ouest
- États-Unis de l’Ouest
- Ouest des États-Unis 2

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Des mises à jour sécurisées sont-elles appliquées aux nœuds de l’agent AKS ?

Azure applique automatiquement les correctifs de sécurité pour les nœuds de votre cluster selon une planification nocturne. Toutefois, vous êtes chargé de vous assurer que les nœuds sont redémarrés selon les besoins. Vous avez plusieurs options pour effectuer des redémarrages de nœud :

- Manuellement, via le portail Azure ou l’interface de ligne de commande Azure.
- En mettant à niveau votre cluster AKS. Le cluster met automatiquement à niveau des [nœuds drain et cordon](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), puis les sauvegarde à nouveau avec la dernière image Ubuntu. Mettez à jour l’image du système d’exploitation sur vos nœuds sans modifier les versions Kubernetes en spécifiant la version actuelle du cluster dans `az aks upgrade`.
- Utilisation de [Kured](https://github.com/weaveworks/kured), un démon de redémarrage Open Source pour Kubernetes. Kured s’exécute comme un [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) et analyse chaque nœud à la recherche d’un fichier indiquant qu’un redémarrage est nécessaire. Ensuite, il orchestre les redémarrages au sein du cluster, en suivant le même processus drain et cordon décrit plus haut.

## <a name="does-aks-support-node-autoscaling"></a>AKS prend-il en charge la mise à l’échelle automatique des nœuds ?

Oui, la mise à l'échelle automatique est disponible via [l’outil de mise à l'échelle automatique de Kubernetes][auto-scaler] dans Kubernetes 1.10 et versions ultérieures.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS prend-il en charge le contrôle d’accès par rôle Kubernetes (RBAC) ?

Oui, le contrôle d’accès en fonction du rôle (RBAC) peut être activé lors du déploiement d’un cluster AKS à partir du modèle Azure CLI ou Azure Resource Manager. Cette fonctionnalité sera bientôt disponible dans le portail Azure.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Quels sont les contrôleurs d’admission Kubernetes qui sont pris en charge par AKS ? Est-ce configurable ?

AKS prend en charge les [contrôleurs d’admission][admission-controllers] suivants :

* NamespaceLifecycle
* LimitRanger
* ServiceAccount
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook 
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

Il n’est pas encore possible de modifier la liste des contrôleurs d’admission dans AKS.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Puis-je déployer AKS dans mon réseau virtuel existant ?

Oui, vous pouvez déployer un cluster AKS dans un réseau virtuel existant à l’aide de la [fonctionnalité de mise en réseau avancée](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault est-il intégré à AKS ?

Pour l’instant, AKS n’est pas intégré nativement à Azure Key Vault. Toutefois, il existe des solutions de la communauté, telles que [the acs-keyvault-agent d’Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Puis-je exécuter des conteneurs Windows Server sur AKS ?

Pour exécuter des conteneurs Windows Server, vous devez exécuter des nœuds Windows Server. Pour l’instant, les nœuds Windows Server ne sont pas disponibles dans AKS. Si vous avez besoin d’exécuter des conteneurs Windows Server sur Kubernetes dans Azure, consultez la [documentation sur acs-engine](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Pourquoi deux groupes de ressources sont-ils créés avec AKS ?

Chaque déploiement AKS s’étend sur deux groupes de ressources. Le premier, créé par vous, contient uniquement la ressource du service Kubernetes. Le fournisseur de ressources AKS crée automatiquement le second au cours du déploiement avec un nom tel que *MC_myResourceGroup_myAKSCluster_eastus*. Le second groupe de ressources contient toutes les ressources d’infrastructure associées au cluster, tels que des machines virtuelles, la mise en réseau et le stockage. Il est créé pour simplifier le nettoyage des ressources.

Si vous créez des ressources qui seront utilisées avec votre cluster AKS, tels que les comptes de stockage ou l’adresse IP publique réservée, vous devez les placer dans le groupe de ressources généré automatiquement.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS offre-t-il un contrat de niveau de service ?

Dans un contrat de niveau de service (SLA), le fournisseur accepte de rembourser le client si le niveau du service publié n’est pas rempli. AKS étant gratuit, il n’y a aucuns frais à rembourser et par conséquent aucun contrat SLA formel. Toutefois, nous cherchons à maintenir une disponibilité d’au moins 99,5 % pour le serveur d’API Kubernetes.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
