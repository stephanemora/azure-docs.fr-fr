---
title: Extension CLI Machine Learning
titleSuffix: Azure Machine Learning service
description: Découvrez l’extension CLI Azure Machine Learning pour l'interface de ligne de commande Azure. L’interface de ligne de commande Azure est un utilitaire de ligne de commande multiplateforme qui vous permet d'utiliser des ressources du cloud Azure. Grâce à l'extension de Machine Learning, vous pouvez utiliser le service Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 9cc6ad4f7b33de4d132efe63ff11c34f10b614af
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023379"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Utiliser l’extension CLI pour le service Azure Machine Learning

L'interface CLI Azure Machine Learning est une extension pour l'[interface Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), une interface de ligne de commande multiplateforme pour la plateforme Azure. Cette extension fournit les commandes à utiliser avec le service Azure Machine Learning à partir de la ligne de commande. Il vous permet d’automatiser votre flux de travail d’apprentissage. Par exemple, vous pouvez effectuer les actions suivantes :

+ Exécuter des expérimentations pour créer des modèles Machine Learning

+ Inscrire des modèles Machine Learning pour l’usage des clients

+ Empaqueter, déployer et suivre le cycle de vie de vos modèles Machine Learning

L’interface CLI ne remplace en rien le kit de développement logiciel (SDK) Azure. Il est un outil complémentaire qui est optimisé pour gérer des tâches hautement paramétrables adaptées à eux-mêmes à l’automation.

## <a name="prerequisites"></a>Conditions préalables

* Pour utiliser l'interface de ligne de commande, vous devez disposer d'un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante de Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Installer l’extension

Pour installer l'extension d'interface de ligne de commande Machine Learning, utilisez la commande suivante :

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Fichiers d’exemple que vous pouvez utiliser les commandes ci-dessous se trouvent [ici](http://aka.ms/azml-deploy-cloud).

Lorsque vous y êtes invité, sélectionnez `y` pour installer l’extension.

Pour vérifier que l’extension a été installée, utilisez la commande suivante afin d'afficher la liste des sous-commandes spécifiques à ML :

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>Supprimer l’extension

Pour supprimer l'extension CLI, utilisez la commande suivante :

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gestion des ressources

Les commandes suivantes montrent comment utiliser l'interface de ligne de commande pour gérer les ressources utilisées par Azure Machine Learning.

+ Si vous n’en avez pas déjà, créez un groupe de ressources :

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Créer un espace de travail pour le service Azure Machine Learning :

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

+ Attacher une configuration de l’espace de travail dans un dossier pour activer la prise en compte contextuelle CLI.
    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

+ Attacher un conteneur d’objets blob Azure comme banque de données.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```
    
+ Joindre un cluster AKS comme cible de calcul.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a id="experiments"></a>Exécuter des expériences

* Exécutez votre expérimentation. Lorsque vous utilisez cette commande, spécifiez le nom du fichier runconfig (le texte avant \*.runconfig si vous cherchez à votre système de fichiers) avec le paramètre - c.

    ```azurecli-interactive
    az ml run submit-script -c local -e testexperiment train.py
    ```

* Afficher la liste des expériences :

    ```azurecli-interactive
    az ml experiment list
    ```

## <a name="model-registration-profiling--deployment"></a>L’inscription de modèle, de profilage et de déploiement

Les commandes suivantes montrent comment inscrire un modèle entraîné, puis le déployer en tant que service de production :

+ Inscrivez un modèle dans Azure Machine Learning :

  ```azurecli-interactive
  az ml model register -n mymodel -p sklearn_regression_model.pkl
  ```

+ Déployez votre modèle sur AKS

  ```azurecli-interactive
  az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
  ```
