---
title: Installer Azure Dev Spaces sur AKS et les outils côté client
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Découvrez comment installer Azure Dev Spaces sur un cluster AKS et installer les outils côté client.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
ms.openlocfilehash: 2649b36c96313d4a7d878a1c72c3b175ad0f4d30
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325780"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>Installer Azure Dev Spaces sur AKS et les outils côté client

Cet article montre plusieurs manières d’installer Azure Dev Spaces sur un cluster AKS et comment installer les outils côté client.

## <a name="install-azure-dev-spaces-using-the-cli"></a>Installer Azure Dev Spaces à l’aide de l’interface CLI

Avant de pouvoir installer Dev Spaces à l’aide de l’interface CLI, vous avez besoin des éléments suivants :
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit][az-portal-create-account].
* [Azure CLI installé][install-cli]
* [Un cluster AKS][create-aks-cli] dans une [région prise en charge][supported-regions]

Utilisez la commande `use-dev-spaces` pour activer Dev Spaces sur votre cluster AKS et suivez les invites.

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

La commande ci-dessus active Dev Spaces sur le cluster *myAKSCluster* dans le groupe *myResourceGroup* et crée un espace de développement *par défaut*.

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

La commande `use-dev-spaces` installe également l’interface CLI Azure Dev Spaces.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Installer Azure Dev Spaces à l’aide du portail Azure

Avant de pouvoir installer Dev Spaces à l’aide du portail Azure, vous avez besoin des éléments suivants :
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit][az-portal-create-account].
* [Un cluster AKS][create-aks-portal] dans une [région prise en charge][supported-regions]

Pour installer Azure Dev Spaces à l’aide du portail Azure
1. Connectez-vous au [Portail Azure][az-portal].
1. Accédez à votre cluster AKS.
1. Cliquez sur *Dev Spaces*.
1. Changez *Activer Dev Spaces* en *Oui* et cliquez sur *Enregistrer*.

![Activer Dev Spaces via le portail Azure](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

L’installation d’Azure Dev Spaces à l’aide du portail Azure n’installe **aucun** outil côté client pour Azure Dev Spaces.

## <a name="install-the-client-side-tooling"></a>Installer les outils côté client

Vous pouvez utiliser les outils côté client Azure Dev Spaces pour interagir avec des espaces de développement sur un cluster AKS à partir de votre ordinateur local. Il existe plusieurs façons d’installer les outils côté client :

* Dans [Visual Studio Code][vscode], installez l’[extension Azure Dev Spaces][vscode-extension].
* Dans [Visual Studio 2019][visual-studio], installez la charge de travail Développement Azure.
* Dans Visual Studio 2017, installez la charge de travail Développement web et [Visual Studio Tools pour Kubernetes][visual-studio-k8s-tools].
* Téléchargez et installez l’interface CLI [Windows][cli-win], [Mac][cli-mac] ou [Linux][cli-linux]

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment Azure Dev Spaces vous aide à développer des applications plus complexes sur plusieurs conteneurs, et comment vous pouvez simplifier le développement collaboratif en utilisant différentes versions ou branches de votre code dans différents espaces.

> [!div class="nextstepaction"]
> [Développement en équipe dans Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
