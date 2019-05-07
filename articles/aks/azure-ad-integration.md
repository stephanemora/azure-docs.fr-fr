---
title: Intégrer Azure Active Directory dans Azure Kubernetes Service
description: Comment créer des clusters Azure Kubernetes Service (AKS) compatibles avec Azure Active Directory.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: 2a218a48223c81e009b83cb1f129601a8035e18e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138531"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Intégrer Azure Active Directory dans Azure Kubernetes Service

Azure Kubernetes Service (AKS) peut être configuré pour utiliser Azure Active Directory (AD) pour l’authentification utilisateur. Dans cette configuration, connectez-vous à un cluster AKS à l’aide de votre jeton d’authentification Azure Active Directory. En outre, les administrateurs de cluster sont en mesure de configurer le contrôle d’accès en fonction du rôle Kubernetes (RBAC) selon l’appartenance au groupe d’un utilisateur identité ou un répertoire.

Cet article vous montre comment déployer les conditions préalables pour AKS et Azure AD, puis comment déployer un cluster AD activé Azure et créer un rôle RBAC de base dans le cluster AKS à l’aide du portail Azure. Vous pouvez également [terminer ces étapes à l’aide de l’interface CLI][azure-ad-cli].

Les limites suivantes s'appliquent :

- Azure AD ne peut être activé que quand vous créez un nouveau cluster prenant en charge RBAC. Vous ne pouvez pas activer Azure AD sur un cluster AKS existant.
- *Invité* utilisateurs dans Azure AD, telles que si vous utilisez une authentification fédérée dans à partir d’un autre répertoire, ne sont pas pris en charge.

## <a name="authentication-details"></a>Informations sur l’authentification

L’authentification Azure AD est fournie aux clusters AKS à l’aide d’OpenID Connect. OpenID Connect est une couche d’identité basée sur le protocole OAuth 2.0. Pour plus d’informations sur OpenID Connect, consultez la documentation [Open ID connect][open-id-connect].

Depuis le cluster Kubernetes, l’authentification par jeton de Webhook est utilisée pour vérifier les jetons d’authentification. L’authentification par jeton de Webhook est configurée et gérée en tant que partie du cluster AKS. Pour plus d’informations sur l’authentification par jeton Webhook, consultez la [documentation relative à l’authentification de webhook][kubernetes-webhook].

Pour fournir l’authentification Azure AD pour un cluster AKS, deux applications Azure AD sont créées. La première application est un composant de serveur qui fournit l’authentification utilisateur. La deuxième application est un composant client qui est utilisé lorsque vous y êtes invité par l’interface CLI pour l’authentification. Cette application cliente utilise l’application serveur pour l’authentification réelle, les informations d’identification fournies par le client.

> [!NOTE]
> Lors de la configuration d’Azure AD pour l’authentification AKS, deux applications Azure AD sont configurées. Les étapes permettant de déléguer des autorisations pour chacune des applications doivent être effectuées par un administrateur de locataire Azure.

## <a name="create-server-application"></a>Créer une application serveur

La première application Azure AD permet d’obtenir l’appartenance à un groupe d’utilisateurs Azure AD. Créer cette application dans le portail Azure.

1. Sélectionnez **Azure Active Directory** > **inscriptions** > **nouvelle inscription**.

    * Nommez l’application, tel que *AKSAzureADServer*.
    * Pour **pris en charge les types de comptes**, choisissez *comptes dans ce répertoire d’organisation uniquement*.
    * Choisissez *Web* pour le **URI de redirection** tapez, entrez n’importe quelle valeur de l’URI mis en forme tel que *https://aksazureadserver*.
    * Sélectionnez **inscrire** lorsque vous avez terminé.

1. Sélectionnez **Manifeste** et définissez la valeur `groupMembershipClaims` sur `"All"`.

    ![Mettre à jour l’appartenance à un groupe pour l’appliquer à tous](media/aad-integration/edit-manifest.png)

    **Enregistrer** les mises à jour une fois celle-ci terminée.

