---
title: Données dans Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez comment Azure Machine Learning interagit avec vos données et comment celles-ci sont utilisées dans vos expériences Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: 4ea0f7092862ef910e9f44e81cc4f5f40849bc70
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74820471"
---
# <a name="data-access-in-azure-machine-learning"></a>Accès aux données dans Azure Machine Learning

Dans cet article, vous allez découvrir les solutions de gestion et d’intégration des données d’Azure Machine Learning pour vos tâches de Machine Learning. Cet article part du principe que vous avez déjà créé un [compte de stockage Azure](https://docs.microsoft.comazure/storage/common/storage-quickstart-create-account?tabs=azure-portal) et un [service de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction).

Quand vous êtes prêt à utiliser les données de votre stockage, nous vous recommandons de

1. Créer un magasin de données Azure Machine Learning.
2. À partir de ce magasin de données, créez un jeu de données Azure Machine Learning. 
3. Utilisez ce jeu de données dans votre expérience Machine Learning en 
    1. Le montant sur la cible de calcul de votre expérience pour l’entraînement du modèle.

        **OU** 

    1. Le consommant directement dans des solutions Azure Machine Learning, telles que des exécutions d’expériences de Machine Learning automatisé, des pipelines de Machine Learning et le [Concepteur Azure Machine Learning](concept-designer.md).
4. Créer des moniteurs de jeux de données pour les jeux de données d’entrée et de sortie de votre modèle afin de détecter la dérive de données. 
5. Si une dérive de données est détectée, mettez à jour votre jeu de données et réentraînez votre modèle en conséquence.

Le diagramme suivant fournit une démonstration visuelle de ce workflow d’accès aux données recommandé.

![Schéma de concept de données](media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>Accéder aux données dans le stockage

Pour accéder à vos données dans votre compte de stockage, Azure Machine Learning propose des magasins de données et des jeux de données. Les magasins de données fournissent une couche d’abstraction sur votre service de stockage. Cela contribue à la sécurité et à la facilité d’accès à votre stockage, car les informations de connexion sont conservées dans le magasin de données et ne sont pas exposées dans des scripts. Les jeux de données pointent vers les fichiers spécifiques dans votre stockage sous-jacent que vous souhaitez utiliser pour votre expérience de Machine Learning. Ensemble, les magasins de données et les jeux de données offrent un workflow de distribution de données sécurisé, scalable et reproductible pour vos tâches de Machine Learning.

### <a name="datastores"></a>Magasins de données

Un magasin de données Azure Machine Learning est une abstraction du stockage sur vos services de stockage Azure. [Inscrivez et créez un magasin de données](how-to-access-data.md) pour vous connecter facilement à votre compte de stockage Azure et accéder aux données de vos services de stockage Azure sous-jacents.

Services de stockage Azure pris en charge qui peuvent être enregistrés en tant que magasins de données :
+ Conteneur d’objets blob Azure
+ Partage de fichiers Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database pour PostgreSQL
+ Système de fichiers Databricks
+ Azure Database pour MySQL

### <a name="datasets"></a>Groupes de données

[Créez un jeu de données Azure Machine Learning](how-to-create-register-datasets.md) pour interagir avec les données de vos magasins de données et empaqueter vos données dans un objet consommable pour les tâches de Machine Learning. Inscrivez le jeu de données dans votre espace de travail pour le partager et le réutiliser dans différentes expériences sans avoir à gérer la complexité liée à l’ingestion des données.

Les jeux de données peuvent être créés à partir de fichiers locaux, d’URL publiques, d’[Azure Open Datasets](#open) ou de fichiers spécifiques dans vos magasins de données. Pour créer un jeu de données à partir d’un dataframe pandas en mémoire, écrivez les données dans un fichier local (par exemple un fichier csv), puis créez votre jeu de données à partir de ce fichier. Les jeux de données ne sont pas des copies de vos données, mais des références qui pointent vers les données dans votre service de stockage ; il n’y a donc aucun coût de stockage supplémentaire. 

Le diagramme suivant montre que si vous n’avez pas de service de stockage Azure, vous pouvez créer un jeu de données directement à partir de fichiers locaux, d’URL publiques ou d’un Azure Open Dataset. Cela permet de connecter votre jeu de données au magasin de données par défaut qui a été créé automatiquement avec l’[espace de travail Azure Machine Learning](concept-workspace.md) de votre expérience.

![Schéma de concept de données](media/concept-data/dataset-workflow.svg)

Vous trouverez des fonctionnalités supplémentaires des jeux de données dans la documentation suivante :

+ [Effectuez le suivi et la gestion des versions](how-to-version-track-datasets.md) de traçabilité des jeux de données.
+ [Supervisez votre jeu de données](how-to-monitor-datasets.md) pour faciliter la détection de la dérive de données.
+  Pour obtenir de la documentation sur les deux types de jeux de données, consultez les rubriques suivantes :
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) représente les données sous forme de tableau en analysant le fichier ou la liste de fichiers fournis. Cela vous permet de matérialiser les données dans un DataFrame Pandas ou Spark afin d’effectuer une manipulation et un nettoyage supplémentaires. Pour obtenir la liste complète des fichiers à partir desquels vous pouvez créer des TabularDatasets, consultez la [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

    + [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fait référence à des fichiers uniques ou multiples dans vos magasins de données ou vos URL publiques. Par cette méthode, vous pouvez télécharger ou monter des fichiers de votre choix vers votre cible de calcul en tant qu’objet FileDataset.

## <a name="work-with-your-data"></a>Travailler avec vos données

Avec les jeux de données, vous pouvez effectuer un certain nombre de tâches de Machine Learning grâce à une intégration fluide avec les fonctionnalités Azure Machine Learning. 

+ [Entraîner des modèles Machine Learning](how-to-train-with-datasets.md)
+ Consommer des jeux de données dans des 
     + [expériences de ML automatisé](how-to-create-portal-experiments.md)
     + le [concepteur](tutorial-designer-automobile-price-train-score.md#import-data) 
+ Accédez aux jeux de données pour la notation avec l’inférence par lot dans des [pipelines Machine Learning](how-to-create-your-first-pipeline.md).
+ Créez un [projet d’étiquetage de données](#label).
+ Configurez un moniteur de jeu de données pour la détection de la [dérive de données](#drift).

<a name="open"></a>

## <a name="azure-open-datasets"></a>Azure Open Datasets

Les jeux de données [Azure Open Datasets](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) sont des jeux de données publics organisés que vous pouvez utiliser pour ajouter des fonctionnalités spécifiques à des scénarios à des solutions de Machine Learning afin d'obtenir des modèles plus précis. Les Open Datasets se trouvent dans le cloud sur Microsoft Azure et sont intégrés à Azure Machine Learning. Vous pouvez également accéder aux jeux de données via des API et les utiliser dans d'autres produits, tels que Power BI et Azure Data Factory.

Les Azure Open Datasets englobent des données du domaine public portant sur la météo, le recensement, les jours fériés, la sécurité publique et la localisation, et vous aident à entraîner des modèles Machine Learning et à enrichir des solutions prédictives. Vous pouvez également partager vos jeux de données publics sur la plateforme Azure Open Datasets.

<a name="label"></a>

## <a name="data-labeling"></a>Étiquetage des données

L’étiquetage des grandes quantités de données a souvent été un casse-tête dans les projets de Machine Learning. Ceux avec un composant de vision par ordinateur, tels que la classification d’images ou la détection d’objets, nécessitent généralement des milliers d’images et d’étiquettes correspondantes.

Azure Machine Learning vous fournit un emplacement central pour créer, gérer et superviser les projets d’étiquetage. Les projets d’étiquetage aident à coordonner les données, les étiquettes et les membres de l’équipe, ce qui vous permet de gérer plus efficacement les tâches d’étiquetage. Les tâches actuellement prises en charge sont la classification d’images, à plusieurs étiquettes ou multiclasse, et l’identification des objets à l’aide de zones délimitées.

+ Créez un [projet d’étiquetage des données](how-to-create-labeling-projects.md) et générez un jeu de données à utiliser dans des expériences Machine Learning.

<a name="drift"></a>

## <a name="data-drift"></a>Dérive de données

Dans le contexte de l’apprentissage automatique, la dérive de données est la modification des données d’entrée du modèle qui entraîne une dégradation des performances du modèle. C’est l’une des principales raisons pour lesquelles la précision du modèle se dégrade au fil du temps, de sorte que la surveillance de la dérive des données permet de détecter les problèmes de performance du modèle.
Pour en savoir plus sur la détection et l’alerte en cas de dérive de données dans un jeu de données, consultez l’article [Créer un moniteur de jeu de données](how-to-monitor-datasets.md).

## <a name="next-steps"></a>Étapes suivantes 

+ Créez un jeu de données dans Azure Machine Learning Studio ou avec le SDK Python, [suivez ces étapes](how-to-create-register-datasets.md).
+ Essayez des exemples d’entraînement de jeux de données avec nos [exemples de notebooks](https://aka.ms/dataset-tutorial).
+ Pour obtenir des exemples de dérive de données, consultez ce [tutoriel sur la dérive de données](https://aka.ms/datadrift-notebook).