---
title: Intégrer Azure Active Directory dans Azure Kubernetes Service
description: Comment créer des clusters Azure Kubernetes Service compatibles avec Azure Active Directory.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e75577ae917cbe14a123ff5e2d44da2edc8062ef
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38307311"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Intégrer Azure Active Directory dans AKS

Azure Kubernetes Service (AKS) peut être configuré pour utiliser Azure Active Directory pour l’authentification utilisateur. Dans cette configuration, vous pouvez vous connecter à un cluster Azure Kubernetes Service à l’aide de votre jeton d’authentification Azure Active Directory. En outre, les administrateurs de cluster sont en mesure de configurer le contrôle d’accès en fonction du rôle (RBAC) Kubernetes basé sur une identité utilisateur ou l’appartenance à un groupe de répertoires.

Ce document décrit en détail la création de toutes les conditions préalables requises pour AKS et Azure AD, le déploiement d’un cluster compatible Azure AD et la création d’un rôle RBAC simple dans le cluster AKS. Sachez qu’il n’est pas possible, à l’heure actuelle, d’ajouter le contrôle RBAC à des clusters AKS qui n’en disposent pas en les mettant à jour.

> [!IMPORTANT]
> L’intégration du contrôle d’accès en fonction du rôle (RBAC) Azure Kubernetes Service (AKS) et d’Azure AD est actuellement en **préversion**. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.
>

## <a name="authentication-details"></a>Informations sur l’authentification

L’authentification Azure AD est fournie aux clusters Azure Kubernetes à l’aide d’OpenID Connect. OpenID Connect est une couche d’identité basée sur le protocole OAuth 2.0. Vous trouverez plus d’informations sur OpenID Connect dans la documentation [Open ID connect][open-id-connect].

Depuis le cluster Kubernetes, l’authentification par jeton de Webhook est utilisée pour vérifier les jetons d’authentification. L’authentification par jeton de Webhook est configurée et gérée en tant que partie du cluster AKS. Vous trouvez plus d’informations sur l’authentification par jeton de Webhook dans la [documentation relative à l’authentification de webhook][kubernetes-webhook].

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

7. Sélectionnez **Terminé**, choisissez *Microsoft Graph* dans la liste des API, puis sélectionnez **Accorder des autorisations**. Cette étape échouera si le compte actuel n’est pas un administrateur de locataire.

  ![Définir des autorisations d’application pour Microsoft Graph](media/aad-integration/grant-permissions.png)

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

4. Sélectionnez **Terminé** et **Accorder des autorisations** pour effectuer cette étape.

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
az group create --name myAKSCluster --location eastus
```

Déployer le cluster à l’aide de la commande [az aks create][az-aks-create]. Remplacez les valeurs de l’exemple de commande ci-dessous par les valeurs collectées lors de la création des applications Azure AD.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Créer une liaison RBAC

Avant de pouvoir utiliser un compte Azure Active Directory avec le cluster AKS, une liaison de rôle ou une liaison de rôle de cluster doit être créée.

Tout d’abord, utilisez la commande [az aks get-credentials][az-aks-get-credentials] avec l’argument `--admin` pour vous connecter au cluster avec un accès administrateur.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Ensuite, utilisez le manifeste suivant pour créer un ClusterRoleBinding pour un compte Azure AD. Mettez à jour le nom d’utilisateur à partir de votre locataire Azure AD. Cet exemple donne au compte un accès complet à tous les espaces de noms du cluster.

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
  name: "user@contoso.com"
```

Une liaison de rôle peut également être créée pour tous les membres d’un groupe d’Azure AD. Les groupes Azure AD sont spécifiés par ID objet de groupe.

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

Pour plus d’informations sur la sécurisation d’un cluster Kubernetes avec RBAC, consultez [Using RBAC Authorization][rbac-authorization](Utilisation de l’autorisation RBAC).

## <a name="access-cluster-with-azure-ad"></a>Accéder au cluster avec Azure AD

Ensuite, extrayez le contexte de l’utilisateur non administrateur à l’aide de la commande [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

Une fois la commande exécutée, vous êtes invité à vous authentifier avec Azure. Suivez les instructions à l’écran.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

Une fois terminé, le jeton d’authentification est mis en cache. Vous êtes de nouveau invité à vous connecter une fois que le jeton a expiré ou que le fichier de configuration Kubernetes a été recréé.

Si un message d’erreur d’autorisation apparaît après connexion, vérifiez que la session n’est pas ouverte en tant qu’invité dans Azure AD (c’est souvent le cas avec les connexions fédérées provenant d’un autre répertoire).
```console
error: You must be logged in to the server (Unauthorized)
```


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la sécurisation des clusters Kubernetes avec RBAC avec la documentation [Using RBAC Authorization][rbac-authorization] (Utilisation de l’autorisation RBAC).

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md