1. Dans le volet de navigation gauche de l’application Azure AD, sélectionnez **certificats et clés secrètes**.

    * Choisissez **+ nouvelle clé secrète client**.
    * Ajoutez une description de la clé, tel que *server d’ACS Azure AD*. Choisissez un délai d’expiration, puis sélectionnez **ajouter**.
    * Notez la valeur de la clé. Il a affiché uniquement cette durée initiale. Lorsque vous déployez un cluster Azure ACS AD activé, cette valeur est appelée le `Server application secret`.

1. Dans le volet de navigation gauche de l’application Azure AD, sélectionnez **autorisations d’API**, puis choisissez à **+ ajouter une autorisation**.

    * Sous **APIs Microsoft**, choisissez *Microsoft Graph*.
    * Choisissez **autorisations déléguées**, puis cochez la case à côté **Directory > Directory.Read.All (données d’annuaire en lecture)**.
        * Si une valeur par défaut délégué l’autorisation de **utilisateur > User.Read (se connecter et lire le profil utilisateur)** n’existe pas, cochez la case cette autorisation.
    * Choisissez **autorisations d’Application**, puis cochez la case à côté **Directory > Directory.Read.All (données d’annuaire en lecture)**.

        ![Définir des autorisations de graphique](media/aad-integration/graph-permissions.png)

    * Choisissez **ajouter des autorisations** pour enregistrer les mises à jour.

    * Sous le **donner leur consentement** , choisissez à **accorder le consentement de l’administrateur**. Ce bouton est grisé et qu’il n’est pas disponible si le compte actuel n’est pas un administrateur de locataire.

        Lorsque les autorisations sont accordées avec succès, la notification suivante s’affiche sur le portail :

        ![Notification de réussite des autorisations accordées](media/aad-integration/permissions-granted.png)

1. Dans le volet de navigation gauche de l’application Azure AD, sélectionnez **exposer une API**, puis choisissez à **+ ajouter une étendue**.
    
    * Définir un *Nom_Étendue*, *nom complet de consentement administrateur*, et *description du consentement administrateur*, tel que *AKSAzureADServer*.
    * Assurez-vous que le **état** a la valeur *activé*.

        ![Exposer l’application serveur en tant qu’API pour une utilisation avec d’autres services](media/aad-integration/expose-api.png)

    * Choisissez **ajouter une étendue**.

