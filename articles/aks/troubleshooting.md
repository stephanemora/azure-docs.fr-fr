---
title: Résoudre les problèmes courants liés à Azure Kubernetes Service
description: Découvrir comment résoudre les problèmes courants liés à l’utilisation d’AKS (Azure Kubernetes Service)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: f0b0ff3ff4ac742a7e850798c736eb31098f66e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65966381"
---
# <a name="aks-troubleshooting"></a>Résolution des problèmes liés à AKS

Lorsque vous créez ou gérez des clusters Azure Kubernetes Service (AKS), vous pouvez occasionnellement rencontrer des problèmes. Cet article décrit en détail certains problèmes courants, et indique comment les résoudre.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>En règle générale, où trouver des informations sur le débogage de problèmes liés à Kubernetes ?

Essayez de vous référer au [guide officiel de résolution des problèmes de clusters Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Il existe également un [guide de résolution des problèmes](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) publié par un ingénieur Microsoft, portant sur le dépannage de pods, nœuds, clusters et autres fonctionnalités.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>J’obtiens une erreur de dépassement de quota pendant une opération de création ou de mise à niveau. Que dois-je faire ? 

Vous devez [demander des cœurs](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Quel est le nombre maximal de pods par nœud pour AKS ?

Le nombre maximal de pods par nœud est défini 30 par défaut si vous déployez un cluster AKS dans le portail Microsoft Azure.
Le nombre maximal de pods par nœud est 110 par défaut si vous déployez un cluster AKS dans Azure CLI. (assurez-vous que vous disposez de la version la plus récente d’Azure CLI). Ce paramètre par défaut peut être modifié à l’aide de l’indicateur `–-max-pods` dans la commande `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>J’obtiens un erreur insufficientSubnetSize quand je déploie un cluster AKS avec des fonctionnalités réseau avancées. Que dois-je faire ?

En cas d’utilisation d’Azure CNI (mise en réseau avancée), AKS préalloue les adresses IP en fonction des « max-pods » par nœud configuré. Le nombre de nœuds dans un cluster AKS peut être compris entre 1 et 110. Sur la base du nombre maximal de pods configurés, la taille de sous-réseau devrait être supérieure au « produit du nombre de nœuds et du nombre maximal de pods par nœud ». L’équation de base suivante en donne un aperçu :

Taille du sous-réseau > nombre de nœuds dans le cluster (en tenant compte des besoins futurs de mise à l’échelle) * nombre maximum de pods par nœud.

Pour plus d’informations, consultez [Planifier l’adressage IP pour votre cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mon pod est bloqué en mode CrashLoopBackOff. Que dois-je faire ?

Il peut y avoir diverses raisons pour que le pod soit bloqué dans ce mode. Vous pourriez examiner :

* Le pod lui-même, en utilisant `kubectl describe pod <pod-name>`.
* Les journaux d’activité en utilisant `kubectl log <pod-name>`.

Pour plus d’informations sur la façon de résoudre les problèmes de pod, voir [Déboguer des applications](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>J’essaie d’activer le contrôle d’accès en fonction du rôle (RBAC) sur un cluster existant. Comment procéder ?

Malheureusement, l’activation du contrôle d’accès en fonction du rôle (RBAC) sur des clusters existants n’est pas prise en charge actuellement. Vous devez créer explicitement de nouveaux clusters. Si vous utilisez l’interface CLI, le contrôle d’accès en fonction du rôle (RBAC) est activé par défaut. Si vous utilisez le portail AKS, un bouton bascule pour activer le contrôle d’accès en fonction du rôle (RBAC) est disponible dans le flux de travail de création.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>J’ai créé un cluster avec le contrôle d’accès en fonction du rôle (RBAC) activé à l’aide d’Azure CLI avec les valeurs par défaut ou du portail Microsoft Azure, et je vois maintenant de nombreux avertissements sur le tableau de bord Kubernetes. Le tableau de bord n’affichait généralement aucun avertissement. Que dois-je faire ?

Les avertissements sur le tableau de bord s’expliquent par le fait que le cluster est désormais activé avec le contrôle d’accès en fonction du rôle (RBAC) et que son accès a été désactivé par défaut. En règle générale, cette approche est une bonne pratique parce que l’exposition par défaut du tableau de bord à tous les utilisateurs du cluster peut entraîner des menaces de sécurité. Si vous souhaitez quand même activer le tableau de bord, procédez de la manière décrite dans ce [billet de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Je ne parviens pas à me connecter au tableau de bord. Que dois-je faire ?

La façon la plus simple d’accéder à votre service à l’extérieur du cluster consiste à exécuter `kubectl proxy`, qui redirige les demandes adressées via proxy à votre port localhost 8001 vers le serveur d’API Kubernetes. À partir de là, le serveur d’API peut réacheminer par proxy vers votre service : `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Si vous ne voyez pas le tableau de bord Kubernetes, vérifiez si le pod `kube-proxy` est en cours d’exécution dans l’espace de noms `kube-system`. S’il n’est pas en cours d’exécution, supprimez-le afin qu’il redémarre.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Je ne parviens pas à obtenir les journaux d’activité à l’aide des journaux d’activité de kubectl, ou à me connecter au serveur API. J’obtiens le message « Error from server: error dialing backend: dial tcp… ». Que dois-je faire ?

Vérifiez que le groupe de sécurité réseau (NSG) par défaut n’est pas modifié et que le port 22 est ouvert pour la connexion au serveur d’API. Vérifiez que le pod `tunnelfront` s’exécute dans l’espace de noms *kube-system* à l’aide de la commande `kubectl get pods --namespace kube-system`. Si ce n’est pas le cas, forcez la suppression du pod pour qu’il redémarre.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>J’essaie d’effectuer une mise à niveau ou une mise à l’échelle et j’obtiens l’erreur suivante : « Changing property 'imageReference' is not allowed » (La modification de la propriété 'imageReference' n’est pas autorisée). Comment résoudre ce problème ?

Cette erreur s’affiche peut-être parce que vous avez modifié les balises dans les nœuds d’agent à l’intérieur du cluster AKS. La modification et la suppression de balises et d’autres propriétés de ressources dans le groupe de ressources MC_ peuvent entraîner des résultats inattendus. La modification des ressources du groupe MC_ * dans le cluster AKS empêche d’atteindre l’objectif de niveau de service (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Je reçois des erreurs qui indiquent que mon cluster est en état d’échec et que la mise à niveau ou la mise à l’échelle n’aboutira pas tant que ce problème n’aura pas été résolu

*Cette assistance de dépannage est redirigée depuis https://aka.ms/aks-cluster-failed*

Cette erreur se produit quand des clusters basculent dans un état d’échec pour plusieurs raisons. Suivez les étapes ci-dessous pour résoudre l’état d’échec de votre cluster avant de tenter à nouveau l’opération qui a échoué précédemment :

1. Tant que le cluster n’aura pas quitté l’état `failed`, les opérations `upgrade` et `scale` n’aboutiront pas. Voici quelques-unes des causes courantes à l’origine du problème ainsi que des solutions :
    * Mise à l’échelle avec un **quota de calcul (CRP) insuffisant**. Pour résoudre ce problème, remettez votre cluster dans un état stable dans les limites du quota. Suivez ensuite ces [étapes pour demander une augmentation du quota de calcul](../azure-supportability/resource-manager-core-quotas-request.md) avant de tenter à nouveau un scale-up au-delà des limites du quota initial.
    * Mise à jour d’un cluster avec des fonctionnalités réseau avancées et des **ressources (réseau) de sous-réseau insuffisantes**. Pour résoudre ce problème, remettez votre cluster dans un état stable dans les limites du quota. Suivez ensuite ces [étapes pour demander une augmentation du quota de ressources](../azure-resource-manager/resource-manager-quota-errors.md#solution) avant de tenter à nouveau un scale-up au-delà des limites du quota initial.
2. Une fois que la cause sous-jacente de l’échec de mise à niveau est résolue, votre cluster doit être dans un état de réussite. Une fois que l’état de réussite a été vérifié, tentez à nouveau l’opération d’origine.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Je reçois des erreurs lors de la mise à niveau ou de la mise à l’échelle qui indiquent que mon cluster fait actuellement l’objet d’une mise à niveau ou que l’opération a échoué

*Cette assistance de dépannage est redirigée depuis https://aka.ms/aks-pending-upgrade*

Les opérations de cluster sont limitées quand des opérations de mise à niveau active sont en cours ou qu’une mise à niveau a été entreprise pour finalement échouer. Pour diagnostiquer le problème, exécutez `az aks show -g myResourceGroup -n myAKSCluster -o table` pour récupérer l’état détaillé de votre cluster. Selon le résultat :

* Si le cluster est en cours de mise à niveau, patientez le temps que l’opération se termine. Si elle a abouti, tentez à nouveau l’opération qui a échoué auparavant.
* Si la mise la mise à niveau du cluster a échoué, suivez les étapes décrites plus haut.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Puis-je déplacer mon cluster vers un autre abonnement ou mon abonnement et mon cluster vers un nouveau locataire ?

Si vous avez déplacé votre cluster AKS vers un autre abonnement ou l’abonnement propriétaire du cluster vers un nouveau locataire, le cluster perdra sa fonctionnalité en raison de la perte des attributions de rôles et des droits de principaux du service. **AKS ne prend pas en charge le déplacement de clusters entre abonnements ou locataires** du fait de cette contrainte.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Je reçois des erreurs quand j’essaie d’utiliser des fonctionnalités qui nécessitent des groupes de machines virtuelles identiques

*Cette assistance de dépannage est redirigée depuis aka.ms/aks-vmss-enablement*

Vous pouvez recevoir des erreurs qui indiquent que votre cluster AKS n’appartient pas à un groupe de machines virtuelles identiques, comme dans l’exemple suivant :

**AgentPool 'agentpool' has set auto scaling as enabled but is not on Virtual Machine Scale Sets**

Pour utiliser des fonctionnalités telles que le composant Cluster Autoscaler ou plusieurs pools de nœuds, il est nécessaire de créer des clusters AKS qui utilisent des groupes de machines virtuelles identiques. Des erreurs sont retournées si vous tentez d’utiliser des fonctionnalités qui dépendent de groupes de machines virtuelles identiques et que vous ciblez un cluster AKS normal composé d’un groupe de machines identiques non virtuelles. La prise en charge des groupes de machines virtuelles identiques est actuellement en préversion dans AKS.

Suivez les étapes *Avant de commencer* décrites dans la documentation appropriée pour inscrire correctement la préversion de la fonctionnalité de groupe de machines virtuelles identiques et créer un cluster AKS :

* [Utiliser le composant Cluster Autoscaler](cluster-autoscaler.md)
* [Créer et utiliser plusieurs pools de nœuds](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Quelles restrictions d’affectation de noms sont appliquées pour les paramètres et les ressources AKS ?

*Cette assistance de dépannage est redirigée depuis aka.ms/aks-naming-rules*

Les restrictions d’affectation de noms sont implémentées par la plateforme Azure et AKS. Si un nom ou paramètre de ressource enfreint une de ces restrictions, une erreur est retournée qui vous invite à fournir une entrée différente. Voici quelques-unes des recommandations qui s’appliquent en matière d’affectation de noms :

* Le nom de groupe de ressources AKS *MC_* combine le nom de groupe de ressources et le nom de la ressource. La syntaxe générée automatiquement de `MC_resourceGroupName_resourceName_AzureRegion` ne doit pas dépasser 80 caractères. Si nécessaire, réduisez la longueur du nom de groupe de ressources ou du nom de cluster AKS.
* L’élément *dnsPrefix* doit commencer et se terminer par des valeurs alphanumériques. Parmi les caractères autorisés figurent les valeurs alphanumériques et les traits d’union (-). L’élément *dnsPrefix* ne peut pas inclure de caractères spéciaux comme un point (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Je reçois une erreur quand j’essaie de créer, mettre à jour, mettre à l’échelle, supprimer ou mettre à niveau un cluster ; ces opérations ne sont pas autorisées quand une autre opération est en cours.

*Cette assistance de dépannage est redirigée depuis aka.ms/aks-pending-operation*

Les opérations de cluster sont limitées quand une opération précédente est toujours en cours. Pour récupérer un état détaillé de votre cluster, utilisez la commande `az aks show -g myResourceGroup -n myAKSCluster -o table`. Utilisez le nom de votre propre groupe de ressources et de votre propre cluster AKS, selon les besoins.

Selon la sortie de l’état du cluster :

* Si le cluster est dans un autre état de provisionnement que *Opération réussie* ou *En échec*, attendez que l’opération (*mise à niveau/mise à jour/création/mise à l’échelle/suppression/migration*) se termine. Une fois que l’opération précédente a abouti, tentez à nouveau votre dernière opération de cluster.

* Si la mise à niveau du cluster a échoué, suivez les étapes décrites dans [Je reçois des erreurs qui indiquent que mon cluster est en état d’échec et que la mise à niveau ou la mise à l’échelle n’aboutira pas tant que ce problème n’aura pas été résolu](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).