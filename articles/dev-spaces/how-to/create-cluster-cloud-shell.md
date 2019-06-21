---
title: Comment créer un cluster Kubernetes activé pour Azure Dev Spaces à l’aide d’Azure Cloud Shell
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Découvrez comment créer rapidement un cluster Kubernetes activé pour Azure Dev Spaces directement depuis votre navigateur, sans rien installer.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
ms.openlocfilehash: cd0c8c3c26feefe3448ada1cf1575706cd17e525
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808693"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Créer un cluster Kubernetes à l’aide d’Azure Cloud Shell

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