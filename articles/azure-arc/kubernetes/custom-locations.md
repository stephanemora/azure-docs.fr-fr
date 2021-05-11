---
title: Emplacements personnalisés sur Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: Utiliser des emplacements personnalisés pour déployer des services PaaS Azure sur des clusters Kubernetes avec Azure Arc
ms.openlocfilehash: d8e1c11069a1097b0bfea3319eeb90fcee3eec82
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287732"
---
# <a name="custom-locations-on-azure-arc-enabled-kubernetes"></a>Emplacements personnalisés sur Kubernetes avec Azure Arc

En tant qu’extensions d’emplacement Azure, les *Emplacements personnalisés* permettent aux administrateurs de locataire d’utiliser leurs clusters Kubernetes avec Azure Arc comme emplacements cibles pour le déploiement d’instances de services Azure. Les exemples de ressources Azure incluent SQL Managed Instance avec Azure Arc et PostgreSQL Hyperscale avec Azure Arc.

À l’instar des emplacements Azure, les utilisateurs finaux au sein du locataire disposant d’un accès aux emplacements personnalisés peuvent y déployer des ressources à l’aide de la capacité de calcul privée de leur entreprise.

Une vue d’ensemble conceptuelle de cette fonctionnalité est disponible dans l’article [Emplacements personnalisés – Kubernetes avec Azure Arc](conceptual-custom-locations.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis

- [Procédez à l’installation ou à la mise à niveau d’Azure CLI](/cli/azure/install-azure-cli) vers une version >= 2.16.0.

- Extensions Azure CLI `connectedk8s` (version >= 1.1.0), `k8s-extension` (version >= 0.2.0) et `customlocation` (version >= 0.1.0). Installez ces extensions Azure CLI en exécutant les commandes suivantes :
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Si les extensions `connectedk8s`, `k8s-extension` et `customlocation` sont déjà installées, vous pouvez les mettre à jour vers la version la plus récente à l’aide de la commande suivante :

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- L’inscription du fournisseur est terminée pour `Microsoft.ExtendedLocation`.
    1. Entrez les commandes suivantes :
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. Supervisez le processus d’inscription. L’inscription peut prendre jusqu’à 10 minutes.
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

>[!NOTE]
>**Régions prises en charge pour les emplacements personnalisés :**
>* USA Est
>* Europe Ouest

## <a name="enable-custom-locations-on-cluster"></a>Activer les emplacements personnalisés sur le cluster

Pour activer cette fonctionnalité sur votre cluster, exécutez la commande suivante :

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

> [!NOTE]
> 1. La fonctionnalité Emplacements personnalisés dépend de la fonctionnalité Connexion au cluster. Les deux fonctionnalités doivent donc être activées pour que les emplacements personnalisés fonctionnent.
> 2. La commande `az connectedk8s enable-features` doit être exécutée sur une machine sur laquelle le fichier `kubeconfig` pointe vers le cluster sur lequel les fonctionnalités doivent être activées.
> 3. Si vous êtes connecté à Azure CLI avec un principal de service, des [autorisations supplémentaires](troubleshooting.md#enable-custom-locations-using-service-principal) doivent être accordées à celui-ci avant d'activer la fonctionnalité Emplacements personnalisés.

## <a name="create-custom-location"></a>Créer un emplacement personnalisé

1. Créer un cluster Kubernetes avec Azure Arc.
    - Si vous n’avez pas encore connecté de cluster, utilisez notre [Démarrage rapide](quickstart-connect-cluster.md).
    - [Mettez à niveau vos agents](agent-upgrade.md#manually-upgrade-agents) vers une version >= 1.1.0.

1. Déployez l’extension de cluster du service Azure dont vous souhaitez l’instance en plus de l’emplacement personnalisé :

    ```azurecli
    az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
    ```

    > [!NOTE]
    > Un proxy sortant sans authentification et un proxy sortant avec authentification de base sont pris en charge par l’extension de cluster Services de données avec Arc. Un proxy sortant qui attend des certificats approuvés n’est pas pris en charge actuellement.

1. Récupérez l’identificateur Azure Resource Manager du cluster Kubernetes avec Azure Arc, référencé dans les étapes ultérieures comme `connectedClusterId` :

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Récupérez l’identificateur Azure Resource Manager de l’extension de cluster déployée en plus du cluster Kubernetes avec Azure Arc, référencé dans les étapes ultérieures comme `extensionId` :

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Créez un emplacement personnalisé en référençant le cluster Kubernetes avec Azure Arc et l’extension :

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> Se connecter en toute sécurité au cluster à l’aide de [Connexion au cluster](cluster-connect.md)