---
title: Azure RBAC pour clusters Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Utiliser Azure RBAC pour les contrôles d’autorisation sur les clusters Kubernetes avec Azure Arc.
ms.openlocfilehash: b4c8d6f4f7abcd9090a0b4aaa69038b75a4aa1b1
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110479596"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Intégrer Azure Active Directory à des clusters Kubernetes avec Azure Arc

Les types d’objets Kubernetes [ClusterRoleBinding et RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) permettent de définir l’autorisation dans Kubernetes en mode natif. Cette fonctionnalité vous permet d'utiliser Azure Active Directory (Azure AD) et les attributions de rôles Azure pour contrôler les vérifications des autorisations sur le cluster. Cela implique que vous pouvez désormais utiliser les attributions de rôles Azure pour contrôler de manière précise qui peut lire, écrire et supprimer des objets Kubernetes comme deployment, pod et service.

Une vue d’ensemble conceptuelle de cette fonctionnalité est disponible dans l’article [Azure RBAC sur Kubernetes avec Azure Arc](conceptual-azure-rbac.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis

- [Installez ou mettez à jour Azure CLI](/cli/azure/install-azure-cli) vers la version 2.16.0 ou une version ultérieure.

- Installez l’extension Azure CLI `connectedk8s` version 1.1.0 ou ultérieure :

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    Si l'extension `connectedk8s` est déjà installée, vous pouvez la mettre à jour vers la version la plus récente à l'aide de la commande suivante : 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Connectez un cluster Kubernetes avec Azure Arc existant :
    - Si vous n’avez pas encore connecté de cluster, utilisez notre [Guide de démarrage rapide](quickstart-connect-cluster.md).
    - [Mettez à niveau vos agents](agent-upgrade.md#manually-upgrade-agents) vers la version 1.1.0 ou une version ultérieure.

> [!NOTE]
> Vous ne pouvez pas configurer cette fonctionnalité pour les offres Kubernetes managées de fournisseurs cloud comme Elastic Kubernetes Service ou Google Kubernetes Engine où l’utilisateur n’a pas d’accès au serveur API du cluster. Pour les clusters Azure Kubernetes service (AKS), cette [fonctionnalité est disponible en mode natif](../../aks/manage-azure-rbac.md) et ne nécessite pas que le cluster AKS soit connecté à Azure Arc.

## <a name="set-up-azure-ad-applications"></a>Configurer les applications Azure AD

### <a name="create-a-server-application"></a>Créer une application serveur

1. Créez une nouvelle application Azure AD et récupérez sa valeur `appId`. Cette valeur est utilisée dans les étapes ultérieures comme `serverApplicationId`.

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. Mettez à jour les revendications d’appartenance au groupe d’applications :

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. Créez un principal de service et récupérez sa valeur de champ `password`. Cette valeur est requise ultérieurement en tant que `serverApplicationSecret` quand vous activez cette fonctionnalité sur le cluster.

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. Accordez à l’application les autorisations de l’API :

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > Un administrateur de locataires Azure doit exécuter cette étape.
    > 
    > Pour l’utilisation de cette fonctionnalité en production, nous recommandons de créer une application serveur différente pour chaque cluster.

### <a name="create-a-client-application"></a>Créer une application cliente

1. Créez une nouvelle application Azure AD et récupérez sa valeur `appId`. Cette valeur est utilisée dans les étapes ultérieures comme `clientApplicationId`.

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. Créez un principal de service pour l’application cliente :

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. Obtenez la valeur `oAuthPermissionId` pour l’application serveur :

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. Accordez les autorisations requises pour l’application cliente :

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>Créez une attribution de rôle pour l’application serveur

L’application serveur a besoin des autorisations `Microsoft.Authorization/*/read` pour vérifier si l’utilisateur qui effectue la demande est autorisé sur les objets Kubernetes qui font partie de la requête.

1. Créez un fichier nommé *accessCheck.json* avec les éléments suivants :

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    Remplacez `<subscription-id>` par l’ID d’abonnement réel.

2. Exécutez la commande suivante pour créer le nouveau rôle personnalisé :

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. À partir de la sortie de la commande précédente, stockez la valeur du champ `id`. Ce champ est utilisé dans les étapes ultérieures comme `roleId`.

4. Créez une attribution de rôle sur l’application serveur en tant que `assignee` à l’aide du rôle que vous avez créé :

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-the-cluster"></a>Activer Azure RBAC sur le cluster

Activez le contrôle d’accès en fonction du rôle d’Azure (RBAC) sur votre cluster Kubernetes avec Arc en exécutant la commande suivante :

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
```
    
> [!NOTE]
> Avant d’exécuter la commande précédente, assurez-vous que le fichier `kubeconfig` sur l’ordinateur pointe sur le cluster sur lequel vous allez activer la fonctionnalité Azure RBAC.
>
> Utilisez `--skip-azure-rbac-list` avec la commande précédente pour obtenir une liste séparée par des virgules de noms d’utilisateur, adresses de messagerie, et connexions OpenID en cours de vérification des autorisations à l’aide des objets Kubernetes natifs `ClusterRoleBinding` et `RoleBinding` à la place d’Azure RBAC.

### <a name="generic-cluster-where-no-reconciler-is-running-on-the-apiserver-specification"></a>Cluster générique dans lequel aucun réconciliateur n’est en cours d’exécution sur la spécification apiserver

1. Utilisez SSH dans chaque nœud principal du cluster et suivez les étapes suivantes :

    1. Ouvrez le manifeste `apiserver` en mode édition :
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. Utilisez la spécification suivante sous `volumes` :
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. Ajoutez la spécification suivante sous `volumeMounts` :

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. Ajoutez les arguments `apiserver` suivants :

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Si le cluster Kubernetes est de version 1.19.0 ou ultérieure, vous devez également définir l'argument `apiserver` suivant :

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Enregistrez et fermez l’éditeur pour mettre à jour le pod `apiserver`.


### <a name="cluster-created-by-using-cluster-api"></a>Cluster créé à l’aide de l’API Cluster

1. Copiez le secret de protection qui contient les fichiers de configuration webhook d’authentification et d’autorisation à partir du cluster de charge de travail sur votre ordinateur :

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. Remplacez le champ `namespace` du fichier *azure-arc-guard-manifests.yaml* par l’espace de noms dans le cluster de gestion dans lequel vous appliquez les ressources personnalisées pour la création de clusters de charge de travail.

1. Appliquez ce manifeste :

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Modifiez l’objet `KubeadmControlPlane` en exécutant `kubectl edit kcp <clustername>-control-plane` :
    
    1. Ajoutez l’extrait de code suivant sous `files` :
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. Ajoutez l’extrait de code suivant sous `apiServer` > `extraVolumes` :
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. Ajoutez l’extrait de code suivant sous `apiServer` > `extraArgs` :
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Enregistrez et fermez pour mettre à jour l’objet `KubeadmControlPlane`. Attendez que ces modifications apparaissent sur le cluster de charge de travail.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Créer des attributions de rôles pour permettre aux utilisateurs d’accéder au cluster

Les propriétaires de la ressource Kubernetes avec Azure Arc peuvent utiliser les rôles intégrés ou des rôles personnalisés pour accorder à d’autres utilisateurs l’accès au cluster Kubernetes.

### <a name="built-in-roles"></a>Rôles intégrés

| Role | Description |
|---|---|
| [Visionneuse Kubernetes Azure Arc](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | Autorise l’accès en lecture seule pour voir la plupart des objets dans un espace de noms. Ce rôle n’autorise pas l’affichage des secrets. C’est parce qu’une autorisation `read` sur les secrets donnerait accès aux informations de connexion `ServiceAccount` dans l’espace de noms. Ces informations d’identification autorisent à leur tour l’accès à l’API par le biais de cette valeur `ServiceAccount` (une forme d’élévation des privilèges). |
| [Enregistreur Kubernetes Azure Arc](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | Autorise l’accès en lecture/écriture pour la plupart des objets dans un espace de noms. Ce rôle n’autorise pas l’affichage ni la modification des rôles et des liaisons de rôles. Cependant, ce rôle donne accès aux secrets et aux pods en exécution comme toute valeur `ServiceAccount` dans l’espace de noms. Afin qu’il puisse être utilisé pour obtenir les niveaux d'accès d’API de toute valeur `ServiceAccount` dans l’espace de noms. |
| [Administrateur Kubernetes Azure Arc](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | Autorise l’accès administrateur. C’est normalement accordé au sein d’un espace de noms via `RoleBinding`. Si vous l’utilisez dans `RoleBinding`, il autorise l’accès en lecture/écriture à la plupart des ressources dans un espace de noms, y compris la possibilité de créer des rôles et des liaisons de rôles dans l’espace de noms. Ce rôle n’autorise pas l’accès en écriture au quota de ressources ou à l’espace de noms lui-même. |
| [Administrateur de cluster Kubernetes Azure Arc](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | Autorise l’accès de super utilisateur qui permet d’exécuter n’importe quelle action sur toutes les ressources. Quand vous l’utilisez dans `ClusterRoleBinding`, il donne un contrôle total sur l’ensemble des ressources dans le cluster et dans tous les espaces de noms. Quand vous l’utilisez dans `RoleBinding`, il offre un contrôle total sur chaque ressource dans l’espace de noms de la liaison de rôle, y compris l’espace de noms lui-même.|

Vous pouvez créer des attributions de rôles limitées au cluster Kubernetes avec Arc dans le Portail Azure sur le panneau de **contrôle d'accès (IAM)** de la ressource de cluster. Vous pouvez également utiliser les commandes Azure CLI suivantes :

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

Dans ces commandes, `AZURE-AD-ENTITY-ID` peut être un nom d’utilisateur (par exemple, `testuser@mytenant.onmicrosoft.com`) ou même la valeur `appId` d’un principal de service.

Voici un autre exemple de création d’une attribution de rôle étendue à un espace de noms spécifique au sein du cluster :

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Vous pouvez créer des attributions de rôles étendues au cluster à l’aide du Portail Azure ou de l’Azure CLI, mais vous pouvez créer des attributions de rôles limitées à des espaces de noms à l’aide de l’interface CLI.

### <a name="custom-roles"></a>Rôles personnalisés

Vous pouvez choisir de créer votre propre définition de rôle pour utilisation dans les attributions de rôles.

Examinez en détail l’exemple suivant de définition de rôle qui permet à un utilisateur de lire uniquement les déploiements. Pour plus d’informations, consultez [la liste complète des actions de données que vous pouvez utiliser pour créer une définition de rôle](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes).

Copiez l’objet JSON ci-dessous dans un fichier appelé *custom-role.js*. Remplacez l’espace réservé `<subscription-id>` par l’ID réel de l’abonnement. Le rôle personnalisé utilise l’une des actions de données et vous permet de voir tous les déploiements dans l’étendue (cluster ou espace de noms) où l’attribution de rôle est créée.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. Créez la définition de rôle en exécutant la commande suivante à partir du dossier où vous avez enregistré *custom-role.json* :

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Créez une attribution de rôle à l’aide de cette définition de rôle personnalisée :

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Configurer kubectl avec les informations d’identification de l’utilisateur

Il existe deux manières d’accéder au fichier *kubeconfig* dont vous avez besoin pour accéder au cluster :

- Vous utilisez la fonctionnalité de [connexion de cluster](cluster-connect.md) (`az connectedk8s proxy`) du cluster Kubernetes avec Azure Arc.
- L’administrateur du cluster partage le fichier *kubeconfig* avec tous les autres utilisateurs.

### <a name="if-youre-accessing-the-cluster-by-using-the-cluster-connect-feature"></a>Si vous accédez au cluster à l’aide de la fonctionnalité de connexion au cluster

Exécutez la commande suivante pour démarrer le processus proxy :

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

Une fois le processus de proxy en cours d’exécution, vous pouvez ouvrir un autre onglet dans votre console pour [commencer à envoyer vos demandes au cluster](#send-requests-to-the-cluster).

### <a name="if-the-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Si l’administrateur de cluster a partagé le fichier kubeconfig avec vous 

1. Exécutez la commande suivante pour définir les informations d’identification de l’utilisateur :

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. Ouvrez le fichier *kubeconfig* que vous avez créé plus tôt. Sous `contexts`, vérifiez que le contexte associé aux points du cluster pointe vers les informations d’identification de l’utilisateur que vous avez créées à l’étape précédente.

1. Ajoutez le paramètre **config-mode** sous `user` > `config` :
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="send-requests-to-the-cluster"></a>Envoyez les requêtes au cluster

1. Exécutez une commande `kubectl`. Par exemple :
   * `kubectl get nodes` 
   * `kubectl get pods`

1. Après avoir été invité à fournir une authentification basée sur le navigateur, copiez l’URL de connexion de l’appareil (`https://microsoft.com/devicelogin`) et ouvrez votre navigateur web.

1. Entrez le code imprimé sur votre console. Copiez et collez le code sur votre terminal dans l’invite d’entrée d’authentification de l’appareil.

1. Entrez le nom d’utilisateur (`testuser@mytenant.onmicrosoft.com`) et le mot de passe associé.

1. Si vous voyez un message d’erreur semblable à celui-ci, cela signifie que vous n’êtes pas autorisé à accéder à la ressource demandée :

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    Un administrateur doit créer une nouvelle attribution de rôle qui autorise cet utilisateur à accéder à la ressource.

## <a name="use-conditional-access-with-azure-ad"></a>Utiliser l’accès conditionnel avec Azure AD

Lorsque vous intégrez Azure AD à votre cluster avec Kubernetes, vous pouvez également utiliser l’[accès conditionnel](../../active-directory/conditional-access/overview.md) pour contrôler l’accès à votre cluster.

> [!NOTE]
> L’accès conditionnel Azure AD est une fonctionnalité Azure AD Premium.

Pour créer un exemple de stratégie d’accès conditionnel à utiliser avec le cluster, procédez comme suit :

1. En haut du Portail Azure, recherchez et sélectionnez **Azure Active Directory**.
1. Dans le menu Azure Active Directory sur le côté gauche, sélectionnez **Applications d’entreprise**.
1. Dans le menu Applications d’entreprise sur le côté gauche, sélectionnez **Accès conditionnel**.
1. Sur le menu Accès conditionnel sur le côté gauche, sélectionnez **Stratégies** > **Nouvelle stratégie**.
    
    [ ![Capture d’écran montrant le bouton permettant d’ajouter une stratégie d’accès conditionnel.](./media/azure-rbac/conditional-access-new-policy.png) ](./media/azure-rbac/conditional-access-new-policy.png#lightbox)

1. Entrez le nom de la stratégie, par exemple **arc-k8s-policy**.
1. Sélectionnez **Utilisateurs et groupes**. Sous **Inclure**, choisissez **Sélectionner les utilisateurs et les groupes**. Choisissez ensuite les utilisateurs et les groupes auxquels vous souhaitez appliquer la stratégie. Pour cet exemple, choisissez le groupe Azure AD qui dispose déjà d’un accès administratif à votre cluster.

    [ ![Capture d’écran qui montre la sélection d’utilisateurs ou de groupes pour appliquer la stratégie d’accès conditionnel](./media/azure-rbac/conditional-access-users-groups.png) ](./media/azure-rbac/conditional-access-users-groups.png#lightbox)

1. Sélectionnez **Applications ou actions cloud**. Sous **Inclure**, choisissez **Sélectionner les applications**. Ensuite, recherchez et sélectionnez l’application serveur que vous avez créée précédemment.

    [ ![Capture d’écran qui montre la sélection d’une application serveur pour l’application de la stratégie d’accès conditionnel.](./media/azure-rbac/conditional-access-apps.png) ](./media/azure-rbac/conditional-access-apps.png#lightbox)

1. Sous **Contrôles d’accès**, sélectionnez **Accorder**. Sélectionnez **Accorder l’accès** > **Exiger que l’appareil soit marqué comme conforme**.

    [ ![Capture d’écran qui montre la sélection de l’option d’autoriser uniquement les appareils conformes pour la stratégie d’accès conditionnel.](./media/azure-rbac/conditional-access-grant-compliant.png) ](./media/azure-rbac/conditional-access-grant-compliant.png#lightbox)
    
1. Sous **Activer une stratégie**, sélectionnez **Activé** > **Créer**.

    [ ![Capture d’écran qui montre l’activation de la stratégie d’accès conditionnel.](./media/azure-rbac/conditional-access-enable-policies.png) ](./media/azure-rbac/conditional-access-enable-policies.png#lightbox)

Accédez à nouveau au cluster. Par exemple, en exécutant la commande `kubectl get nodes` pour afficher les nœuds du cluster :

```console
kubectl get nodes
```

Suivez les instructions pour vous reconnecter. Un message d’erreur s’affiche, indiquant que vous êtes correctement connecté, mais que votre administrateur requiert que l’appareil demandant que l’accès à la ressource soit géré par Azure AD. Procédez comme suit :

1. Dans le portail Azure, accédez à **Azure Active Directory**.
1. Sélectionnez **Applications d’entreprise**. Sous **Activité**, sélectionnez **Connexions**. 
1. Une entrée du haut indique **Échoué** pour le **Status** et **Succès** pour **l'accès conditionnel**. Sélectionnez l’entrée, puis **Accès conditionnel** dans **Détails**. Notez que votre stratégie d’accès conditionnel est indiquée.

   [ ![Capture d’écran montrant une entrée de connexion ayant échoué en raison de la stratégie d’accès conditionnel.](./media/azure-rbac/conditional-access-sign-in-activity.png) ](./media/azure-rbac/conditional-access-sign-in-activity.png#lightbox)

## <a name="configure-just-in-time-cluster-access-with-azure-ad"></a>Configurer un accès juste-à-temps au cluster avec Azure AD

Une autre option pour le contrôle d’accès aux clusters consiste à utiliser Privileged Identity Management (PIM) pour les requêtes juste-à-temps.

>[!NOTE]
> PIM est une fonctionnalité Azure AD Premium nécessitant une référence SKU P2 Premium. Pour plus d’informations sur les références SKU Azure AD, consultez le [Guide de tarification](https://azure.microsoft.com/pricing/details/active-directory/).

Pour configurer des demandes d’accès juste-à-temps pour votre cluster, procédez comme suit :

1. En haut du Portail Azure, recherchez et sélectionnez **Azure Active Directory**.
1. Notez l’ID du locataire. Pour le reste de ces instructions, nous faisons référence à cet ID avec `<tenant-id>`.

    [ ![Capture d’écran qui montre les détails du locataire Azure Active Directory.](./media/azure-rbac/jit-get-tenant-id.png) ](./media/azure-rbac/jit-get-tenant-id.png#lightbox)

1. Dans le menu d’Azure Active Directory sur le côté gauche, sous **Gérer**, sélectionnez **Groupes** > **Nouveau groupe**.

    [ ![Capture d’écran montrant les sélections pour la création d’un nouveau groupe.](./media/azure-rbac/jit-create-new-group.png) ](./media/azure-rbac/jit-create-new-group.png#lightbox)

1. Vérifiez que **Sécurité** est sélectionné pour **Type de groupe**.  Entrez un nom de groupe, comme **myJITGroup**. Sous **Rôles Azure AD pouvant être attribués à ce groupe (préversion)** , sélectionnez **Oui**. Pour finir, sélectionnez **Créer**.

    [ ![Capture d’écran qui affiche des détails sur le nouveau groupe.](./media/azure-rbac/jit-new-group-created.png) ](./media/azure-rbac/jit-new-group-created.png#lightbox)

1. Vous serez redirigé vers la page **Groupes**. Sélectionnez le groupe nouvellement créé et prenez note de l’ID d’objet. Pour le reste de ces instructions, nous faisons référence à cet ID avec `<object-id>`.

    [ ![Capture d’écran montrant l’identificateur d’objet du groupe créé.](./media/azure-rbac/jit-get-object-id.png) ](./media/azure-rbac/jit-get-object-id.png#lightbox)

1. De retour dans le Portail Azure, dans le menu de **Activité** sur le côté gauche, sélectionnez **Accès privilégié (préversion)** . Sélectionnez ensuite **Activer l’accès privilégié**.

    [ ![Capture d’écran montrant des sélections pour l’activation de l’accès privilégié.](./media/azure-rbac/jit-enabling-priv-access.png) ](./media/azure-rbac/jit-enabling-priv-access.png#lightbox)

1. Sélectionnez **Ajouter des affectations** pour commencer à accorder l’accès.

    [ ![Capture d’écran montrant le bouton permettant d’ajouter des affectations actives.](./media/azure-rbac/jit-add-active-assignment.png) ](./media/azure-rbac/jit-add-active-assignment.png#lightbox)

1. Sélectionnez un rôle de **Membre**, puis choisissez les utilisateurs et les groupes auxquels vous souhaitez accorder l’accès au cluster. Un administrateur de groupe peut modifier ces attributions à tout moment. Quand vous êtes prêt à continuer, choisissez **Suivant**.

    [ ![Capture d’écran illustrant l’ajout d’un récepteur.](./media/azure-rbac/jit-adding-assignment.png) ](./media/azure-rbac/jit-adding-assignment.png#lightbox)

1. Choisissez un type d’affectation **Actif**, la durée souhaitée et fournissez une justification. Lorsque vous êtes prêt à continuer, sélectionnez **Attribuer**. Pour plus d’informations sur les types d’affectations, consultez [Attribuer l’éligibilité à l’appartenance ou la propriété d’un groupe d’accès privilégié (préversion) dans Privileged Identity Management](../../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group).

    [ ![Capture d’écran montrant le choix des propriétés d’une affectation.](./media/azure-rbac/jit-set-active-assignment.png) ](./media/azure-rbac/jit-set-active-assignment.png#lightbox)

Après avoir effectué les attributions, vérifiez que l’accès juste-à-temps fonctionne en accédant au cluster. Par exemple, utilisez la commande `kubectl get nodes` pour afficher les nœuds du cluster :

```console
kubectl get nodes
```

Notez la configuration requise pour l’authentification et suivez les étapes pour vous authentifier. Si l’authentification est réussie, le résultat devrait ressembler à ce qui suit :

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME      STATUS   ROLES    AGE      VERSION
node-1    Ready    agent    6m36s    v1.18.14
node-2    Ready    agent    6m42s    v1.18.14
node-3    Ready    agent    6m33s    v1.18.14
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> Connectez-vous en toute sécurité au cluster à l’aide de [Connexion au cluster](cluster-connect.md).
