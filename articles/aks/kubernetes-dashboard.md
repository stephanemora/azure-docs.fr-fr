---
title: Gérer un cluster Azure Kubernetes Service avec le tableau de bord web
description: Découvrez comment utiliser le tableau de bord intégré de l’interface utilisateur web de Kubernetes pour gérer un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595346"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Accéder au tableau de bord web Kubernetes dans Azure Kubernetes Service (AKS)

Kubernetes comprend un tableau de bord web qui peut être utilisé pour les opérations de gestion de base. Ce tableau de bord vous permet d’afficher l’état de santé de base et les métriques concernant vos applications, ainsi que de créer et déployer des services et de modifier les applications existantes. Cet article vous montre comment accéder au tableau de bord Kubernetes à l’aide de l’interface CLI Azure, et fournit des instructions pour certaines opérations de base.

Pour plus d’informations sur le tableau de bord Kubernetes, consultez la section [Tableau de bord de l’interface utilisateur web de Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans ce document supposent que vous avez créé un cluster AKS et que vous avez établi une connexion `kubectl` avec le cluster. Si vous avez besoin de créer un cluster AKS, consultez le [guide de démarrage rapide AKS][aks-quickstart].

Vous devez également avoir installé et configuré Azure CLI version 2.0.46 ou ultérieure. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Ouvrir le tableau de bord Kubernetes

Pour ouvrir le tableau de bord Kubernetes, utilisez la commande [az aks browse][az-aks-browse]. L’exemple suivant ouvre le tableau de bord du cluster nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Cette commande crée un proxy entre votre système de développement et l’API Kubernetes, et ouvre un navigateur web sur le tableau de bord Kubernetes. Si le tableau de bord Kubernetes ne s’ouvre pas dans le navigateur web, copiez et collez l’adresse URL indiquée dans Azure CLI (habituellement `http://127.0.0.1:8001`).

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Si votre cluster AKS utilise RBAC, un *ClusterRoleBinding* doit être créé avant de pouvoir accéder correctement au tableau de bord. Par défaut, le tableau de bord Kubernetes est déployé avec un accès en lecture minimal et affiche les erreurs d’accès RBAC. À l’heure actuelle, il ne prend pas en charge les informations d’identification fournies par l’utilisateur pour déterminer le niveau d’accès ; il utilise en revanche les rôles accordés au compte de service. Un administrateur de cluster peut choisir d’accorder un accès supplémentaire au compte de service *kubernetes-dashboard* ; cependant, cela pourrait représenter un vecteur de réaffectation de privilèges. Vous pouvez également intégrer l’authentification Azure Active Directory pour proposer un niveau d’accès plus granulaire.
> 
> Pour créer une liaison, utilisez la commande [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding]. L’exemple suivant montre comment créer un exemple de liaison, mais cet exemple de liaison n’applique pas de composants d’authentification supplémentaires et peut conduire à une utilisation à risque. Le tableau de bord Kubernetes est accessible à tous ceux qui ont accès à l’URL. N’exposez pas le tableau de bord Kubernetes publiquement.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Pour plus d’informations sur l’utilisation des différentes méthodes d’authentification, consultez le wiki du tableau de bord Kubernetes sur le [contrôle des accès][dashboard-authentication].

![Page de présentation du tableau de bord web Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Créer une application

Pour voir comment le tableau de bord Kubernetes peut réduire la complexité des tâches de gestion, nous allons créer une application. Vous pouvez créer une application à partir du tableau de bord Kubernetes en fournissant le texte d’entrée (un fichier YAML) ou en vous aidant d’un assistant graphique.

Pour créer une application, procédez comme suit :

1. Sélectionnez le bouton **Créer** dans l’angle supérieur droit.
1. Pour utiliser l’assistant graphique, choisissez **Create an app** (Créer une application).
1. Donnez un nom au déploiement, par exemple *nginx*.
1. Entrez le nom de l’image de conteneur à utiliser, par exemple *nginx:1.15.5*.
1. Créez un service Kubernetes pour exposer le port 80 réservé au trafic web. Sous **Service**, sélectionnez **Externe**, puis entrez **80** pour le port et le port cible.
1. Lorsque vous êtes prêt, sélectionnez **Déployer** pour créer l’application.

![Déployer une application dans le tableau de bord web Kubernetes](./media/kubernetes-dashboard/create-app.png)

Une à deux minutes sont nécessaires pour assigner une adresse IP externe publique au service Kubernetes. Sur la gauche, sous **Discovery and Load Balancing** (Découverte et équilibrage de charge), sélectionnez **Services**. Le service de votre application est répertorié, y compris les *points de terminaison externes*, comme illustré dans l’exemple suivant :

![Afficher la liste des services et des points de terminaison](./media/kubernetes-dashboard/view-services.png)

Sélectionnez l’adresse du point de terminaison pour ouvrir une fenêtre de navigateur web vers la page NGINX par défaut :

![Afficher la page NGINX par défaut de l’application déployée](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Afficher les informations de pod

Le tableau de bord Kubernetes peut fournir des métriques de supervision de base et des informations de dépannage telles que des journaux d’activité.

Pour plus d’informations sur vos pods d’application, sélectionnez **Pods** dans le menu de gauche. Une liste des pods disponibles s’affiche. Choisissez votre pod *nginx* pour afficher des informations, par exemple la consommation des ressources :

![Afficher les informations de pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Modifier l’application

En plus de la création et de l’affichage des applications, le tableau de bord Kubernetes permet de modifier et de mettre à jour des déploiements d’applications. Pour assurer une redondance supplémentaire à l’application, nous allons augmenter le nombre de réplicas NGINX.

Pour modifier un déploiement :

1. Sélectionnez **Déploiements** dans le menu de gauche, puis choisissez votre déploiement *nginx*.
1. Sélectionnez **Modifier** dans la barre de navigation en haut à droite.
1. Recherchez la valeur `spec.replica` vers la ligne 20. Pour augmenter le nombre de réplicas de l’application, modifiez cette valeur. Elle doit être comprise entre *1* et *3*.
1. Lorsque vous êtes prêt, sélectionnez **Mettre à jour**.

![Modifier le déploiement pour mettre à jour le nombre de réplicas](./media/kubernetes-dashboard/edit-deployment.png)

Il faut un certain temps pour créer les nouveaux pods à l’intérieur d’un jeu de réplicas. Dans le menu de gauche, choisissez **Replica Sets** (Jeux de réplicas), puis votre jeu de réplicas *nginx*. La liste des pods reflète maintenant le nombre de réplicas mis à jour, comme indiqué dans la sortie fournie à titre d’exemple :

![Afficher les informations sur le jeu de réplicas](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le tableau de bord Kubernetes, consultez le [tableau de bord de l’interface utilisateur web de Kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
