---
title: Intégration de Git pour Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez comment Azure Machine Learning intègre un dépôt Git local. Lors de l’envoi d’une exécution d’entraînement à partir d’un répertoire local, qui est un dépôt Git, les informations relatives au dépôt, à la branche et à la validation actuelle sont suivies dans le cadre de l’exécution.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402826"
---
# <a name="git-integration-for-azure-machine-learning"></a>Intégration de Git pour Azure Machine Learning

[Git](https://git-scm.com/) est un système de gestion de versions connu qui vous permet de partager vos projets et de collaborer. 

Azure Machine Learning prend entièrement en charge les dépôts Git pour le suivi du travail. Vous pouvez cloner des dépôts directement dans le système de fichiers de votre espace de travail partagé, ou utiliser Git sur votre station de travail locale ou à partir d’un pipeline CI/CD.

Lors de la soumission d’un travail à Azure Machine Learning, si les fichiers sources sont stockés dans un dépôt Git local, les informations sur le dépôt font l’objet d’un suivi dans le cadre du processus d’entraînement.

Étant donné qu’Azure Machine Learning effectue le suivi des informations à partir d’un dépôt Git local, il n’est lié à aucun dépôt central spécifique. Votre dépôt peut être cloné à partir de GitHub, GitLab, Bitbucket, Azure DevOps ou de tout autre service compatible Git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Cloner des dépôts Git dans le système de fichiers de votre espace de travail
Azure Machine Learning fournit un système de fichiers partagé pour tous les utilisateurs de l’espace de travail.
Pour cloner un dépôt Git dans ce partage de fichiers, nous vous recommandons de créer une instance de calcul et d’ouvrir un terminal.
Une fois le terminal ouvert, vous avez accès à un client Git complet et pouvez cloner et utiliser Git par le biais de l’interface CLI Git.

Nous vous recommandons de cloner le dépôt dans le répertoire de vos utilisateurs afin que d’autres ne puissent pas créer de collisions directement sur votre branche de travail.

Vous pouvez cloner n’importe quel dépôt Git auprès duquel vous pouvez vous authentifier (GitHub, Azure Repos, BitBucket, etc.)

Pour obtenir un guide sur l’utilisation de l’interface CLI Git, consultez [cette page](https://guides.github.com/introduction/git-handbook/).

## <a name="track-code-that-comes-from-git-repositories"></a>Suivre le code provenant de dépôts Git

Lorsque vous soumettez une exécution d’entraînement à partir du kit SDK Python ou de l’interface CLI de Machine Learning, les fichiers nécessaires à l’entraînement du modèle sont chargés dans votre espace de travail. Si la commande `git` est disponible dans votre environnement de développement, le processus de chargement l’utilise pour vérifier si les fichiers sont stockés dans un dépôt Git. Si tel est le cas, les informations de votre dépôt Git sont également chargées lors de l’exécution d’entraînement. Ces informations sont stockées dans les propriétés suivantes pour l’exécution d’entraînement :

| Propriété | Commande Git utilisée pour récupérer la valeur | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | URI à partir duquel votre dépôt a été cloné. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | URI à partir duquel votre dépôt a été cloné. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Branche active lorsque l’exécution a été envoyée. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Branche active lorsque l’exécution a été envoyée. |
| `azureml.git.commit` | `git rev-parse HEAD` | Hachage de validation du code qui a été envoyé pour l’exécution. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Hachage de validation du code qui a été envoyé pour l’exécution. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, si l’intégrité de la validation/branche est compromise ; sinon, `false`. |

Ces informations sont envoyées pour les exécutions qui utilisent un estimateur, un pipeline Machine Learning ou une exécution de script.

Si vos fichiers d’entraînement ne se trouvent pas dans un dépôt Git dans votre environnement de développement, ou si la commande `git` n’est pas disponible, aucune information liée à Git ne fait l’objet d’un suivi.

> [!TIP]
> Pour vérifier si la commande git est disponible dans votre environnement de développement, ouvrez une session de l’interpréteur de commandes, une invite de commandes, PowerShell ou une autre interface de ligne de commande, puis saisissez la commande suivante :
>
> ```
> git --version
> ```
>
> Si elle est installée et dans le chemin d’accès, vous recevez une réponse similaire à `git version 2.4.1`. Pour plus d’informations sur l’installation de git dans votre environnement de développement, consultez le [site de Git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Voir les informations journalisées

Les informations Git sont stockées dans les propriétés d’une exécution d’entraînement. Vous pouvez voir ces informations en utilisant le portail Azure, le kit SDK Python et l’interface CLI. 

### <a name="azure-portal"></a>Portail Azure

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail.
1. Sélectionnez __Expériences__, puis sélectionnez l’une de vos expériences.
1. Sélectionnez l’une des exécutions dans la colonne __NUMÉRO D’EXÉCUTION__.
1. Sélectionnez __Journaux__, puis développez les entrées __logs__ et __azureml__. Sélectionnez le lien qui commence par __###\_azure__.

    ![Entrée ###_azure dans le portail](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

Les informations journalisées contiennent du texte similaire au code JSON suivant :

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Kit de développement logiciel (SDK) Python

Après l’envoi d’une exécution d’entraînement, un objet [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) est retourné. L’attribut `properties` de cet objet contient les informations Git journalisées. Par exemple, le code suivant récupère le hachage de validation :

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Interface de ligne de commande

Vous pouvez utiliser la commande CLI `az ml run` pour récupérer les propriétés d’une exécution. Par exemple, la commande suivante retourne les propriétés de la dernière exécution dans l’expérience nommée `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Pour plus d’informations, consultez la documentation de référence [az ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer et utiliser des cibles de calcul pour l’entraînement de modèles](how-to-set-up-training-targets.md)
