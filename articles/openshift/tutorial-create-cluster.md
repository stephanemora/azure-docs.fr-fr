---
title: Tutoriel - Créer un cluster Azure Red Hat OpenShift 4
description: Découvrez comment créer un cluster Microsoft Azure Red Hat OpenShift à l’aide d’Azure CLI
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 61b6ad0bedb4817c262b4269a6e9f6930a6caa6c
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985686"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Tutoriel : Créer un cluster Azure Red Hat OpenShift 4

Dans ce tutoriel, première partie d’une série qui en compte trois, vous allez préparer votre environnement pour créer un cluster Azure Red Hat OpenShift exécutant OpenShift 4, puis créer un cluster. Vous apprendrez à :
> [!div class="checklist"]
> * Préparer les éléments requis et créer le réseau virtuel et ses sous-réseaux nécessaires
> * Déployer un cluster

## <a name="before-you-begin"></a>Avant de commencer

Si vous choisissez d’installer et d’utiliser CLI localement, ce tutoriel exige au minimum la version 2.0.75 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="verify-your-permissions"></a>Vérifier vos autorisations

Pour créer un cluster Azure Red Hat OpenShift, vérifiez les autorisations suivantes sur votre abonnement Azure, utilisateur Azure Active Directory ou principal de service :

|Autorisations|Groupe de ressources qui contient le réseau virtuel|Utilisateur exécutant `az aro create`|Principal du service passé en tant que `–client-id`|
|----|:----:|:----:|:----:|
|**Administrateur de l'accès utilisateur**|X|X| |
|**Contributeur**|X|X|X|

### <a name="install-the-az-aro-extension"></a>Installer l’extension `az aro`
L’extension `az aro` vous permet de créer des clusters Azure Red Hat OpenShift, d’y accéder et de les supprimer directement à partir de la ligne de commande à l’aide d’Azure CLI.

Exécutez la commande suivante pour installer l’extension `az aro`.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Si vous avez déjà installé l’extension, vous pouvez la mettre à jour en exécutant la commande suivante.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Inscrire le fournisseur de ressources

Vous devez ensuite inscrire le fournisseur de ressources `Microsoft.RedHatOpenShift` dans votre abonnement.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Vérifiez que l’extension est inscrite.

```azurecli-interactive
az -v
```

  Vous devez obtenir une sortie similaire à celle ci-dessous.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Récupération d’un secret d’extraction Red Hat (facultatif)

Un secret d’extraction Red Hat permet au cluster d’accéder à des registres de conteneurs Red Hat et à du contenu supplémentaire. Cette étape est facultative mais recommandée.

1. **[Accédez au portail de votre gestionnaire de cluster Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) et connectez-vous.**

   Vous devrez vous connecter à votre compte Red Hat existant, ou créer un compte Red Hat avec votre adresse e-mail professionnelle et accepter les conditions générales.

2. **Cliquez sur Download pull secret (Télécharger le secret d’extraction)** .

Conservez le fichier `pull-secret.txt` enregistré à un emplacement sûr, car vous en aurez besoin lors de la création de chaque cluster.

Lors de l’exécution de la commande `az aro create`, vous pouvez référencer votre secret d’extraction à l’aide du paramètre `--pull-secret @pull-secret.txt`. Exécutez `az aro create` à partir du répertoire où vous avez stocké votre fichier `pull-secret.txt`. Sinon, remplacez `@pull-secret.txt` par `@<path-to-my-pull-secret-file>`.

Si vous copiez votre secret d’extraction, ou que vous le référencez dans d’autres scripts, il doit se présenter sous la forme d’une chaîne JSON valide.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Créer un réseau virtuel contenant deux sous-réseaux vides

Vous allez maintenant créer un réseau virtuel contenant deux sous-réseaux vides.

1. **Définissez les variables suivantes**.

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Créer un groupe de ressources**

    Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées. Lorsque vous créez un groupe de ressources, vous devez spécifier un emplacement. Il s’agit de l’emplacement de stockage des métadonnées de groupe de ressources. C’est également là que vos ressources s’exécutent dans Azure si vous ne spécifiez pas une autre région lors de la création de ressources. Créez un groupe de ressources à l’aide de la commande [az group create][az-group-create].

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    L’exemple de sortie suivant montre que le groupe de ressources a été créé correctement :

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Créez un réseau virtuel**.

    Les clusters Azure Red Hat OpenShift exécutant OpenShift 4 nécessitent un réseau virtuel avec deux sous-réseaux vides (pour les nœuds principaux et les nœuds worker).

    Créez un réseau virtuel dans le même groupe de ressources que vous avez créé précédemment.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    L’exemple de sortie suivant montre le réseau virtuel qui a été créé :

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Ajoutez un sous-réseau vide pour les nœuds principaux**.

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Ajoutez un sous-réseau vide pour les nœuds worker**.

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Désactivez les stratégies pour les points de terminaison privés](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) dans le sous-réseau principal**. Cela est obligatoire pour pouvoir se connecter au cluster et le gérer.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Créer le cluster

Exécutez la commande suivante pour créer un cluster. Si vous le souhaitez, vous pouvez [transmettre votre secret d’extraction Red Hat](#get-a-red-hat-pull-secret-optional) pour permettre au cluster d’accéder à des registres de conteneurs Red Hat et à du contenu supplémentaire.

>[!NOTE]
> Si vous copiez/collez des commandes et utilisez l’un des paramètres facultatifs, veillez à supprimer les mots-dièse initiaux ainsi que le texte de commentaire de fin. De même, fermez l’argument sur la ligne précédente de la commande avec une barre oblique inverse de fin.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Après l’exécution de la commande `az aro create`, la création d’un cluster prend normalement une trentaine de minutes.

>[!IMPORTANT]
> Si vous choisissez de spécifier un domaine personnalisé, par exemple **foo.example.com**, la console OpenShift sera disponible sur une URL telle que `https://console-openshift-console.apps.foo.example.com`, au lieu de l’URL de domaine intégrée `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.
>
> Par défaut, OpenShift utilise des certificats auto-signés pour toutes les routes créées sur `*.apps.<random>.<location>.aroapp.io`.  Si vous choisissez d’utiliser un DNS personnalisé après vous être connecté au cluster, vous devez suivre la documentation OpenShift afin de [configurer une autorité de certification personnalisée pour votre contrôleur d’entrée](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) et configurer une [autorité de certification personnalisée pour votre serveur d’API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).
>

## <a name="next-steps"></a>Étapes suivantes

Dans cette partie du tutoriel, vous avez appris à :
> [!div class="checklist"]
> * Préparer les éléments requis et créer le réseau virtuel et ses sous-réseaux nécessaires
> * Déployer un cluster

Passez au tutoriel suivant :
> [!div class="nextstepaction"]
> [Se connecter à un cluster Azure Red Hat OpenShift](tutorial-connect-cluster.md)
