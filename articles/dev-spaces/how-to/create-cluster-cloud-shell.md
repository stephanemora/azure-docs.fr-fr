---
title: Créer un cluster Kubernetes avec Azure Dev Spaces activé – Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Découvrez comment créer rapidement un cluster Kubernetes activé pour Azure Dev Spaces directement depuis votre navigateur, sans rien installer.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
ms.openlocfilehash: ac825caa7aacbc6ac21dd1f9fe7acbb9cf12e900
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325767"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Créer un cluster Kubernetes avec Azure Dev Spaces activé à l’aide d’Azure Cloud Shell

Vous pouvez utiliser [Azure Cloud Shell](/azure/cloud-shell) pour créer un cluster Azure Kubernetes Service via le bouton **Essayer** de cette page. Si vous n’êtes pas connecté, suivez les invites afin de vous connecter en utilisant un compte Azure, puis tapez les commandes à l’invite Azure Cloud Shell lorsqu’elle s’affiche.

## <a name="create-the-cluster"></a>Création du cluster

Créez d’abord le groupe de ressources dans une [région qui prend en charge Azure Dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Créez un cluster Kubernetes avec la commande suivante :

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

La création du cluster ne prend que quelques minutes.  Une fois terminée, la sortie s’affiche au format JSON. Recherchez `provisioningState` et vérifiez qu’il s’agit bien de `Succeeded`.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des liens vers les didacticiels complets, consultez la section [Azure Dev Spaces](/azure/dev-spaces/).

> [!IMPORTANT]
> La plupart des didacticiels et guides de démarrage rapide pour Azure Dev Spaces utilisent l’interface CLI d’Azure Dev Spaces pour effectuer les opérations. Vous ne pouvez pas installer l’interface CLI d’Azure Dev Spaces dans Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations