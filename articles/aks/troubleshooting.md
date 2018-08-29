---
title: Résoudre les problèmes courants liés à Azure Kubernetes Service
description: Découvrir comment résoudre les problèmes courants liés à l’utilisation d’AKS (Azure Kubernetes Service)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 614930fb11e65404416e604c94351e2754b8e941
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190802"
---
# <a name="aks-troubleshooting"></a>Résolution des problèmes liés à AKS
Quand vous créez ou gérez des clusters AKS, vous pouvez parfois rencontrer des problèmes. Cet article décrit en détail certains problèmes courants et indique la façon de les résoudre.

### <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-issues"></a>En règle générale, où puis-je trouver des informations sur le débogage des problèmes liés à Kubernetes ?

[Voici] (https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/) un lien officiel indiquant comment résoudre les problèmes liés aux clusters kubernetes.
[Voici](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) un lien vers un guide de résolution des problèmes publié par un ingénieur Microsoft, portant sur la résolution des problèmes liés aux pods, nœuds, clusters, etc.

### <a name="i-am-getting-a-quota-exceeded-error-during-create-or-upgrade-what-should-i-do"></a>J’obtiens une erreur de dépassement de quota pendant une opération de création ou de mise à niveau. Que dois-je faire ? 

Vous devez demander des cœurs [ici](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

### <a name="what-is-the-max-pods-per-node-setting-for-aks"></a>Quel est le nombre maximal de pods par nœud pour AKS ?

Le nombre maximal de pods par nœud est défini sur 30 par défaut si vous déployez un cluster AKS dans le portail Azure.
Le nombre maximal de pods par nœud est défini sur 110 par défaut si vous déployez un cluster AKS dans l’interface de ligne de commande Azure. (Vérifiez que vous utilisez bien la dernière version de l’interface de ligne de commande Azure). Vous pouvez changer ce paramètre par défaut en utilisant l’indicateur –max-nodes-per-pod dans la commande az aks create.

### <a name="i-am-getting--insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>J’obtiens l’erreur « insufficientSubnetSize » (taille de sous-réseau insuffisante) quand je déploie un cluster AKS avec des fonctionnalités réseau avancées. Que dois-je faire ?

Dans l’option de réseau virtuel personnalisé sélectionnée pour la mise en réseau pendant l’opération de création AKS, Azure CNI est utilisé pour IPAM. Le nombre de nœuds dans un cluster AKS peut être compris entre 1 et 100. La taille du sous-réseau doit être supérieure au produit du nombre de nœuds par le nombre maximal de pods par nœud.

### <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mon pod est bloqué en mode « CrashLoopBackOff ». Que dois-je faire ?

Il peut y avoir diverses raisons pour que le pod soit bloqué dans ce mode. Vous pouvez explorer 
* Le pod lui-même à l’aide de `kubectl describe pod <pod-name>`
* Les journaux à l’aide de `kubectl log <pod-name>`

### <a name="i-am-trying-to-enable-rbac-on-an-existing-cluster-can-you-tell-me-how-i-can-do-that"></a>J’essaie d’activer RBAC sur un cluster existant. Pouvez-vous me dire comment effectuer cette opération ?

Malheureusement, l’activation de RBAC sur des clusters existants n’est pas prise en charge pour l’instant. Vous devez créer des clusters explicitement. Si vous utilisez l’interface CLI, RBAC est activé par défaut, tandis qu’un bouton bascule pour l’activer est disponible dans le flux de travail de création du portail AKS.

### <a name="i-created-a-cluster-using-the-azure-cli-with-defaults-or-the-azure-portal-with-rbac-enabled-and-numerous-warnings-in-the-kubernetes-dashboard-the-dashboard-used-to-work-before-without-any-warnings-what-should-i-do"></a>J’ai créé un cluster à l’aide de l’interface de ligne de commande Azure en utilisant les valeurs par défaut ou le portail Azure avec RBAC activé et de nombreux avertissements apparaissent dans le tableau de bord kubernetes. Auparavant, le tableau de bord n’affichait aucun avertissement. Que dois-je faire ?

Le tableau de bord affiche des avertissements parce qu’il est maintenant activé par le biais de RBAC et que son accès a été désactivé par défaut. En règle générale, cette approche est considérée comme une bonne pratique dans la mesure où l’exposition par défaut du tableau de bord à tous les utilisateurs du cluster peut entraîner des menaces de sécurité. Si vous souhaitez quand même activer le tableau de bord, suivez ce [blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/) pour l’activer.

### <a name="i-cant-seem-to-connect-to-the-dashboard-what-should-i-do"></a>Je ne parviens pas à me connecter au tableau de bord. Que dois-je faire ?

La façon la plus simple d’accéder à votre service à l’extérieur du cluster consiste à exécuter la commande kubectl proxy, qui redirige les demandes via proxy à votre port localhost 8001 sur le serveur d’API Kubernetes. À partir de là, le serveur d’API peut effectuer une redirection via proxy vers votre service : http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default

Si vous ne voyez pas le tableau de bord kubernetes, vérifiez si le pod kube-proxy est en cours d’exécution dans l’espace de noms kube-system. S’il n’est pas en cours d’exécution, supprimez-le afin qu’il redémarre.

### <a name="i-could-not-get-logs-using-kubectl-logs-or-cannot-connect-to-the-api-server-getting-the-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Je n’ai pas pu obtenir de journaux à l’aide de la commande kubectl logs ou ne peux pas me connecter au serveur d’API ; j’obtiens une erreur serveur d’accès au backend (« Error from server: error dialing backend: dial tcp... »). Que dois-je faire ?

Vérifiez que le groupe de sécurité réseau par défaut n’est pas modifié et que le port 22 est ouvert pour la connexion au serveur d’API. Vérifiez si le pod tunnelfront s’exécute dans l’espace de noms kube-system. Si ce n’est pas le cas, forcez sa suppression afin qu’il redémarre.

### <a name="i-am-trying-to-upgrade-or-scale-and-am-getting-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-issue"></a>J’essaie d’effectuer une mise à niveau ou une mise à l’échelle et obtiens l’erreur « Changing property 'imageReference' is not allowed » (La modification de la propriété 'imageReference' n’est pas autorisée) .  Comment résoudre ce problème ?

Cette erreur est peut-être due au fait que vous avez modifié les balises dans les nœuds d’agent à l’intérieur du cluster AKS. La modification et la suppression de balises et d’autres propriétés de ressources dans le groupe de ressources MC_ peuvent entraîner des résultats inattendus. La modification des ressources sous MC_* dans le cluster AKS interrompt le SLO.


