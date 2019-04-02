---
title: Intégrer Azure Active Directory dans Azure Kubernetes Service
description: Comment créer des clusters Azure Kubernetes Service (AKS) compatibles avec Azure Active Directory.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: c2ed053479b11bada4cfc0ec808ad148f024dee6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803242"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Intégrer Azure Active Directory dans Azure Kubernetes Service

Azure Kubernetes Service (AKS) peut être configuré pour utiliser Azure Active Directory (AD) pour l’authentification utilisateur. Dans cette configuration, vous pouvez vous connecter à un cluster AKS à l’aide de votre jeton d’authentification Azure Active Directory. En outre, les administrateurs de cluster sont en mesure de configurer le contrôle d’accès en fonction du rôle (RBAC) Kubernetes basé sur une identité utilisateur ou l’appartenance à un groupe de répertoires.

Cet article explique comment déployer les conditions préalables pour AKS et Azure AD, puis comment déployer un cluster compatible Azure AD et créer un rôle RBAC simple dans le cluster AKS.

Les limites suivantes s'appliquent :

- Azure AD ne peut être activé que quand vous créez un nouveau cluster prenant en charge RBAC. Vous ne pouvez pas activer Azure AD sur un cluster AKS existant.
- Les utilisateurs *Invités* dans Azure AD, comme si vous utilisiez une connexion fédérée à partir d’un autre répertoire, ne sont pas pris en charge.

## <a name="authentication-details"></a>Informations sur l’authentification

L’authentification Azure AD est fournie aux clusters AKS à l’aide d’OpenID Connect. OpenID Connect est une couche d’identité basée sur le protocole OAuth 2.0. Pour plus d’informations sur OpenID Connect, consultez la documentation [Open ID connect][open-id-connect].

Depuis le cluster Kubernetes, l’authentification par jeton de Webhook est utilisée pour vérifier les jetons d’authentification. L’authentification par jeton de Webhook est configurée et gérée en tant que partie du cluster AKS. Pour plus d’informations sur l’authentification par jeton Webhook, consultez la [documentation relative à l’authentification de webhook][kubernetes-webhook].

> [!NOTE]
> Lors de la configuration d’Azure AD pour l’authentification AKS, deux applications Azure AD sont configurées. Cette opération doit être effectuée par un administrateur de locataire Azure.

## <a name="create-server-application"></a>Créer une application serveur

La première application Azure AD permet d’obtenir l’appartenance à un groupe d’utilisateurs Azure AD.

1. Sélectionnez **Azure Active Directory** > **Inscriptions des applications** > **Nouvelle inscription d’application**.

   Nommez l’application, sélectionnez **application Web/API** pour le type d’application et entrez une valeur au format URI pour l’**URL de connexion**. Sélectionnez **Créer** lorsque vous avez terminé.

   ![Créer une inscription Azure AD](media/aad-integration/app-registration.png)

2. Sélectionnez **Manifeste** et définissez la valeur `groupMembershipClaims` sur `"All"`.

   Enregistrez les mises à jour lorsque vous avez terminé.

   ![Mettre à jour l’appartenance à un groupe pour l’appliquer à tous](media/aad-integration/edit-manifest.png)

3. Revenez à l’application Azure AD, sélectionnez **Paramètres** > **Clés**.

   Ajoutez une description de clé, sélectionnez un délai d’expiration, puis **Enregistrer**. Notez la valeur de la clé. Lors du déploiement d’un cluster AKS Azure AD, cette valeur est appelée `Server application secret`.

   ![Obtenir la clé privée d’application](media/aad-integration/application-key.png)

4. Revenez à l’application Azure AD, sélectionnez **Paramètres** > **Autorisations requises** > **Ajouter** > **Sélectionner une API** > **Microsoft Graph** > **Sélectionner**.

   ![Sélectionner une API Graph](media/aad-integration/graph-api.png)

5. Sous **AUTORISATIONS D’APPLICATION**, cochez la case **Lire les données de l’annuaire**.

   ![Définir des autorisations d’application pour Microsoft Graph](media/aad-integration/read-directory.png)

6. Sous **AUTORISATIONS DÉLÉGUÉES**, cochez la case en regard de **Se connecter et lire le profil utilisateur** et **Lire les données de l’annuaire**. Enregistrez les mises à jour lorsque vous avez terminé.

   ![Définir des autorisations d’application pour Microsoft Graph](media/aad-integration/delegated-permissions.png)

   Sélectionnez **Terminé**.

7. Choisissez *Microsoft Graph* dans la liste des API, puis sélectionnez **Accorder des autorisations**. Cette étape échouera si le compte actuel n’est pas un administrateur de locataire.

   ![Définir des autorisations d’application pour Microsoft Graph](media/aad-integration/grant-permissions.png)

   Lorsque les autorisations sont accordées avec succès, la notification suivante s’affiche sur le portail :

   ![Notification de réussite des autorisations accordées](media/aad-integration/permissions-granted.png)

