---
title: Azure RBAC pour clusters Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Utiliser Azure RBAC pour les contrôles d’autorisation sur les clusters Kubernetes avec Azure Arc
ms.openlocfilehash: f0275e1516e8487b5a00fb08c885b09b6df1684c
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108145696"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Intégrer Azure Active Directory à des clusters Kubernetes avec Azure Arc

Les types d’objets Kubernetes [ClusterRoleBinding et RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) permettent de définir l’autorisation dans Kubernetes en mode natif. Cette fonctionnalité vous permet d'utiliser Azure Active Directory et les attributions de rôles Azure pour contrôler les vérifications des autorisations sur le cluster. Cela implique que vous pouvez désormais utiliser les attributions de rôles Azure pour contrôler de manière précise qui peut lire, écrire ou supprimer vos objets Kubernetes comme Deployment, Pod et Service.

Une vue d’ensemble conceptuelle de cette fonctionnalité est disponible dans l’article [Azure RBAC pour clusters Kubernetes avec Azure Arc](conceptual-azure-rbac.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis

- [Procédez à l'installation ou à la mise à niveau d'Azure CLI](/cli/azure/install-azure-cli) vers la version >= 2.16.0

- Installez l’extension Azure CLI `connectedk8s` version >= 1.1.0 :

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    Si l’extension `connectedk8s` est déjà installée, vous pouvez la mettre à jour vers la version la plus récente à l’aide de la commande suivante : 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Cluster Kubernetes avec Azure Arc connecté.
    - Si vous n’avez pas encore connecté de cluster, utilisez notre [Guide de démarrage rapide](quickstart-connect-cluster.md).
    - [Mettez à niveau vos agents vers la](agent-upgrade.md#manually-upgrade-agents) version >= 1.1.0.

> [!NOTE]
> Cette fonctionnalité ne peut pas être configurée pour les offres Kubernetes managées de fournisseurs cloud comme Elastic Kubernetes Service ou Google Kubernetes Engine où l’utilisateur n’a pas d’accès au `apiserver` du cluster. Pour les clusters Azure Kubernetes service (AKS), cette [fonctionnalité est disponible en mode natif](../../aks/manage-azure-rbac.md) et ne nécessite pas que le cluster AKS soit connecté à Azure Arc.

## <a name="set-up-azure-ad-applications"></a>Configurer les applications Azure AD

### <a name="create-server-application"></a>Créer une application serveur

1. Créez une nouvelle application Azure AD et récupérez sa valeur `appId`, qui est utilisée dans les étapes ultérieures comme `serverApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. Mettez à jour les revendications d’appartenance au groupe d’applications :

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. Créez un principal de service et récupérez sa valeur de champ `password`, qui est nécessaire plus tard comme `serverApplicationSecret` lors de l’activation de cette fonctionnalité sur le cluster :

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
    > * Cette étape doit être exécutée par un administrateur client Azure.
    > * Pour l’utilisation de cette fonctionnalité en production, il est recommandé de créer une application serveur différente pour chaque cluster.

### <a name="create-client-application"></a>Créer une application cliente

1. Créez une application Azure AD et récupérez sa valeur appId, qui est utilisée dans les étapes ultérieures comme `clientApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. Créez un principal de service pour l’application cliente :

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. Obtenez le `oAuthPermissionId` pour l’application serveur :

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

1. Créez un fichier nommé accessCheck.json avec les éléments suivants :

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

3. À partir de la sortie de la commande ci-dessus, stockez la valeur du champ `id`, qui est utilisée dans les étapes ultérieures en tant que `roleId`.

4. Créez une attribution de rôle sur l’application serveur en tant que personne responsable à l’aide du rôle créé plus haut :

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>Activer Azure RBAC sur le cluster

1. Activez Azure RBAC sur votre cluster Kubernetes avec Arc en exécutant la commande suivante :

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. Avant d’exécuter la commande ci-dessus, assurez-vous que le fichier `kubeconfig` sur l’ordinateur pointe sur le cluster sur lequel activer la fonctionnalité Azure RBAC.
    > 2. Utilisez `--skip-azure-rbac-list` avec la commande ci-dessus pour obtenir une liste séparée par des virgules de noms d’utilisateur/adresses de messagerie/OID en cours de vérification des autorisations à l’aide des objets Kubernetes natifs ClusterRoleBinding et RoleBinding à la place d’Azure RBAC.

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>Pour un cluster générique où aucun rapprochement n’est exécuté sur la spécification apiserver :

1. Utilisez SSH dans chaque nœud principal du cluster et exécutez les étapes suivantes :

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
    
        Si la version du cluster Kubernetes est >= 1.19.0, les éléments `apiserver argument` suivants doivent également être définis :

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Enregistrez et quittez l’éditeur pour mettre à jour le pod `apiserver`.


### <a name="for-a-cluster-created-using-cluster-api"></a>Pour un cluster créé à l’aide de l’API de cluster

1. Copiez le secret de protection contenant les fichiers de configuration du webhook d’authentification et d’autorisation `from the workload cluster` sur votre ordinateur :

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. Remplacez le champ `namespace` du fichier `azure-arc-guard-manifests.yaml` par l’espace de noms dans le cluster de gestion dans lequel vous appliquez les ressources personnalisées pour la création de clusters de charge de travail.

1. Appliquez ce manifeste :

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Modifiez l’objet `KubeadmControlPlane` en exécutant `kubectl edit kcp <clustername>-control-plane` :
    
    1. Ajoutez l’extrait de code suivant sous `files:` :
    
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

    1. Ajoutez l’extrait de code suivant sous `apiServer:` -> `extraVolumes:` :
    
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

    1. Ajoutez l’extrait de code suivant sous `apiServer:` -> `extraArgs:` :
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Enregistrez et quittez pour mettre à jour l’objet `KubeadmControlPlane`. Attendez que ces modifications soient appliquées sur le cluster de charge de travail.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Créer des attributions de rôles pour permettre aux utilisateurs d’accéder au cluster

Les propriétaires de la ressource Kubernetes avec Azure Arc peuvent utiliser les rôles intégrés ou des rôles personnalisés pour accorder à d’autres utilisateurs l’accès au cluster Kubernetes.

### <a name="built-in-roles"></a>Rôles intégrés

| Role | Description |
|---|---|
| [Visionneuse Kubernetes Azure Arc](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | Autorise l’accès en lecture seule pour voir la plupart des objets dans un espace de noms. Ce rôle n’autorise pas l’affichage des secrets. Cela est dû au fait que l’autorisation `read` sur les secrets autorise l’accès aux informations d’identification `ServiceAccount` dans l’espace de noms, ce qui permet à son tour d’accéder à l’API à l’aide de ce `ServiceAccount` (une forme d’élévation des privilèges). |
| [Enregistreur Kubernetes Azure Arc](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | Autorise l’accès en lecture/écriture pour la plupart des objets dans un espace de noms. Ce rôle n’autorise pas l’affichage ni la modification des rôles et des liaisons de rôles. Toutefois, ce rôle permet d’accéder aux secrets et aux pods en cours d’exécution comme n’importe quel `ServiceAccount` de l’espace de noms. Il peut donc être utilisé pour obtenir les niveaux d’accès API de n’importe quel `ServiceAccount` dans l’espace de noms. |
| [Administrateur Kubernetes Azure Arc](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | Autorise l’accès administrateur. Destiné à être accordé dans un espace de noms à l’aide d’un RoleBinding. Si utilisé dans un RoleBinding, autorise l’accès en lecture/écriture à la plupart des ressources dans un espace de noms, y compris la possibilité de créer des rôles et des liaisons de rôles dans l’espace de noms. Ce rôle n’autorise pas l’accès en écriture au quota de ressources ou à l’espace de noms lui-même. |
| [Administrateur de cluster Kubernetes Azure Arc](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | Autorise l’accès de super utilisateur qui permet d’exécuter n’importe quelle action sur toutes les ressources. Lorsqu’il est utilisé dans un ClusterRoleBinding, il offre un contrôle total sur chaque ressource du cluster et dans tous les espaces de noms. Lorsqu’il est utilisé dans un RoleBinding, il offre un contrôle total sur chaque ressource dans l’espace de noms de la liaison de rôle, y compris l’espace de noms lui-même.|

Vous pouvez créer des attributions de rôles limitées au cluster Kubernetes avec Arc sur le panneau `Access Control (IAM)` de la ressource de cluster sur le portail Azure. Vous pouvez également utiliser des commandes Azure CLI, comme indiqué ci-dessous :

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

où `AZURE-AD-ENTITY-ID` peut être un nom d’utilisateur (par exemple, testuser@mytenant.onmicrosoft.com) ou même le `appId` d’un principal de service.

Voici un autre exemple de création d’une attribution de rôle étendue à un espace de noms spécifique au sein du cluster.

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Alors que les attributions de rôles limitées au cluster peuvent être créées à l’aide du portail Azure ou de l’interface CLI, les attributions de rôles limitées aux espaces de noms peuvent uniquement être créées à l’aide de l’interface CLI.

### <a name="custom-roles"></a>Rôles personnalisés

Vous pouvez choisir de créer votre propre définition de rôle pour utilisation dans les attributions de rôles.

Examinez en détail l’exemple ci-dessous de définition de rôle qui permet à un utilisateur de lire uniquement les déploiements. Pour plus d’informations, consultez [la liste complète des actions de données que vous pouvez utiliser pour créer une définition de rôle](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes).

Copiez l’objet JSON ci-dessous dans un fichier appelé custom-role.js. Remplacez l’espace réservé `<subscription-id>` par l’ID réel de l’abonnement. Le rôle personnalisé ci-dessous utilise l’une des actions de données et vous permet de voir tous les déploiements dans l’étendue (cluster/espace de noms) où l’attribution de rôle est créée.

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

1. Créez la définition de rôle en exécutant la commande suivante à partir du dossier où vous avez enregistré `custom-role.json` :

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Créez une attribution de rôle à l’aide de cette définition de rôle personnalisée :

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Configurer kubectl avec les informations d’identification de l’utilisateur

Il existe deux façons d’obtenir le fichier `kubeconfig` nécessaire pour accéder au cluster :
1. Utilisez la fonctionnalité de [connexion de cluster](cluster-connect.md) (`az connectedk8s proxy`) du cluster Kubernetes avec Azure Arc.
1. L’administrateur de cluster partage le fichier `kubeconfig` avec tous les autres utilisateurs.

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>Si vous accédez au cluster à l’aide de la fonctionnalité de connexion de cluster

Pour démarrer le processus proxy, exécutez la commande suivante :

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

Une fois le processus de proxy en cours d’exécution, vous pouvez ouvrir un autre onglet dans votre console pour [commencer à envoyer vos demandes au cluster](#sending-requests-to-cluster).

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Si l’administrateur de cluster a partagé le fichier `kubeconfig` avec vous 

1. Exécutez la commande suivante pour définir les informations d’identification de l’utilisateur :

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. Ouvrez le fichier `kubeconfig` créé précédemment. Sous `contexts`, vérifiez que le contexte associé aux points du cluster pointe vers les informations d’identification de l’utilisateur créées à l’étape précédente.

1. Ajoutez un paramètre de **mode de configuration** sous la configuration de l’utilisateur :
  
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

## <a name="sending-requests-to-cluster"></a>Envoi de demandes au cluster

1. Exécutez une commande `kubectl`. Par exemple :
  * `kubectl get nodes` 
  * `kubectl get pods`

1. Une fois que vous êtes invité à fournir une authentification basée sur le navigateur, copiez l’URL de connexion de l’appareil `https://microsoft.com/devicelogin` et ouvrez votre navigateur web.

1. Entrez le code imprimé sur votre console, copiez et collez le code sur votre terminal dans l’invite d’entrée d’authentification de l’appareil.

1. Entrez le nom d’utilisateur (testuser@mytenant.onmicrosoft.com) et le mot de passe associé.

1. Si un message d’erreur semblable à celui-ci s’affiche, cela signifie que vous n’êtes pas autorisé à accéder à la ressource demandée :

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    Un administrateur doit créer une nouvelle attribution de rôle pour autoriser cet utilisateur à accéder à la ressource.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> Se connecter en toute sécurité au cluster à l’aide de [Connexion au cluster](cluster-connect.md)