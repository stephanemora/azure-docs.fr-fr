---
title: Exemples de notebooks Jupyter
titleSuffix: Azure Machine Learning service
description: Recherchez des exemples de notebooks Jupyter pour explorer Azure Machine Learning service en Python.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6befe3a3fee80dd65fd3ac5be241c558707224e6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811092"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Utiliser des notebooks Jupyter pour explorer Azure Machine Learning service


Par souci pratique, nous avons développé une série de notebooks Jupyter Python que vous pouvez utiliser pour explorer Azure Machine Learning service. 

Découvrez comment utiliser le service en suivant la documentation de ce site et personnaliser les notebooks en fonction de vos besoins. 

## <a name="prerequisite"></a>Configuration requise

Suivez le [guide de démarrage rapide Azure Machine Learning Python](quickstart-get-started.md) pour créer un espace de travail et lancer Azure Notebooks.

## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Essayez Azure Notebooks : Notebooks Jupyter gratuits dans le cloud

La prise en main d’Azure Notebooks est simple. Le [kit SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) est déjà installé et configuré dans [Azure Notebooks](https://notebooks.azure.com/). L’installation et les futures mises à jour sont gérées automatiquement par le biais des services Azure.
  
+ Pour exécuter les **principaux notebooks du tutoriel** :
  1. Accédez à [Azure Notebooks](https://notebooks.azure.com/).
    
  1. Recherchez le dossier **Tutoriels** dans la bibliothèque **Bien démarrer** que vous avez créée comme l’un des prérequis dans le guide de démarrage rapide.
    
  1. Ouvrez le notebook que vous souhaitez exécuter.
    
+ Pour exécuter d’**autres notebooks** :

  1. [Importez des exemples de notebooks](https://aka.ms/aml-clone-azure-notebooks) dans Azure Notebooks.

  1. Ajoutez un fichier de configuration d’espace de travail à la bibliothèque à l’aide de l’une des méthodes suivantes :
     + Copiez le fichier **config.json** de la bibliothèque **Bien démarrer** dans la nouvelle bibliothèque clonée.

     + Créez un espace de travail à l’aide du code de [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
    
  1. Ouvrez le notebook que vous souhaitez exécuter.     


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Utiliser Data Science Virtual Machine (DSVM)

Le [SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) et le serveur de notebooks sont déjà installés et configurés dans une machine DSVM. Utilisez ces étapes pour exécuter les notebooks.

1. [Créez une machine DSVM](how-to-configure-environment.md#dsvm).

1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

1. Ajoutez un fichier de configuration d’espace de travail à la bibliothèque à l’aide de l’une des méthodes suivantes :
    * Copiez le fichier **aml_config\config.json** (que vous avez créé comme prérequis dans le guide de démarrage rapide) dans le répertoire cloné.

    * Créez un espace de travail à l’aide du code de [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Démarrez le serveur de blocs-notes de votre répertoire cloné.

## <a name="use-your-own-jupyter-notebook-server"></a>Utiliser votre propre serveur de notebooks Jupyter

Utilisez ces étapes pour créer une instance locale de serveur de notebooks Jupyter sur votre ordinateur.

1. Suivez l’intégralité du guide de démarrage rapide durant lequel vous avez installé les kits SDK Azure Machine Learning.

1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

1. Ajoutez un fichier de configuration d’espace de travail à la bibliothèque à l’aide de l’une des méthodes suivantes :
    * Copiez le fichier **aml_config\config.json** (que vous avez créé comme prérequis dans le guide de démarrage rapide) dans le répertoire cloné.
    
    * Créez un espace de travail à l’aide du code de [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Démarrez le serveur de blocs-notes de votre répertoire cloné.

1. Accédez au dossier qui contient le notebook.

1. Ouvrez le bloc-notes.

<a name="auto"></a>

## <a name="automated-ml-setup"></a>Installation du machine learning automatisé 

**Ces étapes s’appliquent uniquement aux notebooks présents dans le dossier `automated-machine-learning`.**

Vous pouvez utiliser l’une des options ci-dessus, ou bien installer l’environnement et créer un espace de travail simultanément, en suivant les instructions ci-dessous. 

1. Installez [Mini-conda](https://conda.io/miniconda.html). Choisissez la version 3.7 ou ultérieure. Suivez les invites pour procéder à l’installation. 
   >[!NOTE]
   >Vous pouvez utiliser une installation existante de conda, du moment que sa version est égale ou supérieure à 4.4.10. Pour afficher la version, utilisez `conda -V`. Vous pouvez mettre à jour une version de conda avec la commande `conda update conda`. Il n’est pas nécessaire d’installer mini-conda.

1. Téléchargez les exemples de notebooks [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) sous la forme d’un fichier zip, puis extrayez le contenu dans un répertoire local. Les notebooks du machine learning automatisé se trouvent dans le dossier `how-to-use-azureml/automated-machine-learning`.

1. Configurez un nouvel environnement Conda. 
   1. Ouvrez une invite Conda sur votre ordinateur local.
   
   1. Accédez aux fichiers que vous avez extraits sur votre ordinateur local.
   
   1. Ouvrez le dossier `automated-machine-learning`.
   
   1. Exécutez `automl_setup.cmd` dans l’invite Conda pour Windows, ou le fichier `.sh` pour votre système d’exploitation. L’exécution dure environ 10 minutes.

      Le script d’installation :
      + Crée un nouvel environnement Conda
      + Installe les packages nécessaires
      + Configure le widget
      + Démarre un notebook Jupyter
      
   >[!NOTE]
   > Le script prend le nom de l’environnement Conda comme paramètre facultatif. Par défaut, le nom de l’environnement Conda est `azure_automl`. La commande exacte varie selon le système d’exploitation. C’est utile si vous créez un nouvel environnement ou si vous faites une mise à niveau vers une nouvelle version. Par exemple, vous pouvez utiliser « automl_setup.cmd azure_automl_sandbox » pour créer un environnement nommé « azure_automl_sandbox ». 
      
1. Une fois l’exécution du script terminée, vous voyez la page d’accueil Jupyter Notebook dans votre navigateur.

1. Accédez à l’emplacement où vous avez enregistré les notebooks. 

1. Ouvrez le dossier automated-machine-learning, puis ouvrez le notebook `configuration.ipynb`. 

1. Exécutez les cellules du notebook pour inscrire le fournisseur de ressources Machine Learning Services et créer un espace de travail.

Vous êtes maintenant prêt à exécuter les notebooks enregistrés sur votre ordinateur local.


## <a name="next-steps"></a>Étapes suivantes

Explorez le [dépôt de notebooks GitHub pour Azure Machine Learning service](https://aka.ms/aml-notebooks).

Essayez ces tutoriels :
+ [Entraîner un modèle de classification d’images avec MNIST](tutorial-train-models-with-aml.md)

+ [Préparer des données et utiliser le machine learning automatisé pour entraîner un modèle de régression avec le jeu de données NYC Taxi](tutorial-data-prep.md)
