---
title: Contrôle des ressources de cluster avec RBAC et Azure AD dans Azure Kubernetes Service
description: Découvrez comment utiliser l’appartenance de groupe Azure Active Directory pour restreindre l’accès aux ressources de cluster à l’aide de contrôle d’accès en fonction du rôle (RBAC) dans Azure Kubernetes Service (ACS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: e974c47d1dfb04f66b622c64a7143d00de87c4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467542"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Contrôler l’accès aux ressources de cluster à l’aide de contrôle d’accès en fonction du rôle et des identités Azure Active Directory dans Azure Kubernetes Service

Azure Kubernetes Service (AKS) peut être configuré pour utiliser Azure Active Directory (AD) pour l’authentification utilisateur. Dans cette configuration, vous connecter à un cluster AKS à l’aide d’un jeton d’authentification Azure AD. Vous pouvez également configurer Kubernetes contrôle d’accès en fonction du rôle (RBAC) pour limiter l’accès aux ressources de cluster en fonction d’un utilisateur identité ou appartenance de groupe.

Cet article vous montre comment utiliser l’appartenance de groupe Azure AD pour contrôler l’accès aux espaces de noms et de ressources à l’aide de Kubernetes RBAC dans un cluster AKS du cluster. Utilisateurs et groupes d’exemple sont créés dans Azure AD, puis les rôles et RoleBindings sont créés dans le cluster AKS à accorder les autorisations appropriées pour créer et afficher des ressources.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster ACS existant activé avec l’intégration d’Azure AD. Si vous avez besoin d’un cluster ACS, consultez [intégrer Azure Active Directory avec AKS][azure-ad-aks-cli].

Vous avez besoin d’Azure CLI version 2.0.61 ou ultérieur installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Créer des groupes de démonstration dans Azure AD

Dans cet article, nous allons créer deux rôles d’utilisateur qui peuvent être utilisées pour afficher la façon dont Kubernetes RBAC et Azure AD contrôlent l’accès aux ressources de cluster. Les rôles de deux exemples suivants sont utilisés :

* **Développeur d’applications**
    * Un utilisateur nommé *aksdev* qui fait partie de la *appdev* groupe.
* **Ingénieur de fiabilité de site**
    * Un utilisateur nommé *akssre* qui fait partie de la *opssre* groupe.

Dans les environnements de production, vous pouvez utiliser utilisateurs et groupes au sein d’un locataire Azure AD existants.

Tout d’abord, obtenez l’ID de ressource de votre cluster AKS à l’aide du [show d’ACS az] [ az-aks-show] commande. Affecter l’ID de ressource à une variable nommée *AKS_ID* afin qu’il peut être référencé dans d’autres commandes.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Créer le premier groupe d’exemple dans Azure AD pour les développeurs d’applications à l’aide de la [az ad groupe créer] [ az-ad-group-create] commande. L’exemple suivant crée un groupe nommé *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Maintenant, créez une attribution de rôle Azure pour le *appdev* groupe en utilisant le [créer d’attribution de rôle az] [ az-role-assignment-create] commande. Cette affectation permet d’utiliser n’importe quel membre du groupe `kubectl` pour interagir avec un cluster ACS en leur octroyant le *rôle d’utilisateur Cluster Azure Kubernetes Service*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Si vous recevez une erreur telle que `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, attendez quelques secondes pour l’ID d’objet de groupe Azure AD pour se propager via le répertoire, puis recommencez la `az role assignment create` réexécutez la commande.

Créer un deuxième groupe de l’exemple, celui-ci pour SREs nommé *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Là encore, créer une attribution de rôle Azure pour accorder aux membres du groupe le *rôle d’utilisateur Cluster Azure Kubernetes Service*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Créer des utilisateurs de démonstration dans Azure AD

Avec deux groupes d’exemple créés dans Azure AD pour nos développeurs d’applications et les SREs, maintenant vous permet de créer deux utilisateurs de l’exemple. Pour tester l’intégration de RBAC à la fin de l’article, vous connecter au cluster AKS avec ces comptes.

Créer le premier compte d’utilisateur dans Azure AD à l’aide du [az ad utilisateur créer] [ az-ad-user-create] commande.

L’exemple suivant crée un utilisateur avec le nom d’affichage *AKS Dev* et le nom d’utilisateur principal (UPN) de `aksdev@contoso.com`. Mettre à jour l’UPN pour inclure un domaine vérifié pour votre locataire Azure AD (remplacez *contoso.com* avec votre propre domaine) et fournir votre propre sécurisé `--password` informations d’identification :

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Ajoutez maintenant l’utilisateur à la *appdev* groupe créé dans la section précédente à l’aide la [ajouter membre du groupe az ad] [ az-ad-group-member-add] commande :

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Créer un deuxième compte d’utilisateur. L’exemple suivant crée un utilisateur avec le nom d’affichage *AKS SRE* et le nom d’utilisateur principal (UPN) de `akssre@contoso.com`. Là encore, mettre à jour l’UPN pour inclure un domaine vérifié pour votre locataire Azure AD (remplacez *contoso.com* avec votre propre domaine) et fournir votre propre sécurisé `--password` informations d’identification :

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Créer les ressources de cluster AKS pour les développeurs d’applications

Les groupes Azure AD et les utilisateurs sont maintenant créés. Attributions de rôles Azure ont été créées pour les membres du groupe pour se connecter à un cluster ACS qu’un utilisateur standard. Maintenant, nous allons configurer le cluster AKS pour autoriser l’accès de ces différents groupes à des ressources spécifiques.

Tout d’abord, obtenir des informations d’identification d’administrateur à l’aide de cluster le [az aks get-credentials] [ az-aks-get-credentials] commande. Dans une des sections suivantes, vous obtenez régulièrement *utilisateur* cluster des informations d’identification pour l’authentification Azure AD de voir flux en action.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Créer un espace de noms dans le cluster AKS à l’aide du [kubectl créer l’espace de noms] [ kubectl-create] commande. L’exemple suivant crée un espace de noms *dev*:

```console
kubectl create namespace dev
```

Dans Kubernetes, *rôles* définir les autorisations à accorder, et *RoleBindings* les appliquer aux utilisateurs de votre choisis ou groupes. Ces affectations peuvent s’appliquer à un espace de noms donné ou à l’ensemble du cluster. Pour plus d’informations, consultez [Utilisation de l’autorisation RBAC][rbac-authorization].

Commencez par créer un rôle pour le *dev* espace de noms. Ce rôle accorde des autorisations complètes pour l’espace de noms. Dans les environnements de production, vous pouvez spécifier des autorisations plus granulaires pour différents utilisateurs ou groupes.

Créez un fichier nommé `role-dev-namespace.yaml` et collez le manifeste YAML suivant :

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Créer le rôle à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de fichier YAML du manifeste de votre :

```console
kubectl apply -f role-dev-namespace.yaml
```

Ensuite, obtenez l’ID de ressource pour le *appdev* groupe en utilisant le [show de groupe az ad] [ az-ad-group-show] commande. Ce groupe est défini en tant que l’objet d’un RoleBinding à l’étape suivante.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Maintenant, créez un RoleBinding pour la *appdev* groupe à utiliser le rôle créé précédemment pour l’accès de l’espace de noms. Créez un fichier nommé `rolebinding-dev-namespace.yaml` et collez le manifeste YAML suivant. Sur la dernière ligne, remplacez *groupObjectId* avec l’ID d’objet groupe retourné à partir de la commande précédente :

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Créer le RoleBinding à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de fichier YAML du manifeste de votre :

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Créer les ressources de cluster AKS pour SREs

À présent, répétez les étapes précédentes pour créer un espace de noms, rôle et RoleBinding pour les SREs.

Commencez par créer un espace de noms *sre* à l’aide de la [kubectl créer l’espace de noms] [ kubectl-create] commande :

```console
kubectl create namespace sre
```

Créez un fichier nommé `role-sre-namespace.yaml` et collez le manifeste YAML suivant :

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Créer le rôle à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de fichier YAML du manifeste de votre :

```console
kubectl apply -f role-sre-namespace.yaml
```

Obtenir l’ID de ressource pour le *opssre* groupe en utilisant le [show de groupe az ad] [ az-ad-group-show] commande :

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Créer un RoleBinding pour la *opssre* groupe à utiliser le rôle créé précédemment pour l’accès de l’espace de noms. Créez un fichier nommé `rolebinding-sre-namespace.yaml` et collez le manifeste YAML suivant. Sur la dernière ligne, remplacez *groupObjectId* avec l’ID d’objet groupe retourné à partir de la commande précédente :

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Créer le RoleBinding à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de fichier YAML du manifeste de votre :

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagir avec les ressources de cluster à l’aide d’identités Azure AD

Maintenant, nous allons tester l’autorisations attendues de travail lorsque vous créez et gérez des ressources dans un cluster AKS. Dans ces exemples, vous planifiez et affichez des pods dans l’espace de noms attribué l’utilisateur. Ensuite, vous tentez de planification et la vue pods en dehors de l’espace de noms attribué.

Tout d’abord, réinitialiser le *kubeconfig* à l’aide du contexte du [az aks get-credentials] [ az-aks-get-credentials] commande. Dans la section précédente, vous définissez le contexte en utilisant les informations d’identification administrateur de cluster. L’utilisateur administrateur ignore les invites de connexion Azure AD. Sans le `--admin` paramètre, le contexte utilisateur est appliqué qui nécessite toutes les demandes soient authentifiées à l’aide d’Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Planification d’une base NGINX pod à l’aide de la [kubectl exécuter] [ kubectl-run] commande dans le *dev* espace de noms :

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

En tant que la connexion invite, entrez les informations d’identification pour vos propres `appdev@contoso.com` compte créé au début de l’article. Une fois que vous êtes correctement connecté, le jeton de compte est mis en cache pour l’avenir `kubectl` commandes. NGINX est de planifier correctement, comme indiqué dans l’exemple de sortie suivant :

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

À présent utiliser le [kubectl get pods] [ kubectl-get] commande pour afficher le nombre de pods dans le *dev* espace de noms.

```console
kubectl get pods --namespace dev
```

Comme indiqué dans l’exemple suivant, le bloc NGINX a été correctement *en cours d’exécution*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Créer et afficher les ressources de cluster en dehors de l’espace de noms attribué

Maintenant que vous tentez d’afficher les pods en dehors de la *dev* espace de noms. Utilisez le [kubectl get pods] [ kubectl-get] commande là encore, cette fois pour voir `--all-namespaces` comme suit :

```console
kubectl get pods --all-namespaces
```

L’appartenance au groupe de l’utilisateur n’a pas un rôle de Kubernetes qui permet cette action, comme indiqué dans l’exemple de sortie suivant :

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Dans la même façon, essayez de planifier un pod dans l’espace de noms différent, tel que le *sre* espace de noms. L’appartenance au groupe de l’utilisateur ne s’alignent pas un rôle de Kubernetes et le RoleBinding accorder ces autorisations, comme indiqué dans l’exemple de sortie suivant :

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Tester l’accès SRE aux ressources de cluster AKS

Pour vérifier que notre l’appartenance au groupe de Azure AD et Kubernetes RBAC fonctionnent correctement entre différents utilisateurs et groupes, essayez les commandes précédentes lorsque connecté en tant que le *opssre* utilisateur.

Réinitialiser le *kubeconfig* à l’aide du contexte du [az aks get-credentials] [ az-aks-get-credentials] commande qui efface le jeton d’authentification précédemment mise en cache pour le *aksdev*  utilisateur :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Essayez de planification et la vue pods dans assigné *sre* espace de noms. Lorsque vous y êtes invité, connectez-vous avec votre propre `opssre@contoso.com` les informations d’identification créées au début de l’article :

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Comme indiqué dans l’exemple suivant, vous pouvez le créer avec succès et afficher les pods :

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Maintenant, essayez d’afficher ou planifier des pods en dehors de l’espace de noms SRE attribué :

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Ces `kubectl` commandes échouent, comme indiqué dans l’exemple suivant. L’utilisateur l’appartenance au groupe et rôle de Kubernetes et RoleBindings n’accordez pas d’autorisations pour créer ou le Gestionnaire des ressources dans d’autres espaces de noms :

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Dans cet article, vous avez créé des ressources dans le cluster AKS et les utilisateurs et groupes dans Azure AD. Pour nettoyer toutes ces ressources, exécutez les commandes suivantes :

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurisation des clusters Kubernetes, consultez [options d’accès et d’identité pour AKS)][rbac-authorization].

Pour obtenir des recommandations sur le contrôle des identités et des ressources, consultez [meilleures pratiques pour l’authentification et autorisation dans AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
