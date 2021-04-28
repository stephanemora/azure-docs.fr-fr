---
title: Utilisez la connexion de cluster pour vous connecter aux clusters Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Utilisez la connexion de cluster pour vous connecter de manière sécurisée aux clusters Kubernetes avec Azure Arc
ms.openlocfilehash: 54a462164e4b992451cc66f8a0ec229aff27f2e1
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108145588"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Utilisez la connexion de cluster pour vous connecter aux clusters Kubernetes avec Azure Arc

Avec la connexion de cluster, vous pouvez vous connecter de manière sécurisée aux clusters Kubernetes avec Azure Arc sans avoir besoin d’activer un port entrant sur le pare-feu. L’accès au `apiserver` du cluster Kubernetes avec Arc rend les scénarios suivants possibles :
* Activer le débogage et la résolution des problèmes de manière interactive.
* Permettre aux services Azure d’accéder au cluster pour les [emplacements personnalisés](custom-locations.md) et les autres ressources créées par-dessus.

Une vue d’ensemble conceptuelle de cette fonctionnalité est disponible dans l’article [Connexion de cluster - Kubernetes avec Azure Arc](conceptual-cluster-connect.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis   

- [Procédez à l'installation ou à la mise à niveau d'Azure CLI](/cli/azure/install-azure-cli) vers la version >= 2.16.0

- Installez l’extension Azure CLI `connectedk8s` dont la version est >= 1.1.0 :

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    Si vous avez déjà installé l’extension `connectedk8s`, mettez-la à jour vers la dernière version :
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Cluster Kubernetes avec Azure Arc connecté.
    - Si vous n’avez pas encore connecté de cluster, utilisez notre [Guide de démarrage rapide](quickstart-connect-cluster.md).
    - [Mettez à niveau vos agents](agent-upgrade.md#manually-upgrade-agents) vers la version >= 1.1.0.

- Activez la connexion de cluster sur un cluster Kubernetes avec Azure Arc en exécutant la commande suivante sur une machine où le fichier `kubeconfig` pointe vers le cluster concerné :

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- Activez les points de terminaison ci-dessous pour l’accès sortant en plus de ceux mentionnés dans la [connexion d’un cluster Kubernetes à Azure Arc](quickstart-connect-cluster.md#meet-network-requirements) :

    | Point de terminaison | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>Utilisation

Deux options d’authentification sont prises en charge avec la fonctionnalité de connexion de cluster : 
* Azure Active Directory (Azure AD) 
* Jeton de compte de service

### <a name="option-1-azure-active-directory"></a>Option 1 : Azure Active Directory

1. Faites pointer le fichier `kubeconfig` vers le `apiserver` de votre cluster Kubernetes, créez un ClusterRoleBinding ou un RoleBinding pour l’entité Azure AD (principal de service ou utilisateur) nécessitant l’accès :

    **Pour l’utilisateur :**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **Pour l’application Azure AD :**

    1. Récupérez le `objectId` associé à votre application Azure AD :

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. Créez un ClusterRoleBinding ou un RoleBinding pour l’entité Azure AD (principal de service ou utilisateur) qui doit accéder au cluster :
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. Après vous être connecté à Azure CLI à l’aide de l’entité Azure AD appropriée, récupérez la connexion de cluster `kubeconfig` nécessaire pour communiquer avec le cluster depuis n’importe quel emplacement (même en dehors du pare-feu entourant le cluster) :

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. Utilisez `kubectl` pour envoyer les requêtes au cluster :

    ```console
    kubectl get pods
    ```
    
    Vous devez voir une réponse du cluster contenant la liste de tous les pods présents dans l’espace de noms `default`.

### <a name="option-2-service-account-bearer-token"></a>Option 2 : Jeton du porteur du compte de service

1. Faites pointer le fichier `kubeconfig` vers le `apiserver` de votre cluster Kubernetes, créez un compte de service dans un espace de noms (la commande suivante permet de le créer dans l’espace de noms par défaut) :

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Créez ClusterRoleBinding ou RoleBinding afin d’octroyer à ce [compte de service les autorisations appropriées pour le cluster](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding) :

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Récupérez le jeton du compte de service à l’aide des commandes suivantes

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. Récupérez la connexion de cluster `kubeconfig` nécessaire pour communiquer avec le cluster depuis n’importe quel emplacement (même en dehors du pare-feu entourant le cluster) :

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. Utilisez `kubectl` pour envoyer les requêtes au cluster :

    ```console
    kubectl get pods
    ```

    Vous devez voir une réponse du cluster contenant la liste de tous les pods présents dans l’espace de noms `default`.

## <a name="known-limitations"></a>Limitations connues

Quand des requêtes sont adressées au cluster Kubernetes, si l’entité Azure AD utilisée fait partie de plus de 200 groupes, l’erreur suivante se produit, car il s’agit d’une limitation connue :

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

Pour contourner cette erreur :
1. Créez un [principal de service](/cli/azure/create-an-azure-service-principal-azure-cli), qui est moins susceptible d’être membre de plus de 200 groupes.
1. [Connectez-vous](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) à Azure CLI avec le principal de service avant d’exécuter la commande `az connectedk8s proxy`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> Configurer [Azure AD RBAC](azure-rbac.md) sur vos clusters