8. Revenez à l’application et notez l’**ID de l’application**. Lors du déploiement d’un cluster AKS Azure AD, cette valeur est appelée `Server application ID`.

   ![Obtenir l’ID de l’application](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Créer une application cliente

La deuxième application Azure AD est utilisée lors de la connexion avec la CLI Kubernetes (kubectl).

1. Sélectionnez **Azure Active Directory** > **Inscriptions des applications** > **Nouvelle inscription d’application**.

   Nommez l’application, sélectionnez **Native** pour le type d’application et entrez une valeur au format URI pour l’**URL de redirection**. Sélectionnez **Créer** lorsque vous avez terminé.

   ![Créer un enregistrement AAD](media/aad-integration/app-registration-client.png)

2. À partir de l’application Azure AD, sélectionnez **Paramètres** > **Autorisations requises** > **Ajouter** > **Sélectionner une API** et recherchez le nom de l’application serveur créée lors de la dernière étape de ce document.

   ![Configurer des autorisations de l’application](media/aad-integration/select-api.png)

3. Cochez la case en regard de l’application et cliquez sur **Sélectionner**.

   ![Sélectionnez le point de terminaison de l’application serveur AKS AAD](media/aad-integration/select-server-app.png)

   Sélectionnez **Terminé**

4. Sélectionnez l’API de votre serveur dans la liste, puis choisissez **Accorder des autorisations** :

   ![Accorder des autorisations](media/aad-integration/grant-permissions-client.png)

5. Revenez à l’application AD et notez l’**ID de l’application**. Lors du déploiement d’un cluster AKS Azure AD, cette valeur est appelée `Client application ID`.

   ![Récupérer l’ID de l’application](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Obtenir l’ID de locataire

Enfin, récupérez l’ID de votre locataire Azure. Cette valeur est également utilisée lors du déploiement du cluster AKS.

Dans le portail Azure, sélectionnez **Azure Active Directory** > **Propriétés** et notez l’**ID de l’annuaire**. Lors du déploiement d’un cluster AKS Azure AD, cette valeur est appelée `Tenant ID`.

![Récupérer l’ID de locataire Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Déployer un cluster

Utilisez la commande [az group create][az-group-create] pour créer un groupe de ressources pour le cluster AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Déployer le cluster à l’aide de la commande [az aks create][az-aks-create]. Remplacez les valeurs de l’exemple de commande ci-dessous par les valeurs collectées lors de la création des applications Azure AD.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Créer une liaison RBAC

Avant de pouvoir utiliser un compte Azure Active Directory avec le cluster AKS, une liaison de rôle ou une liaison de rôle de cluster doit être créée. Les *rôles*définissent les permissions à accorder, et les *liaisons*les appliquent aux utilisateurs souhaités. Ces affectations peuvent s’appliquer à un espace de noms donné ou à l’ensemble du cluster. Pour plus d’informations, consultez [Utilisation de l’autorisation RBAC][rbac-authorization].

Tout d’abord, utilisez la commande [az aks get-credentials][az-aks-get-credentials] avec l’argument `--admin` pour vous connecter au cluster avec un accès administrateur.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ensuite, utilisez le manifeste suivant pour créer un ClusterRoleBinding pour un compte Azure AD. Cet exemple donne au compte un accès complet à tous les espaces de noms du cluster. 

Obtenir le *objectId* de l’utilisateur requis compte à l’aide du [show d’utilisateur az ad] [ az-ad-user-show] commande. Fournir le nom d’utilisateur principal (UPN) du compte requis :

```azurecli-interactive
az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
```

Créez un fichier, tel que *rbac-aad-user.yaml*, et collez le contenu suivant. Mettre à jour le nom d’utilisateur avec l’ID d’objet de votre compte d’utilisateur à partir d’Azure AD obtenu à l’étape précédente :

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
  name: "947026ec-9463-4193-c08d-4c516e1f9f52"
```

Appliquez la liaison avec la commande [kubectl apply][kubectl-apply], comme indiqué dans l’exemple suivant :

```console
kubectl apply -f rbac-aad-user.yaml
```

Une liaison de rôle peut également être créée pour tous les membres d’un groupe d’Azure AD. Les groupes Azure AD sont spécifiés par ID d’objet de groupe, comme illustré dans l’exemple suivant. Créez un fichier, tel que *rbac-aad-group.yaml*, et collez le contenu suivant. Mettez à jour l’ID d’objet de groupe à partir de votre locataire Azure AD :

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Appliquez la liaison avec la commande [kubectl apply][kubectl-apply], comme indiqué dans l’exemple suivant :

```console
kubectl apply -f rbac-aad-group.yaml
```

Pour plus d’informations sur la sécurisation d’un cluster Kubernetes avec RBAC, consultez [Using RBAC Authorization][rbac-authorization](Utilisation de l’autorisation RBAC).

## <a name="access-cluster-with-azure-ad"></a>Accéder au cluster avec Azure AD

Ensuite, extrayez le contexte de l’utilisateur non administrateur à l’aide de la commande [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Une fois la commande exécutée, vous êtes invité à vous authentifier avec Azure. Suivez les instructions à l’écran.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

Une fois terminé, le jeton d’authentification est mis en cache. Vous êtes de nouveau invité à vous connecter une fois que le jeton a expiré ou que le fichier de configuration Kubernetes a été recréé.

Si un message d’erreur d’autorisation apparaît après connexion, vérifiez ce qui suit :
1. La session n’est pas ouverte en tant qu’invité dans l’instance Azure AD (c’est souvent le cas avec les connexions fédérées provenant d’un autre répertoire).
2. L'utilisateur n’est pas membre de plus de 200 groupes.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la sécurisation des clusters Kubernetes avec RBAC avec la documentation [Using RBAC Authorization][rbac-authorization] (Utilisation de l’autorisation RBAC).

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
