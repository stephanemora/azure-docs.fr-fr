---
title: Résoudre les problèmes courants liés à Azure Kubernetes Service
description: Découvrir comment résoudre les problèmes courants liés à l’utilisation d’AKS (Azure Kubernetes Service)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 5902ba86b51ca1998364e393ac02bbb0d0a23a28
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432632"
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
* Les journaux en utilisant `kubectl log <pod-name>`.

Pour plus d’informations sur la façon de résoudre les problèmes de pod, voir [Déboguer des applications](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>J’essaie d’activer le contrôle d’accès en fonction du rôle (RBAC) sur un cluster existant. Comment procéder ?

Malheureusement, l’activation du contrôle d’accès en fonction du rôle (RBAC) sur des clusters existants n’est pas prise en charge actuellement. Vous devez créer explicitement de nouveaux clusters. Si vous utilisez l’interface CLI, le contrôle d’accès en fonction du rôle (RBAC) est activé par défaut. Si vous utilisez le portail AKS, un bouton bascule pour activer le contrôle d’accès en fonction du rôle (RBAC) est disponible dans le flux de travail de création.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>J’ai créé un cluster avec le contrôle d’accès en fonction du rôle (RBAC) activé à l’aide d’Azure CLI avec les valeurs par défaut ou du portail Microsoft Azure, et je vois maintenant de nombreux avertissements sur le tableau de bord Kubernetes. Le tableau de bord n’affichait généralement aucun avertissement. Que dois-je faire ?

Les avertissements sur le tableau de bord s’expliquent par le fait que le cluster est désormais activé avec le contrôle d’accès en fonction du rôle (RBAC) et que son accès a été désactivé par défaut. En règle générale, cette approche est une bonne pratique parce que l’exposition par défaut du tableau de bord à tous les utilisateurs du cluster peut entraîner des menaces de sécurité. Si vous souhaitez quand même activer le tableau de bord, procédez de la manière décrite dans ce [billet de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Je ne parviens pas à me connecter au tableau de bord. Que dois-je faire ?

La façon la plus simple d’accéder à votre service à l’extérieur du cluster consiste à exécuter `kubectl proxy`, qui redirige les demandes adressées via proxy à votre port localhost 8001 vers le serveur d’API Kubernetes. À partir de là, le serveur d’API peut réacheminer par proxy vers votre service : `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Si vous ne voyez pas le tableau de bord Kubernetes, vérifiez si le pod `kube-proxy` est en cours d’exécution dans l’espace de noms `kube-system`. S’il n’est pas en cours d’exécution, supprimez-le afin qu’il redémarre.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Je ne parviens pas à obtenir les journaux à l’aide des journaux de kubectl, ou à me connecter au serveur API. J’obtiens « erreur de serveur : back-end de l’erreur de numérotation : composer tcp... ». Que dois-je faire ?

Assurez-vous que le groupe de sécurité réseau par défaut n’est pas modifié et que le port 22 est ouvert pour la connexion au serveur d’API. Vérifiez si le `tunnelfront` pod est en cours d’exécution le *kube-system* à l’aide de l’espace de noms le `kubectl get pods --namespace kube-system` commande. Si ce n’est pas le cas, forcez la suppression du pod pour qu’il redémarre.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>J’essaie d’effectuer une mise à niveau ou une mise à l’échelle et j’obtiens l’erreur suivante : « Changing property 'imageReference' is not allowed » (La modification de la propriété 'imageReference' n’est pas autorisée). Comment résoudre ce problème ?

Cette erreur s’affiche peut-être parce que vous avez modifié les balises dans les nœuds d’agent à l’intérieur du cluster AKS. La modification et la suppression de balises et d’autres propriétés de ressources dans le groupe de ressources MC_ peuvent entraîner des résultats inattendus. La modification des ressources du groupe MC_ * dans le cluster AKS empêche d’atteindre l’objectif de niveau de service (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Je reçois des erreurs que mon cluster est en état d’échec et de la mise à niveau ou mise à l’échelle ne fonctionnera pas jusqu'à la résolution

*Cette assistance de dépannage est redirigée via https://aka.ms/aks-cluster-failed*

Cette erreur se produit lorsque des clusters permet d’entrer un état d’échec pour plusieurs raisons. Suivez les étapes ci-dessous pour résoudre l’état de votre cluster a échoué avant de recommencer l’opération ayant échouée précédemment :

1. Jusqu'à ce que le cluster est hors `failed` état, `upgrade` et `scale` opérations ne réussissent. Problèmes courants de la racine et de résolutions sont les suivantes :
    * Mise à l’échelle avec **quota insuffisant calcul (CRP)**. Pour résoudre, tout d’abord mettre à l’échelle votre cluster à un état stable d’objectif dans le quota. Puis suivez ces [étapes pour demander un quota de calcul augmentation](../azure-supportability/resource-manager-core-quotas-request.md) avant d’essayer de monter en puissance à nouveau les limites de quota initiale d’au-delà.
    * Mise à l’échelle un cluster avec advanced mise en réseau et **ressources de sous-réseau insuffisant (mise en réseau)**. Pour résoudre, tout d’abord mettre à l’échelle votre cluster à un état stable d’objectif dans le quota. Puis suivez [ces étapes pour demander un quota de ressources augmentation](../azure-resource-manager/resource-manager-quota-errors.md#solution) avant d’essayer de monter en puissance à nouveau les limites de quota initiale d’au-delà.
2. Une fois que la cause sous-jacente de l’échec de mise à niveau est résolue, votre cluster doit être dans un état de réussite. Une fois que l’état réussi est vérifié, recommencez l’opération d’origine.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Je reçois des erreurs lors de la mise à niveau ou mise à l’échelle que l’état de mon cluster actuellement étant mis à niveau ou l’échec de mise à niveau

*Cette assistance de dépannage est redirigée via https://aka.ms/aks-pending-upgrade*

Opérations de cluster sont limitées lors des opérations de mise à niveau actives sont en cours ou une mise à niveau a été tentée, mais par la suite a échoué. Pour diagnostiquer ce problème, exécutez `az aks show -g myResourceGroup -n myAKSCluster -o table` pour récupérer l’état détaillé sur votre cluster. En fonction du résultat :

* Si le cluster est activement mise à niveau, patientez jusqu'à ce que l’opération se termine. Si elle a réussi, recommencez l’opération ayant échouée précédemment.
* Si le cluster a échoué mise à niveau, suivez les étapes présentées ci-dessus
