---
title: Guide pratique pour mettre à niveau les outils Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
ms.custom: devx-track-azurecli
description: Découvrez comment mettre à niveau les outils en ligne de commande Azure Dev Spaces, l’extension Visual Studio Code et l’extension Visual Studio.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, conteneurs
ms.openlocfilehash: aebe81d58a1bcd44f5766d368dbafa53c81ed2b8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504328"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Guide pratique pour mettre à niveau les outils Azure Dev Spaces

S’il existe une nouvelle version et que vous utilisez déjà Azure Dev Spaces, vous devrez peut-être mettre à niveau vos outils client Azure Dev espaces.

## <a name="update-the-azure-cli"></a>Mettre à jour Azure CLI

Lorsque vous mettez à jour la dernière version d’Azure CLI, vous obtenez également la dernière version de l’extension CLI Dev Spaces.

Vous n’avez pas besoin de désinstaller la version précédente, il vous suffit de rechercher le téléchargement approprié dans [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Mettre à jour l’extension CLI Dev Spaces et les outils de ligne de commande

Exécutez la commande suivante :

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Mettre à jour l’extension VS Code

Une fois installée, l’extension se met à jour automatiquement. Vous devrez peut-être recharger l’extension pour utiliser les nouvelles fonctionnalités. Dans VS Code, ouvrez le volet **Extensions**, choisissez les extensions **Azure Dev Spaces**, puis **Recharger**.

## <a name="update-visual-studio"></a>Mettre à jour Visual Studio

Azure Dev Spaces fait partie de la charge de travail de développement d’Azure, et est inclus dans toutes les mises à jour de Visual Studio.

## <a name="next-steps"></a>Étapes suivantes

Testez les nouveaux outils en créant un nouveau cluster. Testez les démarrages rapides et les didacticiels sur [Azure Dev Spaces](../index.yml).
