---
title: Exemples de notebooks Jupyter
titleSuffix: Azure Machine Learning service
description: Recherchez des exemples de notebooks Jupyter pour explorer Azure Machine Learning service en Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035366"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Utiliser des notebooks Jupyter pour explorer Azure Machine Learning service

Par souci pratique, nous avons développé une série de notebooks Jupyter Python que vous pouvez utiliser pour explorer Azure Machine Learning service. 

Découvrez comment utiliser le service en suivant la documentation de ce site et personnaliser les notebooks en fonction de vos besoins. 

Utilisez l’un des chemins ci-dessous pour exécuter un serveur de notebooks avec ces exemples de notebooks.  Une fois que le serveur est en cours d’exécution, recherchez des notebooks de tutoriels dans le dossier des **tutoriels** ou explorez les différentes fonctionnalités dans le dossier **how-to-utilisation-azureml**.

## <a name="a-managed-cloud-notebook-server"></a>Un serveur de notebooks cloud managé

Il est facile de bien démarrer avec votre propre serveur de notebooks basé sur le cloud. L’exemple de notebook et le [kit SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) sont déjà installés et configurés pour vous une fois que vous avez créé cette ressource cloud.  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Les exemples sont disponibles sur la page web du notebook.

## <a name="a-data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

Le [SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) et le serveur de notebooks sont déjà installés et configurés dans une machine DSVM. 

Après avoir [créé une machine DSVM](how-to-configure-environment.md#dsvm), effectuez les étapes suivantes sur la machine DSVM pour exécuter les notebooks.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>Votre propre serveur de notebooks Jupyter

Utilisez ces étapes pour créer une instance locale de serveur de notebooks Jupyter sur votre ordinateur.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Les instructions de configuration permettent d’installer les packages nécessaires pour exécuter les notebooks de démarrage rapide et de tutoriel.  D’autres exemples de notebooks peuvent nécessiter l’installation de composants supplémentaires.  Pour plus d’informations sur ces composants, consultez [Installer le kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="azure-notebooks"></a>Azure Notebooks

Les exemples de notebooks et le [kit SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) sont déjà installés et configurés pour vous dans [Azure Notebooks](https://notebooks.azure.com/). L’installation et les futures mises à jour sont gérées automatiquement par le biais des services Azure.

Utilisez le [portail Azure](https://portal.azure.com) pour bien démarrer avec Azure Notebooks.  Ouvrez votre espace de travail et, dans la section **Vue d’ensemble**, sélectionnez **Bien démarrer dans Azure Notebooks**.

## <a name="next-steps"></a>Étapes suivantes

+ Explorez les exemples de notebooks pour le service Azure Machine Learning dans ce dépôt GitHub : https://aka.ms/aml-notebooks

Essayez ces tutoriels :
+ [Entraîner un modèle de classification d’images avec MNIST](tutorial-train-models-with-aml.md)

+ [Préparer des données et utiliser le machine learning automatisé pour entraîner un modèle de régression avec le jeu de données NYC Taxi](tutorial-data-prep.md)