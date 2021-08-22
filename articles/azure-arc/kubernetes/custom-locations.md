---
title: Créer et gérer des emplacements personnalisés sur Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: Utiliser des emplacements personnalisés pour déployer des services PaaS Azure sur des clusters Kubernetes avec Azure Arc
ms.openlocfilehash: 5fa255755dd0b78498203624194d081447d70a13
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730853"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>Créer et gérer des emplacements personnalisés sur Kubernetes avec Azure Arc

En tant qu’extensions d’emplacement Azure, les *Emplacements personnalisés* permettent aux administrateurs de locataire d’utiliser leurs clusters Kubernetes avec Azure Arc comme emplacements cibles pour le déploiement d’instances de services Azure. Les exemples de ressources Azure incluent SQL Managed Instance avec Azure Arc et PostgreSQL Hyperscale avec Azure Arc.

À l’instar des emplacements Azure, les utilisateurs finaux au sein du locataire disposant d’un accès aux emplacements personnalisés peuvent y déployer des ressources à l’aide de la capacité de calcul privée de leur entreprise.

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
> * Activer les emplacements personnalisés sur votre cluster Kubernetes avec Azure Arc.
> * Déployer l’extension de cluster de service Azure de l’instance de service Azure sur votre cluster.
> * Créez un emplacement personnalisé sur votre cluster Kubernetes avec Azure Arc.

Une vue d’ensemble conceptuelle de cette fonctionnalité est disponible dans l’article [Emplacements personnalisés – Kubernetes avec Azure Arc](conceptual-custom-locations.md).

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis

- [Procédez à l’installation ou à la mise à niveau d’Azure CLI](/cli/azure/install-azure-cli) vers une version >= 2.16.0.

- Installez les extensions Azure CLI suivantes :
    - `connectedk8s` (version 1.1.0 ou ultérieure)
    - `k8s-extension` (version 0.2.0 ou ultérieure)
    - `customlocation` (version 0.1.0 ou ultérieure) 
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Si les extensions `connectedk8s`, `k8s-extension` et `customlocation` sont déjà installées, mettez-les à jour vers la version la plus récente à l’aide de la commande suivante :

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- Vérifiez que l’inscription du fournisseur est terminée pour `Microsoft.ExtendedLocation`.
    1. Entrez les commandes suivantes :
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. Supervisez le processus d’inscription. L’inscription peut prendre jusqu’à 10 minutes.
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

- Vérifiez que vous disposez d’un [cluster connecté Kubernetes avec Azure Arc](quickstart-connect-cluster.md).
    - [Mettez à niveau vos agents](agent-upgrade.md#manually-upgrade-agents) vers la version 1.1.0 ou une version ultérieure.

>[!NOTE]
>**Régions prises en charge pour les emplacements personnalisés :**
>* USA Est
>* Europe Ouest

## <a name="enable-custom-locations-on-cluster"></a>Activer les emplacements personnalisés sur le cluster

Si vous êtes connecté à Azure CLI en tant qu’utilisateur Azure AD, exécutez la commande suivante pour activer cette fonctionnalité sur votre cluster :

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

Si vous êtes connecté à Azure CLI à l’aide d’un principal de service, effectuez les étapes suivantes pour activer cette fonctionnalité sur votre cluster :

1. Récupérez l’ID d’objet de l’application Azure AD utilisée par le service Azure Arc :

    ```console
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. Utilisez la valeur `<objectId>` de l’étape ci-dessus pour activer la fonctionnalité Emplacements personnalisés sur le cluster :

    ```console
    az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
    ```

> [!NOTE]
> 1. La fonctionnalité Emplacements personnalisés dépend de la fonctionnalité Connexion au cluster. Les deux fonctionnalités doivent donc être activées pour que les emplacements personnalisés fonctionnent.
> 2. La commande `az connectedk8s enable-features` doit être exécutée sur une machine sur laquelle le fichier `kubeconfig` pointe vers le cluster sur lequel les fonctionnalités doivent être activées.

## <a name="create-custom-location"></a>Créer un emplacement personnalisé

1. Déployer l’extension de cluster de service Azure de l’instance de service Azure souhaitée sur votre cluster :

    * [Data Services avec Azure Arc](../data/create-data-controller-direct-cli.md#create-the-arc-data-services-extension)

        > [!NOTE]
        > Un proxy sortant sans authentification et un proxy sortant avec authentification de base sont pris en charge par l’extension de cluster Services de données avec Arc. Un proxy sortant qui attend des certificats approuvés n’est pas pris en charge actuellement.


    * [Azure App Service sur Azure Arc](../../app-service/manage-create-arc-environment.md#install-the-app-service-extension)

    * [Event Grid sur Kubernetes](../../event-grid/kubernetes/install-k8s-extension.md)

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

- Connectez-vous en toute sécurité au cluster à l’aide de [Connexion au cluster](cluster-connect.md).
- Poursuivez avec [Azure App Service sur Azure Arc](../../app-service/overview-arc-integration.md) pour obtenir des instructions de bout en bout sur l’installation des extensions, la création d’emplacements personnalisés et la création de l’environnement Kubernetes App Service. 
- Créez une rubrique Event Grid et un abonnement aux événements pour [Event Grid sur Kubernetes](../../event-grid/kubernetes/overview.md).
- En savoir plus sur les [extensions Kubernetes avec Azure Arc](extensions.md#currently-available-extensions) actuellement disponibles.