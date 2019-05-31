---
title: Comment créer un cluster Kubernetes est activé pour les espaces de développement Azure à l’aide d’Azure Cloud Shell
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Découvrez comment créer rapidement un cluster Kubernetes activé pour Azure Dev Spaces directement depuis votre navigateur, sans rien installer.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
ms.openlocfilehash: 532a094f96817f3c525384694b6fa60a292aa948
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391675"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Créer un cluster Kubernetes à l’aide d’Azure Cloud Shell

Vous pouvez utiliser [Azure Cloud Shell](/azure/cloud-shell) pour créer un cluster pour Azure Dev Spaces via le bouton **Essayer** de cette page. Si vous n’êtes pas connecté, suivez les invites afin de vous connecter en utilisant un compte Azure, puis tapez les commandes à l’invite Azure Cloud Shell lorsqu’elle s’affiche.

## <a name="create-the-cluster"></a>Création du cluster

Tout d’abord, créez le groupe de ressources dans un [région qui prend en charge des espaces de développement Azure][supported-regions].

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


[supported-regions]: ../about.md#supported-regions-and-configurations