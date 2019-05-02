---
title: Intégrer Azure Active Directory dans Azure Kubernetes Service
description: Découvrez comment utiliser Azure CLI pour créer et cluster compatibles avec Azure Active Directory Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: 0216a8c7d4e52e89098979223e9b792398e25038
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920178"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Intégrer Azure Active Directory avec Azure Kubernetes Service à l’aide de l’interface CLI Azure

Azure Kubernetes Service (AKS) peut être configuré pour utiliser Azure Active Directory (AD) pour l’authentification utilisateur. Dans cette configuration, vous pouvez vous connecter à un cluster AKS à l’aide d’un jeton d’authentification Azure AD. Opérateurs de cluster peuvent également configurer de contrôle d’accès en fonction du rôle Kubernetes (RBAC) selon l’appartenance au groupe d’un utilisateur identité ou le répertoire.

Cet article vous montre comment créer les composants d’Azure AD, puis déployer un cluster AD activé Azure et créer un rôle RBAC de base dans le cluster AKS. Vous pouvez également [effectuez ces étapes en utilisant le portail Azure][azure-ad-portal].

Pour le script d’exemple complet utilisé dans cet article, consultez [d’exemples Azure CLI - Intégration d’ACS avec Azure AD][complete-script].

Les limites suivantes s'appliquent :

- Azure AD ne peut être activé que quand vous créez un nouveau cluster prenant en charge RBAC. Vous ne pouvez pas activer Azure AD sur un cluster AKS existant.
- *Invité* utilisateurs dans Azure AD, telles que si vous utilisez une authentification fédérée dans à partir d’un autre répertoire, ne sont pas pris en charge.

## <a name="before-you-begin"></a>Avant de commencer

Vous avez besoin d’Azure CLI version 2.0.61 ou ultérieur installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][install-azure-cli].

Par souci de cohérence et pour aider à exécuter les commandes dans cet article, créez une variable pour le nom de votre cluster AKS souhaitée. L’exemple suivant utilise le nom *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Présentation de l’authentification Azure AD

L’authentification Azure AD est fournie aux clusters AKS à l’aide d’OpenID Connect. OpenID Connect est une couche d’identité basée sur le protocole OAuth 2.0. Pour plus d’informations sur OpenID Connect, consultez la documentation [Open ID connect][open-id-connect].

Depuis le cluster Kubernetes, l’authentification par jeton de Webhook est utilisée pour vérifier les jetons d’authentification. L’authentification par jeton de Webhook est configurée et gérée en tant que partie du cluster AKS. Pour plus d’informations sur l’authentification par jeton Webhook, consultez la [documentation relative à l’authentification de webhook][kubernetes-webhook].

> [!NOTE]
> Lors de la configuration d’Azure AD pour l’authentification AKS, deux applications Azure AD sont configurées. Cette opération doit être effectuée par un administrateur de locataire Azure.

## <a name="create-azure-ad-server-component"></a>Créer le composant de serveur Azure AD

Pour intégrer avec AKS, vous créez et utilisez une application Azure AD qui agit comme un point de terminaison pour les demandes d’identité. La première application Azure AD, que vous devez Obtient l’appartenance au groupe de Azure AD pour un utilisateur.

Créer le composant d’application serveur en utilisant le [créer az ad app] [ az-ad-app-create] commande, puis mise à jour l’appartenance au groupe des revendications à l’aide de la [mise à jour de az ad app] [ az-ad-app-update] commande. L’exemple suivant utilise le *aksname* variable définie dans le [avant de commencer](#before-you-begin) section et crée une variable

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Créez maintenant un principal de service pour l’application serveur en utilisant le [créer az ad sp] [ az-ad-sp-create] commande. Ce principal du service est utilisé pour s’authentifier au sein de la plateforme Azure. Ensuite, obtenez le service principal du secret à l’aide de la [réinitialisation des informations d’identification de az ad sp] [ az-ad-sp-credential-reset] commande et affecter à la variable nommée *serverApplicationSecret* pour une utilisation dans un du étapes suivantes :

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD doit être autorisé à effectuer les actions suivantes :

* Lire les données du répertoire
* Connecter et lire le profil utilisateur

Attribuez ces autorisations à l’aide de la [ajouter l’autorisation d’application ad az] [ az-ad-app-permission-add] commande :

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Enfin, accordez les autorisations attribuées à l’étape précédente pour l’application serveur en utilisant le [octroi d’autorisation az ad app] [ az-ad-app-permission-grant] commande. Cette étape échoue si le compte actuel n’est pas un administrateur de locataire. Vous devez également ajouter des autorisations d’application Azure AD demander des informations qui peuvent nécessiter sinon le consentement d’administration à l’aide de la [az ad app autorisation-consentement de l’administrateur][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Créer le composant de client Azure AD

La deuxième application Azure AD est utilisée lorsqu’un utilisateur se connecte au cluster AKS avec la CLI Kubernetes (`kubectl`). Cette application cliente prend la demande d’authentification de l’utilisateur et vérifie leurs informations d’identification et les autorisations. Créer l’application Azure AD pour le composant de client à l’aide de la [créer az ad app] [ az-ad-app-create] commande :

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Créer un principal de service pour l’application cliente utilisant la [créer az ad sp] [ az-ad-sp-create] commande :

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Obtenir l’ID d’oAuth2 pour l’application serveur pour autoriser le flux d’authentification entre les composants d’application de deux à l’aide de la [show de az ad app] [ az-ad-app-show] commande. Cet ID oAuth2 est utilisé dans l’étape suivante.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Ajoutez les autorisations pour l’application cliente et les composants d’application serveur à utiliser la communication oAuth2 flux à l’aide de la [ajouter l’autorisation d’application ad az] [ az-ad-app-permission-add] commande. Ensuite, accorder des autorisations pour l’application cliente pour la communication avec l’application serveur en utilisant le [octroi d’autorisation az ad app] [ az-ad-app-permission-grant] commande :

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Déployer le cluster

Les deux applications Azure AD créées, désormais créer le cluster AKS lui-même. Tout d’abord, créez un groupe de ressources avec le [az groupe créer] [ az-group-create] commande. L’exemple suivant crée le groupe de ressources dans le *EastUS* région :

Créer un groupe de ressources pour le cluster :

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Obtenir l’ID client de votre abonnement Azure à l’aide de la [show de compte az] [ az-account-show] commande. Ensuite, créez le cluster AKS à l’aide du [créer az aks] [ az-aks-create] commande. La commande pour créer le cluster AKS fournit le serveur et client ID d’application, le secret principal du service d’application serveur et votre ID de locataire :

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Enfin, obtenir le cluster des informations d’identification d’administrateur à l’aide de la [az aks get-credentials] [ az-aks-get-credentials] commande. Dans une des étapes suivantes, vous obtenez régulièrement *utilisateur* cluster des informations d’identification pour l’authentification Azure AD de voir flux en action.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Créer une liaison RBAC

Avant de pouvoir utiliser un compte Azure Active Directory avec le cluster AKS, une liaison de rôle ou une liaison de rôle de cluster doit être créée. Les *rôles*définissent les permissions à accorder, et les *liaisons*les appliquent aux utilisateurs souhaités. Ces affectations peuvent s’appliquer à un espace de noms donné ou à l’ensemble du cluster. Pour plus d’informations, consultez [Utilisation de l’autorisation RBAC][rbac-authorization].

Obtenir le nom d’utilisateur principal (UPN) pour l’utilisateur actuellement connecté à l’aide de la [show d’utilisateur connecté az ad] [ az-ad-signed-in-user-show] commande. Ce compte d’utilisateur est activé pour l’intégration d’Azure AD à l’étape suivante.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Si l’utilisateur vous accordez la liaison de RBAC pour est dans le même locataire Azure AD, attribuer des autorisations en fonction de la *userPrincipalName*. Si l’utilisateur est dans un autre annuaire Azure AD locataire, rechercher et utiliser le *objectId* propriété à la place.

Créez un manifeste YAML nommé `basic-azure-ad-binding.yaml` et collez le contenu suivant. Sur la dernière ligne, remplacez *userPrincipalName_or_objectId* avec l’UPN ou l’objet ID issu de la commande précédente :

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Créer le ClusterRoleBinding à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de fichier YAML du manifeste de votre :

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Accéder au cluster avec Azure AD

Maintenant nous allons tester l’intégration de l’authentification Azure AD pour le cluster AKS. Définir le `kubectl` le contexte de configuration à utiliser les informations d’identification de l’utilisateur standard. Ce contexte passe toutes les demandes d’authentification via Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Utilisez maintenant le [kubectl get pods] [ kubectl-get] commande pour afficher les pods entre tous les espaces de noms :

```console
kubectl get pods --all-namespaces
```

Vous recevez un authentification dans l’invite de commandes pour s’authentifier à l’aide des informations d’identification Azure AD à l’aide d’un navigateur web. Une fois que vous avez été authentifié, le `kubectl` commande affiche les pods dans le cluster AKS, comme indiqué dans l’exemple de sortie suivant :

```console
$ kubectl get pods --all-namespaces

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Le jeton d’authentification reçu pour `kubectl` est mis en cache. Vous sont uniquement différentes pour vous connecter lorsque le jeton a expiré ou le fichier de configuration Kubernetes est recréé.

Si vous voyez un message d’erreur d’autorisation une fois que vous êtes connecté avec succès à l’aide d’un navigateur web, comme dans l’exemple suivant, vérifiez les problèmes possibles suivants :

```console
error: You must be logged in to the server (Unauthorized)
```

* L’utilisateur que vous êtes connecté dans la mesure n’est ne pas un *invité* dans l’instance d’Azure AD (cela est souvent le cas si vous utilisez un compte de connexion fédérée à partir d’un répertoire différent).
* L'utilisateur n’est pas membre de plus de 200 groupes.

## <a name="next-steps"></a>Étapes suivantes

Pour le script complet qui contient les commandes indiquées dans cet article, consultez le [dépôt d’exemples de script d’intégration Azure AD dans le AKS][complete-script].

Pour utiliser des groupes et utilisateurs Azure AD pour contrôler l’accès aux ressources de cluster, consultez [contrôler l’accès aux ressources de cluster à l’aide de contrôle d’accès en fonction du rôle et des identités Azure AD dans ACS][azure-ad-rbac].

Pour plus d’informations sur la sécurisation des clusters Kubernetes, consultez [options d’accès et d’identité pour AKS)][rbac-authorization].

Pour obtenir des recommandations sur le contrôle des identités et des ressources, consultez [meilleures pratiques pour l’authentification et autorisation dans AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
