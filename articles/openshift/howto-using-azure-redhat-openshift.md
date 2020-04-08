---
title: Créer un cluster Azure Red Hat OpenShift 4.3 | Microsoft Docs
description: Créer un cluster avec Azure Red Hat OpenShift 4.3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, red hat, cli
ms.openlocfilehash: 423f09c135da51b8401c1933a4a271d0becd2c8f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349431"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Créer un cluster Azure Red Hat OpenShift 4.3, y accéder et le gérer

> [!IMPORTANT]
> Notez qu’Azure Red Hat OpenShift 4.3 est disponible uniquement en préversion privée dans la région USA Est. L’acceptation de la préversion privée se fait sur invitation uniquement. Inscrivez votre abonnement avant d’essayer d’activer cette fonctionnalité : [Inscription à la préversion privée d’Azure Red Hat OpenShift](https://aka.ms/aro-preview-register)

> [!NOTE]
> Les fonctionnalités d’évaluation sont en libre-service et sont fournies en l’état et en fonction des disponibilités. De plus, elles sont exclues du contrat de niveau de service (SLA) et de la garantie limitée. Par conséquent, ces fonctionnalités ne sont pas destinées à une utilisation en production.

## <a name="prerequisites"></a>Prérequis

Pour créer un cluster Azure Red Hat OpenShift 4.3, vous avez besoin des éléments suivants :

- Azure CLI version 2.0.72 ou ultérieure
  
- Extension « az aro »

- Un réseau virtuel contenant deux sous-réseaux vides, chacun sans groupe de sécurité réseau attaché.  Votre cluster sera déployé sur ces sous-réseaux.

- Une application AAD de cluster (ID client et secret) et un principal de service, ou des autorisations AAD suffisantes pour `az aro create` afin de créer automatiquement une application AAD et un principal de service.

- Le principal de service RP et le principal de service du cluster doivent tous avoir le rôle Contributeur sur le réseau virtuel du cluster.  Si vous disposez du rôle « Administrateur de l’accès utilisateur » sur le réseau virtuel, `az aro create` configure automatiquement les attributions de rôles.

### <a name="install-the-az-aro-extension"></a>Installer l’extension « az aro »
L’extension `az aro` vous permet de créer des clusters Azure Red Hat OpenShift, d’y accéder et de les supprimer directement à partir de la ligne de commande à l’aide d’Azure CLI.

> [!Note] 
> L’extension `az aro` est en préversion. Elle peut être changée ou supprimée dans une version ultérieure.
> Pour adhérer à la préversion de l’extension `az aro`, vous devez inscrire le fournisseur de ressources `Microsoft.RedHatOpenShift`.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Connectez-vous à Azure.

   ```console
   az login
   ```

2. Exécutez la commande suivante pour installer l’extension `az aro` :

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Vérifiez que l’extension ARO est inscrite.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Créer un réseau virtuel contenant deux sous-réseaux vides

Pour créer un réseau virtuel contenant deux sous-réseaux vides, effectuez ces étapes.

1. Définissez les variables suivantes.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   PULL_SECRET="<optional-pull-secret>"
   ```
   >[!NOTE]
   > Le secret d’extraction facultatif permet à votre cluster d’accéder à des registres de conteneurs Red Hat avec du contenu supplémentaire.
   >
   > Accédez à votre secret d’extraction en accédant à https://cloud.redhat.com/openshift/install/azure/installer-provisioned et en cliquant sur *Copy Pull Secret* (Copier le secret d’extraction).
   >
   > Vous devrez vous connecter à votre compte Red Hat ou créer un nouveau compte Red Hat avec votre adresse de messagerie professionnelle et accepter les conditions générales.
 

2. Créez un groupe de ressources pour votre cluster.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Création du réseau virtuel.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Ajoutez deux sous-réseaux vides à votre réseau virtuel.

   ```console
    for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Désactivez les stratégies réseau pour le service Liaison privée sur votre réseau virtuel et vos sous-réseaux. Cela est indispensable pour que le service ARO puisse accéder au cluster et le gérer.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Créer un cluster

Exécutez la commande suivante pour créer un cluster.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --pull-secret "$PULL_SECRET"
```

>[!NOTE]
> Normalement, la création d’un cluster prend environ 35 minutes.

## <a name="access-the-cluster-console"></a>Accéder à la console du cluster

Vous pouvez trouver l’URL de la console du cluster (sous la forme `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) sous la ressource de cluster Azure Red Hat OpenShift 4.3. Exécutez la commande suivante pour voir la ressource :

```console
az aro list -o table
```

Vous pouvez vous connecter au cluster à l’aide de l’utilisateur `kubeadmin`.  Exécutez la commande suivante pour rechercher le mot de passe de l’utilisateur `kubeadmin` :

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Suppression d'un cluster

Exécutez la commande suivante pour supprimer un cluster.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
