---
title: Données dans Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez comment Azure Machine Learning se connecte de manière sécurisée à vos données et les utilise pour les tâches de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128487"
---
# <a name="data-access-in-azure-machine-learning"></a>Accès aux données dans Azure Machine Learning

Azure Machine Learning facilite la connexion à vos données dans le cloud.  Il fournit une couche d’abstraction sur le service de stockage sous-jacent, ce qui vous permet d’accéder de manière sécurisée à vos données et de les utiliser sans avoir à écrire du code propre à votre type de stockage. Azure Machine Learning propose également les fonctionnalités de données suivantes :

*    Gestion de versions et suivi de la traçabilité des données
*    Étiquetage des données 
*    Supervision de la dérive des données
*    Interopérabilité avec les DataFrames Pandas et Spark

## <a name="data-workflow"></a>Workflow de données

Quand vous êtes prêt à utiliser les données dans votre solution de stockage cloud, nous vous recommandons de recourir au workflow de livraison de données suivant. Ce workflow suppose que vous disposez d’un [compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) et de données dans un service de stockage cloud au sein d’Azure. 

1. Créez un [magasin de données Azure Machine Learning](#datastores) pour stocker les informations de connexion à votre stockage Azure.

2. À partir de ce magasin de données, créez un [jeu de données Azure Machine Learning](#datasets) pour pointer vers un ou plusieurs fichiers spécifiques dans votre stockage sous-jacent. 

3. Pour utiliser ce jeu de données dans votre expérience machine learning, vous pouvez
    1. Le monter sur la cible de calcul de votre expérience pour l’entraînement du modèle.

        **OR** 

    1. Le consommer directement dans des solutions Azure Machine Learning, telles que des exécutions d’expériences de machine learning automatisé, des pipelines de Machine Learning ou le [Concepteur Azure Machine Learning](concept-designer.md).

4. Créez des [moniteurs de jeux de données](#data-drift) pour le jeu de données de sortie de votre modèle afin de détecter la dérive de données. 

5. Si une dérive de données est détectée, mettez à jour votre jeu de données d’entrée et renouvelez l’entraînement de votre modèle en conséquence.

Le diagramme suivant fournit une démonstration visuelle de ce workflow recommandé.

![Schéma de concept de données](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Magasins de données

Les magasins de données Azure Machine Learning conservent les informations de connexion à votre stockage Azure de manière sécurisée. Vous n’avez donc pas à les coder dans vos scripts. [Inscrivez et créez un magasin de données](how-to-access-data.md) pour vous connecter facilement à votre compte de stockage et accéder aux données de votre service de stockage Azure sous-jacent. 

Services de stockage cloud pris en charge dans Azure qui peuvent être enregistrés en tant que magasins de données :

+ Conteneur d’objets blob Azure
+ Partage de fichiers Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database pour PostgreSQL
+ Système de fichiers Databricks
+ Azure Database pour MySQL

## <a name="datasets"></a>Groupes de données

Les jeux de données Azure Machine Learning sont des références qui pointent vers les données de votre service de stockage. Comme il ne s’agit pas de copies de vos données, aucun coût de stockage supplémentaire n’est encouru. Pour interagir avec vos données dans le stockage, [créez un jeu de données](how-to-create-register-datasets.md) afin d’empaqueter vos données dans un objet consommable pour les tâches de machine learning. Inscrivez le jeu de données dans votre espace de travail pour le partager et le réutiliser dans différentes expériences sans avoir à gérer la complexité liée à l’ingestion des données.

Les jeux de données peuvent être créés à partir de fichiers locaux, d’URL publiques, d’[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) ou de services de stockage Azure par le biais de magasins de données. Pour créer un jeu de données à partir d’un dataframe pandas en mémoire, écrivez les données dans un fichier local (par exemple un fichier parquet), puis créez votre jeu de données à partir de ce fichier.  

Nous prenons en charge 2 types de jeux de données : 
+ Un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) représente les données sous forme de tableau en analysant le fichier ou la liste de fichiers fournis. Vous pouvez charger un TabularDataset dans un DataFrame Pandas ou Spark afin d’effectuer une manipulation et un nettoyage supplémentaires. Pour obtenir la liste complète des formats de données à partir desquels vous pouvez créer des TabularDatasets, consultez la [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

+ Un [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fait référence à des fichiers uniques ou multiples dans vos magasins de données ou vos URL publiques. Vous pouvez [télécharger ou monter des fichiers](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) référencés par des FileDatasets sur votre cible de calcul.

Vous trouverez des fonctionnalités supplémentaires des jeux de données dans la documentation suivante :

+ [Effectuez le suivi et la gestion des versions](how-to-version-track-datasets.md) de traçabilité des jeux de données.
+ [Supervisez votre jeu de données](how-to-monitor-datasets.md) pour faciliter la détection de la dérive de données.    

## <a name="work-with-your-data"></a>Travailler avec vos données

Avec les jeux de données, vous pouvez effectuer un certain nombre de tâches de Machine Learning grâce à une intégration fluide avec les fonctionnalités Azure Machine Learning. 

+ Créez un [projet d’étiquetage de données](#label).
+ Entraînez des modèles Machine Learning :
     + [expériences de ML automatisé](how-to-use-automated-ml-for-ml-models.md)
     + le [concepteur](tutorial-designer-automobile-price-train-score.md#import-data)
     + [notebooks](how-to-train-with-datasets.md)
     + [Pipelines Azure Machine Learning](how-to-create-your-first-pipeline.md)
+ Accédez aux jeux de données pour le scoring avec l’[inférence par lot](how-to-use-parallel-run-step.md) dans des [pipelines machine learning](how-to-create-your-first-pipeline.md).
+ Configurez un moniteur de jeu de données pour la détection de la [dérive de données](#drift).

<a name="label"></a>

## <a name="data-labeling"></a>Étiquetage des données

L’étiquetage des grandes quantités de données a souvent été un casse-tête dans les projets de Machine Learning. Ceux avec un composant de vision par ordinateur, tels que la classification d’images ou la détection d’objets, nécessitent généralement des milliers d’images et d’étiquettes correspondantes.

Azure Machine Learning vous fournit un emplacement central pour créer, gérer et superviser les projets d’étiquetage. Les projets d’étiquetage aident à coordonner les données, les étiquettes et les membres de l’équipe, ce qui vous permet de gérer plus efficacement les tâches d’étiquetage. Les tâches actuellement prises en charge sont la classification d’images, à plusieurs étiquettes ou multiclasse, et l’identification des objets à l’aide de zones délimitées.

Créez un [projet d’étiquetage des données](how-to-create-labeling-projects.md) et générez un jeu de données à utiliser dans des expériences Machine Learning.

<a name="drift"></a>

## <a name="data-drift"></a>Dérive de données

Dans le contexte de l’apprentissage automatique, la dérive de données est la modification des données d’entrée du modèle qui entraîne une dégradation des performances du modèle. C’est l’une des principales raisons pour lesquelles la précision du modèle se dégrade au fil du temps, de sorte que la surveillance de la dérive des données permet de détecter les problèmes de performance du modèle.

Pour en savoir plus sur la détection et l’alerte en cas de dérive de données dans un jeu de données, consultez l’article [Créer un moniteur de jeu de données](how-to-monitor-datasets.md).

## <a name="next-steps"></a>Étapes suivantes 

+ Créez un jeu de données dans Azure Machine Learning Studio ou avec le SDK Python [en suivant ces étapes](how-to-create-register-datasets.md).
+ Essayez des exemples d’entraînement de jeux de données avec nos [exemples de notebooks](https://aka.ms/dataset-tutorial).
+ Pour obtenir des exemples de dérive de données, consultez ce [tutoriel sur la dérive de données](https://aka.ms/datadrift-notebook).
