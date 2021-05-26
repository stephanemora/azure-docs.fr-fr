---
title: Étendre l’image Docker prédéfinie
titleSuffix: Azure Machine Learning
description: Étendre les images Docker prédéfinies dans Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 2c410d6c310c8f9b0c50ff0073f4dda9a35c8f9c
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382540"
---
# <a name="extend-a-prebuilt-docker-image-preview"></a>Étendre une image Docker prédéfinie (préversion)

Il peut arriver dans certains cas que les [images Docker prédéfinies pour des solutions d’inférence](concept-prebuilt-docker-images-inference.md) et d’[extensibilité](./how-to-prebuilt-docker-images-inference-python-extensibility.md) de modèles pour Azure Machine Learning ne répondent pas à vos besoins de service d’inférence.

Vous pouvez alors utiliser un Dockerfile pour créer une nouvelle image en utilisant une des images prédéfinies comme point de départ. L’extension d’une image Docker prédéfinie existante vous permet d’utiliser la pile réseau et les bibliothèques d’Azure Machine Learning sans avoir à créer entièrement une image.

> [!IMPORTANT]
> Pour l’heure, l’utilisation d’images Docker prédéfinies avec Azure Machine Learning est disponible en préversion. La fonctionnalité en préversion est fournie en l’état, sans garantie de support ni contrat de niveau de service. Pour plus d’informations, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Avantages et inconvénients**

L’utilisation d’un Dockerfile autorise une personnalisation complète de l’image avant le déploiement. Elle offre un contrôle maximal sur les dépendances ou les variables d’environnement, entre autres choses, qui sont définies dans le conteneur.

Le principal inconvénient de cette approche est qu’une image supplémentaire doit être générée pendant le déploiement, ce qui a pour effet de ralentir le processus de déploiement. Si vous pouvez utiliser la méthode d’[extensibilité de package Python](./how-to-prebuilt-docker-images-inference-python-extensibility.md), le déploiement sera plus rapide.
## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Pour obtenir un tutoriel de création d’espace de travail, consultez [Bien démarrer avec Azure Machine Learning](quickstart-create-resources.md).
* Aptitude à créer un [Dockerfile](https://docs.docker.com/engine/reference/builder/).
* Une installation opérationnelle locale de [Docker](https://www.docker.com/), avec l’interface CLI `docker` **OU** un ACR (Azure Container Registry) associé à votre espace de travail Azure Machine Learning.

    > [!WARNING]
    > Le registre Azure Container Registry pour votre espace de travail est créé la première fois que vous entraînez ou déployez un modèle à l’aide de l’espace de travail. Si vous avez créé un espace de travail, mais sans entraîner ni créer un modèle, aucun registre Azure Container Registry n’existe pour l’espace de travail.
## <a name="create-and-build-dockerfile"></a>Créer et générer un Dockerfile

Vous trouverez ci-dessous un exemple de Dockerfile qui utilise une image Docker prédéfinie Azure Machine Learning comme image de base :

```Dockerfile
FROM mcr.microsoft.com/azureml/<image_name>:<tag>

COPY requirements.txt /tmp/requirements.txt

RUN pip install –r /tmp/requirements.txt
```

Placez ensuite le Dockerfile précédent dans le répertoire avec tous les fichiers nécessaires, puis exécutez la commande suivante pour générer l’image :

```bash
docker build -f <above dockerfile> -t <image_name>:<tag> .
```

> [!TIP]
> Vous trouverez des détails complémentaires sur `docker build` dans la [documentation Docker](https://docs.docker.com/engine/reference/commandline/build/).

Si la commande `docker build` n’est pas disponible localement, utilisez l’ACR (Azure Container Registry) de votre espace de travail Azure Machine Learning pour générer l’image Docker dans le cloud. Pour plus d’informations, consultez [Tutoriel : Générer et déployer des images conteneur avec Azure Container Registry](/azure/container-registry/container-registry-tutorial-quick-task).

> [!IMPORTANT]
> Microsoft vous recommande de vérifier dans un premier temps que le Dockerfile fonctionne localement avant d’essayer de créer une image de base personnalisée via Azure Container Registry.

Les sections suivantes contiennent des informations plus détaillées sur le Dockerfile.

## <a name="install-extra-packages"></a>Installer des packages supplémentaires

Si vous avez besoin d’installer d’autres package `apt` dans le conteneur Ubuntu, vous pouvez les ajouter dans le Dockerfile. L’exemple suivant montre comment la commande `apt-get` est utilisée à partir d’un Dockerfile :

```Dockerfile
FROM <prebuilt docker image from MCR>

# Switch to root to install apt packages
USER root:root

RUN apt-get update && \
    apt-get install -y \
    <package-1> \
    ... 
    <package-n> && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# Switch back to non-root user
USER dockeruser
```

Vous pouvez aussi installer des packages pip supplémentaires à partir d’un Dockerfile. L’exemple suivant montre comment utiliser `pip install` :

```Dockerfile
RUN pip install <library>
```

<a id="buildmodel"></a>

## <a name="build-model-and-code-into-images"></a>Générer un modèle et du code dans des images

Si le modèle et le code doivent être intégrés à l’image, les variables d’environnement suivantes doivent être définies dans le Dockerfile :

* `AZUREML_ENTRY_SCRIPT` : script d’entrée de votre code. Ce fichier contient les méthodes `init()` et `run()`.
* `AZUREML_MODEL_DIR` : répertoire qui contient le ou les fichiers de modèle. Le script d’entrée doit utiliser ce répertoire comme répertoire racine du modèle.

L’exemple suivant montre comment ces variables d’environnement sont définies dans le Dockerfile :

```Dockerfile
FROM <prebuilt docker image from MCR>

# Code
COPY <local_code_directory> /var/azureml-app
ENV AZUREML_ENTRY_SCRIPT=<entryscript_file_name>

# Model
COPY <model_directory> /var/azureml-app/azureml-models
ENV AZUREML_MODEL_DIR=/var/azureml-app/azureml-models
```

## <a name="example-dockerfile"></a>Exemple de Dockerfile

L’exemple suivant montre comment installer des packages `apt`, définir des variables d’environnement et inclure du code et des modèles dans le Dockerfile :

```Dockerfile
FROM mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest 

USER root:root

# Install libpng-tools and opencv
RUN apt-get update && \
    apt-get install -y \
    libpng-tools \
    python3-opencv && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# Switch back to non-root user
USER dockeruser

# Code
COPY code /var/azureml-app
ENV AZUREML_ENTRY_SCRIPT=score.py

# Model
COPY model /var/azureml-app/azureml-models
ENV AZUREML_MODEL_DIR=/var/azureml-app/azureml-models
```

## <a name="next-steps"></a>Étapes suivantes

Pour utiliser un Dockerfile avec le kit SDK Python d’Azure Machine Learning, consultez les documents suivants :

* [Utiliser votre propre Dockerfile](how-to-use-environments.md#use-your-own-dockerfile)
* [Utiliser une image Docker prédéfinie et créer une image de base personnalisée](how-to-use-environments.md#use-a-prebuilt-docker-image)

Pour plus d’informations sur le déploiement d’un modèle, consultez [Guide pratique pour déployer un modèle](how-to-deploy-and-where.md).

Pour savoir comment résoudre les problèmes de déploiement d’images Docker prédéfinies, consultez [Guide pratique pour résoudre les problèmes de déploiement d’images Docker prédéfinies](how-to-troubleshoot-prebuilt-docker-image-inference.md).