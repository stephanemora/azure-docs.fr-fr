---
title: Gérer des environnements dans ML Studio (préversion)
titleSuffix: Azure Machine Learning
description: Apprenez à créer et gérer des environnements dans Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 5/25/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 8015152a7888df86128aaff31d4b8c6dd9179cf2
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112460591"
---
# <a name="manage-software-environments-in-azure-machine-learning-studio-preview"></a>Gérer des environnements logiciels dans Azure Machine Learning Studio (préversion)

Cet article explique comment créer et gérer des [environnements](/python/api/azureml-core/azureml.core.environment.environment) Azure Machine Learning dans Azure Machine Learning Studio. Utilisez les environnements pour assurer le suivi et la reproduction des dépendances logicielles de vos projets au fil de leur évolution.

Les exemples présentés de cet article montrent comment :

* Parcourir des environnements organisés
* Créer un environnement et spécifier des dépendances de package
* Modifier une spécification d'environnement existante et ses propriétés
* Régénérer un environnement et afficher les journaux de génération d'images

Pour une vue d’ensemble du fonctionnement des environnements dans Azure Machine Learning, consultez [Présentation des environnements ML](concept-environments.md). Pour plus d'informations, consultez [Configurer un environnement de développement pour Azure Machine Learning](how-to-configure-environment.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md)

## <a name="browse-curated-environments"></a>Parcourir des environnements organisés

Les environnements organisés contiennent des collections de packages Python et sont disponibles dans votre espace de travail par défaut. Ces environnements s’appuient sur des images Docker mises en cache, ce qui réduit le coût de préparation de l’exécution. 

Cliquez sur un environnement pour afficher des informations détaillées sur son contenu. Pour plus d’informations, consultez [Environnements organisés Azure Machine Learning](resource-curated-environments.md). 

## <a name="create-an-environment"></a>Créer un environnement

Pour créer un environnement :
1. Dans [Azure Machine Learning Studio](https://ml.azure.com), ouvrez votre espace de travail.
1. Sur la gauche, sélectionnez **Environnements**.
1. Sélectionnez l'onglet **Environnements personnalisés**. 
1. Cliquez sur le bouton **Créer**. 

Créez un environnement en spécifiant l'un des éléments suivants :
* [Fichier](https://pip.pypa.io/en/stable/cli/pip_install) de spécifications pip
* [Fichier](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) YAML Conda
* [Image](https://hub.docker.com/search?q=&type=image) Docker
* [Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

:::image type="content" source="media/how-to-manage-environments-in-studio/create-page.jpg" alt-text="Assistant de création d'un environnement":::

Vous pouvez personnaliser le fichier de configuration, ajouter des balises et des descriptions, et examiner les propriétés avant de créer l'entité. 

Si un nouvel environnement porte le même nom qu'un environnement existant dans l'espace de travail, une nouvelle version de l'environnement existant est créée.

## <a name="view-and-edit-environment-details"></a>Afficher et modifier les détails de l'environnement

Une fois l'environnement créé, cliquez sur son nom pour afficher les détails de celui-ci. Utilisez le menu déroulant pour sélectionner différentes versions de l'environnement. Vous pouvez ici afficher les métadonnées et le contenu de l’environnement par le biais de ses couches Docker et Conda. 

Cliquez sur les icônes en forme de crayon pour modifier les balises et les descriptions, ainsi que les fichiers de configuration ou l'image. Gardez à l'esprit que toute modification apportée aux sections Docker ou Conda crée une nouvelle version de l'environnement. 

:::image type="content" source="media/how-to-manage-environments-in-studio/details-page.jpg" alt-text="Page de détails sur les environnements":::

## <a name="view-image-build-logs"></a>Afficher les journaux de génération d'images

Cliquez sur l'onglet **Journal de génération** de la page de détails pour afficher les journaux de génération d'images d'une version d'environnement. 

## <a name="rebuild-an-environment"></a>Régénérer un environnement

Sur la page de détails, cliquez sur le bouton **Régénérer** pour régénérer l'environnement. Toute version d'un package non épinglée dans vos fichiers de configuration peut être mise à jour vers la version la plus récente grâce à cette action. 
