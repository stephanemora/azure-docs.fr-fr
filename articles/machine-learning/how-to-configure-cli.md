---
title: Installer, configurer et utiliser l’interface CLI 2.0
titleSuffix: Azure Machine Learning
description: Découvrez comment installer, configurer et utiliser l’extension Azure CLI pour le machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 05/25/2021
ms.reviewer: laobri
ms.openlocfilehash: e93985b6583f164e1ea497f5d8cad7352b60f556
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111755552"
---
# <a name="install-set-up-and-use-the-20-cli-preview"></a>Installer, configurer et utiliser l’interface CLI 2.0 (préversion)

L’extension `ml` (préversion) d’[Azure CLI](/cli/azure/) est l’interface améliorée d’Azure Machine Learning. Elle permet d’entraîner et de déployer des modèles à partir de la ligne de commande, avec des fonctionnalités qui accélèrent le scale-up et le scale-out pour la science des données tout en assurant le suivi du cycle de vie des modèles.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

- Pour utiliser l'interface de ligne de commande, vous devez disposer d'un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.
- Pour utiliser les commandes CLI dans ce document à partir de votre **environnement local**, vous avez besoin [d’Azure CLI](/cli/azure/install-azure-cli).

    > [!TIP]
    > Si vous utilisez [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), l’interface CLI est accessible via le navigateur et réside dans le cloud.

## <a name="installation"></a>Installation

La nouvelle extension du machine learning **nécessite Azure CLI version `>=2.15.0`** . Vérifiez que cette condition est respectée :

```azurecli
az version
```

Si ce n’est pas le cas, [mettez à niveau votre interface Azure CLI](/cli/azure/update-azure-cli).

Vérifiez les extensions Azure CLI que vous avez installées :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_list":::

Vérifiez qu’aucune extension en conflit utilisant l’espace de noms `ml` n’est installée, notamment l’extension `azure-cli-ml` :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_remove":::

À présent, installez l’extension `ml` :

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_install":::

Exécutez la commande help pour vérifier votre installation et voir les sous-commandes disponibles :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_verify":::

Vous pouvez mettre à niveau l’extension vers la dernière version :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_update":::

## <a name="set-up"></a>Configurer

Connexion :

```azurecli
az login
```

Si vous avez accès à plusieurs abonnements Azure, vous pouvez définir votre abonnement actif :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

S’il n’existe pas déjà, vous pouvez créer le groupe de ressources Azure :

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_group_create":::

Les sous-commandes de machine learning nécessitent les paramètres `--workspace/-w` et `--resource-group/-g`. Pour éviter d’avoir à les taper plusieurs fois, configurez des valeurs par défaut :

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

> [!TIP]
> La plupart des exemples de code partent du principe que vous avez défini un espace de travail et un groupe de ressources par défaut. Vous pouvez les remplacer sur la ligne de commande.

Créez maintenant l’espace de travail d’apprentissage automatique :

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_workspace_create":::

## <a name="hello-world"></a>Hello World

Pour suivre la procédure, clonez le référentiel d’exemples et changez-le en sous-répertoire `cli` :

```azurecli-interactive
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples/cli
```

Pour exécuter Hello World localement via Python, reportez-vous à l’exemple situé dans le sous-répertoire `jobs` :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/hello-world.yml":::

> [!IMPORTANT]
> [Docker](https://docker.io) doit être installé et exécuté localement.

Envoyez le travail en streamant les journaux vers la sortie de la console et en ouvrant l’exécution dans le studio Azure Machine Learning :

:::code language="azurecli" source="~/azureml-examples-main/cli/hello-world.sh" id="hello_world":::

> [!IMPORTANT]
> La première exécution peut prendre plusieurs minutes, car l’image Docker est extraite localement et le travail Azure ML est exécuté. Pour les exécutions suivantes, l’image est mise en cache localement, ce qui accélère le processus.

## <a name="next-steps"></a>Étapes suivantes

- [Entraîner des modèles à l’aide de l’extension CLI Machine Learning (préversion)](how-to-train-cli.md)
- [Configurer l’extension Visual Studio Code d’Azure Machine Learning](how-to-setup-vs-code.md)
- [Entraîner un modèle de classification d’images TensorFlow à l’aide de l’extension Visual Studio Code d’Azure Machine Learning](tutorial-train-deploy-image-classification-model-vscode.md)
