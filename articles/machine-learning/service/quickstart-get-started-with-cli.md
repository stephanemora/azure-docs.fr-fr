---
title: Bien démarrer avec Azure Machine Learning en utilisant l’extension CLI | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez apprendre à démarrer avec le service Azure Machine Learning en utilisant l’extension CLI Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
author: rastala
ms.author: roastala
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 856f9629e97f8cf7cf811e7d591cbcad6067f47a
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237158"
---
# <a name="quickstart-get-started-with-azure-machine-learning-using-the-cli-extension"></a>Démarrage rapide : bien démarrer avec Azure Machine Learning en utilisant l’extension CLI

Dans ce guide de démarrage rapide, vous allez utiliser une extension CLI d’apprentissage automatique pour démarrer avec le [service Azure Machine Learning](overview-what-is-azure-ml.md) (préversion).

En utilisant cette interface CLI, vous allez découvrir comment :

1. Créer un espace de travail dans votre abonnement Azure. Cet espace de travail est utilisé par un ou plusieurs utilisateurs pour stocker leurs ressources de calcul, modèles, déploiements et historiques des exécutions dans le cloud.
1. Associer un projet à votre espace de travail.   Un projet est un dossier local qui contient les scripts et les fichiers de configuration nécessaires pour résoudre un problème de machine learning.  
1. Exécuter un script Python dans votre projet qui journalise des valeurs sur plusieurs itérations.
1. Afficher les valeurs journalisées dans l’historique des exécutions de votre espace de travail.

> [!NOTE]
> Pour des raisons pratiques, les ressources Azure suivantes sont automatiquement ajoutées à votre espace de travail, si elles sont disponibles dans votre région : [Container Registry](https://azure.microsoft.com/services/container-registry/), [Stockage](https://azure.microsoft.com/services/storage/), [Application Insights](https://azure.microsoft.com/services/application-insights/) et [Key Vault](https://azure.microsoft.com/services/key-vault/).

Les ressources que vous créez peuvent être utilisées comme prérequis dans d’autres tutoriels et guides pratiques Azure Machine Learning.

Cette interface CLI repose sur le <a href="http://aka.ms/aml-sdk" target="_blank">kit SDK</a> basé sur Python pour le service Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

Vérifiez que les prérequis suivants sont remplis avant de commencer les procédures du guide de démarrage rapide :

+ Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
+ [Python 3.5 ou ultérieur](https://www.python.org/) installé
+ [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installé

## <a name="install-the-cli-extension"></a>Installer l’extension CLI

Sur votre ordinateur, ouvrez un éditeur de ligne de commande et installez [l’extension CLI Azure Machine Learning](reference-azure-machine-learning-cli.md).  Cette installation peut prendre plusieurs minutes.

```azurecli
az extension add azureml-sdk
```

## <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

[!INCLUDE [aml-install-sdk](../../../includes/aml-install-sdk.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. À l’aide d’Azure CLI, connectez-vous à Azure, spécifiez l’abonnement et créez un groupe de ressources.

Dans une fenêtre de ligne de commande, connectez-vous avec la commande Azure CLI, `az login`. Suivez les invites de la connexion interactive :
    
   ```azurecli
   az login
   ```

Affichez la liste des abonnements Azure disponibles, puis spécifiez celui que vous voulez utiliser :
   ```azurecli
   az account list --output table
   az account set --subscription <your-subscription-id>
   az account show
   ```
   où \<your-subscription-id\> est la valeur d’ID de l’abonnement que vous voulez utiliser. N’incluez pas les chevrons.

Créez un groupe de ressources qui contiendra votre espace de travail.
Dans ce guide de démarrage rapide :
   + Le nom du groupe de ressources est `docs-aml`.
   + La région est `eastus2`. 

   ```azurecli
   az group create -n docs-aml -l eastus2
   ```

## <a name="create-a-workspace-and-a-project-folder"></a>Créer un espace de travail et un dossier de projet

Dans la fenêtre de ligne de commande, créez un espace de travail Azure Machine Learning service sous le groupe de ressources.


   Dans ce guide de démarrage rapide :
   + Le nom de l'espace de travail est `docs-ws`.
   + Le nom du groupe de ressources est `docs-aml`.

   ```azurecli
   az ml workspace create -n docs-ws -g docs-aml
   ```

Dans la fenêtre de ligne de commande, créez un dossier sur votre ordinateur local pour votre projet Azure Machine Learning.

   ```
   mkdir docs-prj
   cd docs-prj
   ```

## <a name="create-a-python-script"></a>Créer un script Python

[!INCLUDE [aml-create-script-pi](../../../includes/aml-create-script-pi.md)]

## <a name="run-the-script"></a>Exécutez le script

Attachez le dossier en tant que projet à l’espace de travail. L’argument `--history` spécifie un nom pour le fichier d’historique des exécutions qui capture les métriques pour chaque exécution.

   ```azurecli
   az ml project attach --history my_history -w docs-ws -g docs-aml
   ```

Exécutez un script sur votre ordinateur local.

   ```azurecli
   az ml run submit -c local pi.py
   ```

   Cette commande exécute le code et génère un lien web sur votre console. Copiez-collez le lien dans votre navigateur web.

Dans un navigateur web, accédez à l’URL. Un portail web s’affiche avec les résultats de l’exécution. Vous pouvez inspecter les résultats de cette exécution ou d’exécutions précédentes, s’il en existe.

Le tableau de bord du portail est pris en charge uniquement sur les navigateurs Edge, Chrome et Firefox.

   ![afficher l'historique](./media/quickstart-get-started/web-results.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous venez de créer les ressources nécessaires pour commencer à expérimenter et à déployer des modèles. Vous avez aussi créé un projet, exécuté un script et exploré l’historique des exécutions du script.

Pour une expérience approfondie du flux de travail, suivez le tutoriel Azure Machine Learning relatif à la création, à l’entraînement et au déploiement d’un modèle.

> [!div class="nextstepaction"]
> [Tutoriel : créer, entraîner et déployer](tutorial-train-models-with-aml.md)