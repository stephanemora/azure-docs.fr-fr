---
title: Intégrer Azure Active Directory dans Azure Kubernetes Service
description: Comment créer des clusters AKS (Azure Kubernetes Service) compatibles avec Azure Active Directory
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: 0476acadf5af3a3e2c470fe6c08ebbd355653e22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596587"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Intégrer Azure Active Directory dans Azure Kubernetes Service

AKS (Azure Kubernetes Service) peut être configuré pour utiliser Azure Active Directory (Azure AD) pour l’authentification utilisateur. Dans cette configuration, vous vous connectez à un cluster AKS en utilisant votre jeton d’authentification Azure AD.

Les administrateurs de cluster peuvent configurer le contrôle d’accès en fonction du rôle (RBAC) Kubernetes en se basant sur l’identité ou l’appartenance à un groupe d’annuaires de l’utilisateur.

Cet article explique comment :

- Déployer les prérequis pour AKS et Azure AD.
- Déployer un cluster AKS compatible avec Azure AD.
- Créer un rôle RBAC de base dans le cluster AKS à partir du portail Azure.

Vous pouvez aussi effectuer ces étapes à partir [d’Azure CLI][azure-ad-cli].

> [!NOTE]
> Azure AD ne peut être activé que lorsque vous créez un cluster prenant en charge RBAC. Vous ne pouvez pas activer Azure AD sur un cluster AKS existant.

## <a name="authentication-details"></a>Informations sur l’authentification

L’authentification Azure AD est fournie aux clusters AKS qui disposent d’OpenID Connect. OpenID Connect est une couche d’identité basée sur le protocole OAuth 2.0.

Pour plus d’informations sur OpenID Connect, consultez [Autoriser l’accès aux applications web à l’aide d’OpenID Connect et d’Azure AD][open-id-connect].

Au sein d’un cluster Kubernetes, l’authentification par jeton de Webhook est utilisée pour les jetons d’authentification. L’authentification par jeton de Webhook est configurée et gérée en tant que partie du cluster AKS.

Pour plus d’informations sur l’authentification par jeton de Webhook, consultez la section relative à [l’authentification par jeton de Webhook][kubernetes-webhook] dans la documentation Kubernetes.

Pour fournir l’authentification Azure AD à un cluster AKS, deux applications Azure AD sont créées. La première application est un composant serveur qui fournit l’authentification utilisateur. La deuxième application est un composant client qui est utilisé quand l’interface CLI vous invite à vous authentifier. Cette application cliente utilise l’application serveur pour l’authentification réelle des informations d’identification fournies par le client.

> [!NOTE]
> Quand vous configurez l’authentification Azure AD pour AKS, deux applications Azure AD sont configurées. La procédure de délégation d’autorisations pour chaque application doit être effectuée par un administrateur de locataire Azure.

## <a name="create-the-server-application"></a>Créer l’application serveur

La première application Azure AD est appliquée pour obtenir l’appartenance à un groupe Azure AD de l’utilisateur. Pour créer cette application sur le portail Azure :

1. Sélectionnez **Azure Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.

    a. Donnez un nom à l’application, par exemple *AKSAzureADServer*.

    b. Pour les **Types de comptes pris en charge**, sélectionnez **Comptes dans cet annuaire organisationnel**.
    
    c. Choisissez **Web** pour le type d’URI de redirection, puis entrez une valeur au format URI, par exemple *https://aksazureadserver* .

    d. Sélectionnez **Inscrire** quand vous avez terminé.

2. Sélectionnez **Manifeste**, puis modifiez la valeur de **groupMembershipClaims:** en choisissant **All**. Une fois les mises à jour terminées, sélectionnez **Enregistrer**.

    ![Mettre à jour l’appartenance à un groupe pour l’appliquer à tous](media/aad-integration/edit-manifest.png)

3. Dans le volet gauche de l’application Azure AD, sélectionnez **Certificats et secrets**.

    a. Sélectionnez **+ Nouveau secret client**.

    b. Ajoutez une description de clé, comme *AKS Azure AD server*. Choisissez une date/heure d’expiration, puis sélectionnez **Ajouter**.

    c. Notez la valeur de clé, qui ne s’affiche qu’à cet instant. Quand vous déployez un cluster AKS compatible avec Azure AD, cette valeur est appelée « secret d’application serveur ».

