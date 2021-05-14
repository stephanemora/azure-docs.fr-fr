---
title: Guide pratique pour mettre à niveau les outils Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Découvrez comment mettre à niveau les outils en ligne de commande Azure Dev Spaces, l’extension Visual Studio Code et l’extension Visual Studio.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, conteneurs
ms.openlocfilehash: ba9bb220862396c7265eb012996ae93b8208473d
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108075462"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Guide pratique pour mettre à niveau les outils Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

S’il existe une nouvelle version et que vous utilisez déjà Azure Dev Spaces, vous devrez peut-être mettre à niveau vos outils client Azure Dev espaces.

## <a name="update-the-azure-cli"></a>Mettre à jour Azure CLI

Lorsque vous mettez à jour la dernière version d’Azure CLI, vous obtenez également la dernière version de l’extension CLI Dev Spaces.

Vous n’avez pas besoin de désinstaller la version précédente, il vous suffit de rechercher le téléchargement approprié dans [Azure CLI](/cli/azure/install-azure-cli).


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