1. Retourner à l’application **vue d’ensemble** page et prenez note de la **ID d’Application (client)**. Lorsque vous déployez un cluster Azure ACS AD activé, cette valeur est appelée le `Server application ID`.

   ![Obtenir l’ID de l’application](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Créer une application cliente

La deuxième application Azure AD est utilisée lors de la connexion avec la CLI Kubernetes (`kubectl`).

1. Sélectionnez **Azure Active Directory** > **inscriptions** > **nouvelle inscription**.

    * Nommez l’application, tel que *AKSAzureADClient*.
    * Pour **pris en charge les types de comptes**, choisissez *comptes dans ce répertoire d’organisation uniquement*.
    * Choisissez *Web* pour le **URI de redirection** tapez, entrez n’importe quelle valeur de l’URI mis en forme tel que *https://aksazureadclient*.
    * Sélectionnez **inscrire** lorsque vous avez terminé.

1. Dans le volet de navigation gauche de l’application Azure AD, sélectionnez **autorisations d’API**, puis choisissez à **+ ajouter une autorisation**.

    * Sélectionnez **mes API**, puis choisissez votre application de serveur Azure AD créée à l’étape précédente, tel que *AKSAzureADServer*.
    * Choisissez **autorisations déléguées**, puis cochez la case en regard de votre application de serveur Azure AD.

        ![Configurer des autorisations de l’application](media/aad-integration/select-api.png)

    * Sélectionnez **ajouter des autorisations**.

    * Sous le **donner leur consentement** , choisissez à **accorder le consentement de l’administrateur**. Ce bouton est grisé et qu’il n’est pas disponible si le compte actuel n’est pas un administrateur de locataire.

        Lorsque les autorisations sont accordées avec succès, la notification suivante s’affiche sur le portail :

        ![Notification de réussite des autorisations accordées](media/aad-integration/permissions-granted.png)

1. Dans le volet de navigation gauche de l’application Azure AD, prenez note de la **ID d’Application**. Lors du déploiement d’un cluster AKS Azure AD, cette valeur est appelée `Client application ID`.

   ![Récupérer l’ID de l’application](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Obtenir l’ID de locataire

Enfin, récupérez l’ID de votre locataire Azure. Cette valeur est utilisée lorsque vous créez le cluster AKS.

Dans le portail Azure, sélectionnez **Azure Active Directory** > **Propriétés** et notez l’**ID de l’annuaire**. Lorsque vous créez un cluster Azure ACS AD activé, cette valeur est appelée le `Tenant ID`.

![Récupérer l’ID de locataire Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Déployer un cluster

Utilisez la commande [az group create][az-group-create] pour créer un groupe de ressources pour le cluster AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Déployer le cluster à l’aide de la commande [az aks create][az-aks-create]. Remplacez les valeurs dans l’exemple de commande ci-dessous avec les valeurs collectées lors de la création d’applications Azure AD pour l’ID d’application serveur secret, ID d’application cliente et ID de locataire :

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

Il prend quelques minutes pour créer le cluster AKS.

## <a name="create-rbac-binding"></a>Créer une liaison RBAC

Avant de pouvoir utiliser un compte Azure Active Directory avec le cluster AKS, une liaison de rôle ou une liaison de rôle de cluster doit être créée. Les *rôles*définissent les permissions à accorder, et les *liaisons*les appliquent aux utilisateurs souhaités. Ces affectations peuvent s’appliquer à un espace de noms donné ou à l’ensemble du cluster. Pour plus d’informations, consultez [Utilisation de l’autorisation RBAC][rbac-authorization].

Tout d’abord, utilisez le [az aks get-credentials] [ az-aks-get-credentials] commande avec le `--admin` argument pour vous connecter au cluster avec un accès administrateur.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ensuite, créez un ClusterRoleBinding pour un compte Azure AD que vous souhaitez accorder l’accès au cluster AKS. L’exemple suivant donne le compte d’un accès complet à tous les espaces de noms dans le cluster.

- Si vous accordez à l’utilisateur que la liaison de RBAC pour est dans le même locataire Azure AD, affecter des autorisations en fonction du nom d’utilisateur principal (UPN). Passer à l’étape pour créer le manifeste YAML pour le ClusterRuleBinding.

- Si l’utilisateur est dans un autre annuaire Azure AD locataire, rechercher et utiliser le *objectId* propriété à la place. Si nécessaire, obtenez le *objectId* de l’utilisateur requis compte à l’aide du [show d’utilisateur az ad] [ az-ad-user-show] commande. Fournir le nom d’utilisateur principal (UPN) du compte requis :

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Créez un fichier, tel que *rbac-aad-user.yaml*, et collez le contenu suivant. Sur la dernière ligne, remplacez *userPrincipalName_or_objectId* avec l’ID d’objet ou UPN selon que l’utilisateur est le même locataire Azure AD ou non.

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

Après avoir exécuté un `kubectl` commande, vous êtes invité à s’authentifier avec Azure. Suivez l’à l’écran des instructions pour terminer le processus, comme illustré dans l’exemple suivant :

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Lorsque vous avez terminé, le jeton d’authentification est mis en cache. Vous sont uniquement différentes pour vous connecter lorsque le jeton a expiré ou le fichier de configuration Kubernetes recréé.

Si un message d’erreur d’autorisation apparaît après connexion, vérifiez ce qui suit :
1. L’utilisateur vous vous connectez comme n’est pas un invité dans l’instance d’Azure AD (ce scénario est souvent le cas si vous utilisez un compte fédéré à partir d’un répertoire différent).
2. L'utilisateur n’est pas membre de plus de 200 groupes.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Étapes suivantes

Pour utiliser des groupes et utilisateurs Azure AD pour contrôler l’accès aux ressources de cluster, consultez [contrôler l’accès aux ressources de cluster à l’aide de contrôle d’accès en fonction du rôle et des identités Azure AD dans ACS][azure-ad-rbac].

Pour plus d’informations sur la sécurisation des clusters Kubernetes, consultez [options d’accès et d’identité pour AKS)][rbac-authorization].

Pour obtenir des recommandations sur le contrôle des identités et des ressources, consultez [meilleures pratiques pour l’authentification et autorisation dans AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
