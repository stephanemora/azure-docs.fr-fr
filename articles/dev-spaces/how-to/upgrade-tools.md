---
title: Guide pratique pour mettre à niveau les outils Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/03/2018
ms.topic: article
ms.technology: azds-kubernetes
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, conteneurs
ms.openlocfilehash: 3d7e82903400d8aab0d33628af2b404583cb42a7
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706348"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Guide pratique pour mettre à niveau les outils Azure Dev Spaces

S’il existe une nouvelle version et que vous utilisez déjà Azure Dev Spaces, vous devrez peut-être mettre à niveau vos outils client Azure Dev espaces.

## <a name="update-the-azure-cli"></a>Mettre à jour Azure CLI

Lorsque vous mettez à jour la dernière version d’Azure CLI, vous obtenez également la dernière version de l’extension CLI Dev Spaces.

Vous n’avez pas besoin de désinstaller la version précédente, il vous suffit de rechercher le téléchargement approprié dans [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Mettre à jour l’extension CLI Dev Spaces et les outils de ligne de commande

Exécutez la commande suivante :

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Mettre à jour l’extension VS Code

Une fois installée, l’extension se met à jour automatiquement. Vous devrez peut-être recharger l’extension pour utiliser les nouvelles fonctionnalités. Dans VS Code, ouvrez le volet **Extensions**, choisissez les extensions **Azure Dev Spaces**, puis **Recharger**.

## <a name="update-the-visual-studio-extension"></a>Mettre à jour l’extension Visual Studio

Comme pour toute autre extension et mise à jour, Visual Studio vous avertit lorsqu’une mise à jour pour Visual Studio Tools pour Kubernetes, avec Azure Dev Spaces inclus, est disponible. Recherchez une icône d’indicateur dans le coin supérieur droit de l’écran.

Pour mettre à jour les outils dans Visual Studio, choisissez l’élément de menu **Outils > Extensions et mises à jour** et, sur le côté gauche, choisissez **Mises à jour**. Recherchez **Visual Studio Tools pour Kubernetes** et choisissez le bouton **Mettre à jour**.

## <a name="next-steps"></a>Étapes suivantes

Testez les nouveaux outils en créant un nouveau cluster. Testez les démarrages rapides et les didacticiels sur [Azure Dev Spaces](/azure/dev-spaces).

> [!WARNING]
> Azure Dev Spaces sur des clusters existants ne sera pas immédiatement corrigé, donc pour vous assurer que vous utilisez la version la plus récente sur tous vos déploiements Azure, créez un nouveau cluster après la mise à niveau des outils.
