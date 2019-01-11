---
title: Résoudre les problèmes courants liés à Azure Kubernetes Service
description: Découvrir comment résoudre les problèmes courants liés à l’utilisation d’AKS (Azure Kubernetes Service)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: fd3d1c464c6f2d4cbecd715db0689581ca141769
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654068"
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

Dans l’option Réseau virtuel Azure personnalisée pour la mise en réseau lors de la création d’AKS, l’Interface de réseau de conteneur (CNI) Azure est utilisée pour la gestion des adresses IP (IPAM). Le nombre de nœuds dans un cluster AKS peut être compris entre 1 et 100. Sur la base de la section précédente, la taille de sous-réseau devrait être supérieure au produit du nombre de nœuds et du nombre maximal de pods par nœud. La relation peut être exprimée comme suit : taille de sous-réseau > nombre de nœuds du cluster * nombre maximal de pods par nœud.

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

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Je ne parviens pas à obtenir les journaux à l’aide des journaux de kubectl, ou à me connecter au serveur API. J’obtiens le message « Error from server: error dialing backend: dial tcp… » Que dois-je faire ?

Assurez-vous que le groupe de sécurité de réseau (NSG) par défaut n’est pas modifié et que le port 22 est ouvert pour la connexion au serveur d’API. Vérifiez si le pod `tunnelfront` est en cours d’exécution dans l’espace de noms `kube-system`. Si ce n’est pas le cas, forcez la suppression du pod pour qu’il redémarre.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-problem"></a>Lorsque j’essaie d’effectuer une mise à niveau ou une mise à l’échelle, j’obtiens l’erreur « Changing property ’imageReference’ is not allowed ».   Comment résoudre ce problème ?

Cette erreur s’affiche peut-être parce que vous avez modifié les balises dans les nœuds d’agent à l’intérieur du cluster AKS. La modification et la suppression de balises et d’autres propriétés de ressources dans le groupe de ressources MC_ peuvent entraîner des résultats inattendus. La modification des ressources du groupe MC_ * dans le cluster AKS empêche d’atteindre l’objectif de niveau de service (SLO).

## <a name="how-do-i-renew-the-service-principal-secret-on-my-aks-cluster"></a>Comment renouveler la clé secrète du principal du service sur mon cluster AKS ?

Par défaut, les clusters AKS sont créés avec un principal de service dont le délai d’expiration est d’un an. À mesure que vous approchez de la date d’expiration, vous pouvez réinitialiser les informations d’identification afin de prolonger le délai d’expiration du principal de service.

Cet exemple effectue les étapes suivantes :

1. Obtient l’ID du principal de service de votre cluster en utilisant la commande [az aks show](/cli/azure/aks#az-aks-show).
1. Répertorie le secret du client du principal de service en utilisant la commande [az ad sp credential list](/cli/azure/ad/sp/credential#az-ad-sp-credential-list).
1. Prolonge d’un an le délai d’expiration du principal de service en utilisant la commande [az ad sp credential-reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset). La clé secrète du client du principal du service doit rester la même pour que le cluster AKS puisse s’exécuter correctement.

```azurecli
# Get the service principal ID of your AKS cluster.
sp_id=$(az aks show -g myResourceGroup -n myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)

# Get the existing service principal client secret.
key_secret=$(az ad sp credential list --id $sp_id --query [].keyId -o tsv)

# Reset the credentials for your AKS service principal and extend for one year.
az ad sp credential reset \
    --name $sp_id \
    --password $key_secret \
    --years 1
```