4. Dans le volet gauche de l’application Azure AD, sélectionnez **API autorisées**, puis **+ Ajouter une autorisation**.

    a. Sous **API Microsoft**, sélectionnez **Microsoft Graph**.

    b. Sélectionnez **Autorisations déléguées**, puis cochez la case en regard de **Annuaire > Directory.Read.All (Lire les données de l’annuaire)** .

    c. S’il n’existe pas d’autorisation déléguée par défaut pour **Utilisateur > User.Read (Connecter et lire le profil utilisateur)** , cochez la case en regard.

    d. Sélectionnez **Autorisations de l’application**, puis cochez la case en regard de **Annuaire > Directory.Read.All (Lire les données de l’annuaire)** .

    ![Définir les autorisations Graph](media/aad-integration/graph-permissions.png)

    e. Sélectionnez **Ajouter des autorisations** pour enregistrer les mises à jour.

    f. Sous **Donner son consentement**, sélectionnez **Accorder le consentement administrateur**. Ce bouton est indisponible car le compte actuellement utilisé n'est pas répertorié en tant qu'administrateur de locataire.

    L’affichage de la notification suivante sur le portail indique que les autorisations ont bien été octroyées :

   ![Notification de réussite des autorisations accordées](media/aad-integration/permissions-granted.png)

5. Dans le volet gauche de l’application Azure AD, sélectionnez **Exposer une API**, puis **+ Ajouter une étendue**.
    
    a. Entrez le **Nom de l’étendue**, le **Nom d’affichage du consentement administrateur** , puis une **Description du consentement administrateur**  comme *AKSAzureADServer*.

    b. Vérifiez que l’**État** est défini sur **Activé**.

    ![Exposer l’application serveur en tant qu’API pour pouvoir l’utiliser avec d’autres services](media/aad-integration/expose-api.png)

    c. Sélectionnez **Ajouter une étendue**.

