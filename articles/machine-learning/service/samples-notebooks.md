---
title: Exemples de notebooks Jupyter
titleSuffix: Azure Machine Learning service
description: Recherchez des exemples de notebooks Jupyter pour explorer le SDK Python d’Azure Machine Learning Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 05/29/2019
ms.custom: seodec18
ms.openlocfilehash: ea4d5a807c25ea0406b49dac8a83ef1a34e0e8b3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391790"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Utiliser des notebooks Jupyter pour explorer Azure Machine Learning service

Le [dépôt Azure Machine Learning Notebooks](https://github.com/azure/machinelearningnotebooks) contient les derniers exemples du SDK Python Azure Machine Learning. Ces notebooks Jupyter sont conçus pour vous aider à explorer le SDK et servent de modèles pour vos propres projets Machine Learning.

Cet article vous montre comment accéder au dépôt dans les environnements suivants :

- [Machine virtuelle Notebook Azure Machine Learning](#azure-machine-learning-notebook-vm)
- [Utiliser son propre serveur de notebooks](#bring-your-own-jupyter-notebook-server)
- [Data Science Virtual Machine](#data-science-virtual-machine) (Machine virtuelle Science des données)
- [Azure Notebooks](#azure-notebooks)

> [!NOTE]
> Une fois que vous avez cloné le dépôt, vous voyez que des notebooks de tutoriel se trouvent dans le dossier **tutorials**, et que des notebooks relatifs à certaines fonctionnalités se trouvent dans le dossier **how-to-use-azureml**.

## <a name="azure-machine-learning-notebook-vm"></a>Machine virtuelle Notebook Azure Machine Learning

Pour bien démarrer avec les exemples, le plus simple est de suivre le [guide de démarrage rapide des notebooks cloud](quickstart-run-cloud-notebook.md). Une fois que vous l’aurez suivi, vous disposerez d’un serveur de notebook dédié dans lequel auront été préchargés le SDK et l’exemple de dépôt. Aucune installation ni aucun téléchargement ne sont nécessaires.

## <a name="bring-your-own-jupyter-notebook-server"></a>Utiliser votre propre serveur de notebooks Jupyter

Si vous souhaitez utiliser votre propre serveur de notebooks pour le développement local, effectuez les étapes suivantes :

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Ces instructions permettent d’installer les packages de base du SDK qui sont nécessaires pour le guide de démarrage rapide et les notebooks du tutoriel. Les autres exemples de notebooks peuvent nécessiter l’installation de composants supplémentaires. Pour plus d’informations, consultez [Installer le kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="data-science-virtual-machine"></a>Machine virtuelle de science des données

La machine virtuelle DSVM (Data Science Virtual Machine) est une image de machine virtuelle personnalisée spécialement conçue pour la science des données. Lorsque vous [créez une machine virtuelle DSVM](how-to-configure-environment.md#dsvm), le SDK et le serveur de notebooks sont installés et configurés automatiquement. Toutefois, vous devrez créer un espace de travail et cloner l’exemple de dépôt.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="azure-notebooks"></a>Azure Notebooks

Dans [Azure Notebooks](https://notebooks.azure.com/), le SDK et le serveur de notebooks sont installés et configurés automatiquement. Azure Notebooks fournit un environnement de notebook léger et complètement managé.

Pour accéder à l’exemple de dépôt dans Azure Notebooks, accédez à votre espace de travail Azure Machine Learning via le [portail Azure](https://portal.azure.com). Dans la section **Vue d’ensemble**, sélectionnez **Bien démarrer dans Azure Notebooks**.

## <a name="next-steps"></a>Étapes suivantes

Explorez les [exemples de notebooks](https://aka.ms/aml-notebooks) pour voir ce que permet de faire Azure Machine Learning Service, ou essayez l’un de ces tutoriels :

- [Entraîner un modèle de classification d’images avec MNIST](tutorial-train-models-with-aml.md)

- [Préparer des données et utiliser le machine learning automatisé pour entraîner un modèle de régression avec le jeu de données NYC Taxi](tutorial-data-prep.md)