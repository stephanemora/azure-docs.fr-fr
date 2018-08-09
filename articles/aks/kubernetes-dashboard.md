---
title: Gérer le cluster Kubernetes Azure par l’intermédiaire de l’interface utilisateur web
description: Découvrez comment utiliser le tableau de bord intégré de l’interface utilisateur web de Kubernetes avec Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: af48af596e86e0eb09fe45deabe13beedef57cd2
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307923"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Accéder au tableau de bord Kubernetes avec Azure Kubernetes Service (AKS)

Kubernetes comprend un tableau de bord web qui peut être utilisé pour les opérations de gestion de base. Cet article vous montre comment accéder au tableau de bord Kubernetes à l’aide de l’interface CLI Azure, et fournit des instructions pour certaines opérations de base. Pour plus d’informations sur le tableau de bord Kubernetes, consultez [Tableau de bord de l’interface utilisateur web de Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans ce document supposent que vous avez créé un cluster AKS et que vous avez établi une connexion `kubectl` avec le cluster. Si vous avez besoin de créer un cluster AKS, suivez le [guide de démarrage rapide AKS][aks-quickstart].

Vous devez également avoir installé et configuré Azure CLI version 2.0.27 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Démarrer le tableau de bord Kubernetes

Pour ouvrir le tableau de bord Kubernetes, utilisez la commande [az aks browse][az-aks-browse]. L’exemple suivant ouvre le tableau de bord du cluster nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Cette commande crée un proxy entre votre système de développement et l’API Kubernetes, et ouvre un navigateur web sur le tableau de bord Kubernetes.

### <a name="for-rbac-enabled-clusters"></a>Pour les clusters où RBAC est activé

Si votre cluster AKS utilise RBAC, un *ClusterRoleBinding* doit être créé avant de pouvoir accéder correctement au tableau de bord. Par défaut, le tableau de bord Kubernetes est déployé avec un accès en lecture minimal et affiche les erreurs d’accès RBAC. À l’heure actuelle, il ne prend pas en charge les informations d’identification fournies par l’utilisateur pour déterminer le niveau d’accès ; il utilise en revanche les rôles accordés au compte de service. Un administrateur de cluster peut choisir d’accorder un accès supplémentaire au compte de service *kubernetes-dashboard* ; cependant, cela pourrait représenter un vecteur de réaffectation de privilèges. Vous pouvez également intégrer l’authentification Azure Active Directory pour proposer un niveau d’accès plus granulaire.

Pour créer une liaison, utilisez la commande [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding], comme indiqué dans l’exemple suivant. 

> [!WARNING]
> Cet exemple de liaison n’applique pas de composants d’authentification supplémentaires et peut conduire à une utilisation non sécurisée. Le tableau de bord Kubernetes est accessible à tous ceux qui ont accès à l’URL. N’exposez pas le tableau de bord Kubernetes publiquement.
>
> Pour plus d’informations sur l’utilisation des différentes méthodes d’authentification, consultez le wiki du tableau de bord Kubernetes sur le [contrôle des accès][dashboard-authentication].

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

Vous pouvez maintenant accéder au tableau de bord Kubernetes dans votre cluster RBAC. Pour ouvrir le tableau de bord Kubernetes, utilisez la commande [az aks browse][az-aks-browse], comme expliqué à l’étape précédente.

## <a name="run-an-application"></a>Exécuter une application

Dans le tableau de bord Kubernetes, cliquez sur le bouton **Créer** dans la fenêtre supérieure droite. Nommez le déploiement `nginx`, puis entrez `nginx:latest` pour le nom de l’image de conteneur. Sous **Service**, sélectionnez **Externe**, puis entrez `80` pour le port et le port cible.

Lorsque vous êtes prêt, cliquez sur **Déployer** pour créer le déploiement.

![Boîte de dialogue de création de service Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Afficher l’application

L’état de l’application peut être consulté sur le tableau de bord Kubernetes. Une fois l’application en cours d’exécution, une case à cocher verte apparaît en regard de chaque composant.

![Pods kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Pour plus d’informations sur les pods d’application, cliquez sur **Pods** dans le menu de gauche, puis sélectionnez le pod **NGINX**. Vous pouvez voir ici des informations spécifiques du pod, telles que la consommation de ressources.

![Ressources Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Pour trouver l’adresse IP de l’application, cliquez sur **Services** dans le menu de gauche, puis sélectionnez le service **NGINX**.

![Vue nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Modifier l’application

En plus de la création et de l’affichage des applications, le tableau de bord Kubernetes permet de modifier et de mettre à jour des déploiements d’applications.

Pour modifier un déploiement, cliquez sur **Déploiements** dans le menu de gauche, puis sélectionnez le déploiement **NGINX**. Enfin, sélectionnez **Modifier** dans la barre de navigation en haut à droite.

![Modification de Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Recherchez la valeur de `spec.replica`, qui devrait être 1, et modifiez-la en 3. De ce fait, le nombre de réplicas du déploiement NGINX passe de 1 à 3.

Lorsque vous êtes prêt, sélectionnez **Mettre à jour**.

![Modification de Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le tableau de bord Kubernetes, voir la documentation de Kubernetes.

> [!div class="nextstepaction"]
> [Tableau de bord de l’interface utilisateur web de Kubernetes][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