6. Revenez à la page **Vue d’ensemble** de l’application et notez l’**ID d’application (client)** . Quand vous déployez un cluster AKS compatible avec Azure AD, cette valeur est appelée « ID d’application serveur ».

    ![Obtenir l’ID de l’application](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Créer l’application cliente

La deuxième application Azure AD est utilisée au moment de se connecter avec l’interface CLI Kubernetes (kubectl).

1. Sélectionnez **Azure Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.

    a. Donnez un nom à l’application, par exemple *AKSAzureADClient*.

    b. Pour les **Types de comptes pris en charge**, sélectionnez **Comptes dans cet annuaire organisationnel**.

    c. Sélectionnez **Web** pour le type d’URI de redirection, puis entrez une valeur au format URI comme *https://aksazureadclient* .

    >[!NOTE]
    >Si vous créez un cluster RBAC pour prendre en charge Azure Monitor pour conteneurs, ajoutez les deux URL de redirection supplémentaires suivantes à cette liste en tant que types d’application **web**. La première valeur de l’URL de base doit être `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` et la deuxième valeur de l’URL de base doit être `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
    >
    >Si vous utilisez cette fonctionnalité dans Azure Chine, la première valeur de l’URL de base doit être `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`, et la seconde `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
    >
    >Pour plus d’informations, consultez [Comment configurer la fonctionnalité Live Data (préversion)](../azure-monitor/insights/container-insights-livedata-setup.md) pour Azure Monitor pour conteneurs, ainsi que les étapes de configuration de l’authentification dans la section [Configurer l’authentification intégrée à Azure AD](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication).

    d. Sélectionnez **Inscrire** quand vous avez terminé.

2. Dans le volet gauche de l’application Azure AD, sélectionnez **API autorisées**, puis **+ Ajouter une autorisation**.

    a. Sélectionnez **Mes API**, puis choisissez votre application serveur Azure AD créée à l’étape précédente, à savoir *AKSAzureADServer*.

    b. Sélectionnez **Autorisations déléguées**, puis cochez la case en regard de votre application serveur Azure AD.

    ![Configurer des autorisations de l’application](media/aad-integration/select-api.png)

    c. Sélectionnez **Ajouter des autorisations**.

    d. Sous **Donner son consentement**, sélectionnez **Accorder le consentement administrateur**. Ce bouton n’est pas disponible si le compte actif n’est pas administrateur de locataire. L’affichage de la notification suivante sur le portail indique que les autorisations ont été octroyées :

    ![Notification de réussite des autorisations accordées](media/aad-integration/permissions-granted.png)

3. Dans le volet gauche de l’application Azure AD, sélectionnez **Authentification**.

    - Sous **Type de client par défaut**, sélectionnez **Oui** pour **Considérer le client comme un client public**.

5. Dans le volet gauche de l’application Azure AD, notez l’ID de l’application. Quand vous déployez un cluster AKS compatible avec Azure AD, cette valeur est appelée « ID d’application cliente ».

   ![Récupérer l’ID de l’application](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Obtenir l’ID de locataire

Enfin, obtenez l’ID de votre locataire Azure. Cette valeur est utilisée au moment de créer le cluster AKS.

À partir du portail Azure, sélectionnez **Azure Active Directory** > **Propriétés** et notez l’**ID de répertoire**. Quand vous créez un cluster AKS compatible avec Azure AD, cette valeur est appelée « ID de locataire ».

![Récupérer l’ID de locataire Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Déployer le cluster AKS

Utilisez la commande [az group create][az-group-create] pour créer un groupe de ressources pour le cluster AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Utilisez la commande [az aks create][az-aks-create] pour déployer le cluster AKS. Ensuite, remplacez les valeurs dans l’exemple de commande suivant. Utilisez les valeurs d’ID d’application serveur, d’ID d’application cliente et d’ID de locataire que vous avez recueillies au moment de créer les applications Azure AD.

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

La création d’un cluster AKS ne prend que quelques minutes.

## <a name="create-an-rbac-binding"></a>Créer une liaison RBAC

> [!NOTE]
> Le nom de liaison du rôle de cluster respecte la casse.

Avant d’utiliser un compte Azure Active Directory avec un cluster AKS, vous devez créer une liaison de rôle ou une liaison de rôle de cluster. Les rôles définissent les permissions à accorder, et les liaisons les appliquent aux utilisateurs souhaités. Ces affectations peuvent s’appliquer à un espace de noms donné ou à l’ensemble du cluster. Pour plus d’informations, consultez [Utilisation de l’autorisation RBAC][rbac-authorization].

Tout d’abord, utilisez la commande [az aks get-credentials][az-aks-get-credentials] avec `--admin`l’argument pour vous connecter au cluster avec un accès administrateur.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ensuite, créez ClusterRoleBinding pour le compte Azure AD auquel vous souhaitez accorder un accès au cluster AKS. L’exemple suivant donne au compte un accès complet à tous les espaces de noms du cluster :

- Si l’utilisateur auquel vous accordez la liaison RBAC se trouve dans le même locataire Azure AD, attribuez les autorisations en fonction du nom d’utilisateur principal (UPN). Passer à l’étape de création du manifeste YAML pour ClusterRoleBinding.

- Si l’utilisateur se trouve dans un autre locataire Azure AD, recherchez et utilisez plutôt la propriété **objectId**. Si nécessaire, procurez-vous l’objectId du compte d’utilisateur requis à l’aide de la commande [az ad user show][az-ad-user-show]. Indiquez le nom d’utilisateur principal (UPN) du compte en question :

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Créez un fichier, par exemple *rbac-aad-user.yaml*, puis collez le contenu suivant. À la dernière ligne, remplacez **userPrincipalName_or_objectId** par l’UPN ou l’ID d’objet, selon que l’utilisateur est le même locataire Azure AD ou pas.

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

Appliquez la liaison en utilisant la commande [kubectl apply][kubectl-apply], comme dans l’exemple suivant :

```console
kubectl apply -f rbac-aad-user.yaml
```

Une liaison de rôle peut également être créée pour tous les membres d’un groupe d’Azure AD. Les groupes Azure AD sont spécifiés à l’aide de l’ID d’objet de groupe, comme dans l’exemple suivant.

Créez un fichier, par exemple *rbac-aad-group.yaml*, puis collez le contenu suivant. Mettez à jour l’ID d’objet de groupe à partir de votre locataire Azure AD :

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

Appliquez la liaison en utilisant la commande [kubectl apply][kubectl-apply], comme dans l’exemple suivant :

```console
kubectl apply -f rbac-aad-group.yaml
```

Pour plus d’informations sur la sécurisation d’un cluster Kubernetes avec RBAC, consultez [Utilisation de l’autorisation RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Accéder au cluster avec Azure AD

Extrayez le contexte de l’utilisateur non administrateur en utilisant la commande [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Après avoir exécuté la commande `kubectl`, vous êtes invité à vous authentifier en utilisant Azure. Suivez les instructions à l’écran pour terminer le processus, comme illustré dans l’exemple suivant :

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Une fois le processus terminé, le jeton d’authentification est mis en cache. Vous n’êtes invité à vous reconnecter qu’une fois le jeton arrivé à expiration ou après que le fichier de configuration Kubernetes a été recréé.

Si vous voyez un message d’erreur d’autorisation après vous être connecté, vérifiez les critères suivants :

```console
error: You must be logged in to the server (Unauthorized)
```


- Vous avez défini l’ID d’objet ou l’UPN approprié, selon que le compte d’utilisateur se trouve dans le même locataire Azure AD ou non.
- L’utilisateur n’est pas membre de plus de 200 groupes.
- Le secret défini dans l’inscription d’application pour le serveur correspond à la valeur configurée en utilisant `--aad-server-app-secret`.

## <a name="next-steps"></a>Étapes suivantes

Pour utiliser des utilisateurs et des groupes Azure AD afin de contrôler l’accès aux ressources de cluster, voir [contrôler l’accès aux ressources de cluster à l’aide d’un contrôle d’accès en fonction du rôle et d’identités Azure AD dans AKS][azure-ad-rbac].

Pour plus d’informations sur la sécurisation des clusters Kubernetes, consultez [Options d’accès et d’identité pour AKS][rbac-authorization].

Pour en savoir plus sur le contrôle des identités et des ressources, consultez [Meilleures pratiques en matière d’authentification et d’autorisation dans AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
