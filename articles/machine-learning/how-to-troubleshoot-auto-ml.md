---
title: Résoudre les problèmes d’expériences de ML automatisé
titleSuffix: Azure Machine Learning
description: Découvrez comment résoudre les problèmes rencontrés dans vos expériences de machine learning automatisé.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: 28aac830326d60161f54d7ad5fa03326c1d66462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563672"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Résoudre les problèmes d’expériences de ML automatisé

Dans ce guide, découvrez comment identifier et résoudre les problèmes connus dans vos expériences de machine learning automatisé avec le kit [SDK Azure Machine Learning](/python/api/overview/azure/ml/intro).

## <a name="version-dependencies"></a>Dépendances de versions

Les dépendances **`AutoML` vis-à-vis des versions de package récentes cassent la compatibilité**. Après la version 1.13.0 du SDK, les modèles ne sont pas chargés dans les anciens SDK en raison d’une incompatibilité entre les anciennes versions épinglées dans les packages `AutoML` précédents et les versions récentes épinglées d’aujourd’hui.

Vous pouvez vous attendre à obtenir diverses erreurs, à savoir :

* Des erreurs de type « Module introuvable », telles que

  `No module named 'sklearn.decomposition._truncated_svd`

* Des erreurs d’importation, telles que

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Des erreurs d’attribut, telles que

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

La résolution dépend de votre version de formation du SDK `AutoML` :

* Si votre version de formation du kit de développement logiciel (SDK) `AutoML` est supérieure à 1.13.0, vous avez besoin de `pandas == 0.25.1` et de `scikit-learn==0.22.1`.

    * En cas d’incompatibilité de version, mettez à niveau scikit-learn et/ou pandas pour corriger la version comme suit :

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* Si votre version de formation du kit de développement logiciel (SDK) `AutoML` est inférieure ou égale à 1.12.0, vous avez besoin de `pandas == 0.23.4` et de `sckit-learn==0.20.3`.
  * En cas d’incompatibilité de version, passez à une version antérieure appropriée de scikit-learn et/ou de pandas comme suit :
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Programme d’installation

Les modifications dont a fait l’objet le package `AutoML` depuis la version 1.0.76 nécessitent la désinstallation de la version précédente avant de passer à la nouvelle.

* **`ImportError: cannot import name AutoMLConfig`**

    Si vous rencontrez cette erreur après une mise à niveau à partir d’une version du SDK comprise entre v1.0.76 et v1.0.76 ou une version ultérieure, résolvez l’erreur en exécutant `pip uninstall azureml-train automl` puis `pip install azureml-train-automl`. Le script automl_setup.cmd le fait automatiquement.

* **Échec d’automl_setup**

  * Sur Windows, exécutez automl_setup à partir d’une invite Anaconda. [Installez Miniconda](https://docs.conda.io/en/latest/miniconda.html).

  * Vérifiez que la version 4.4.10 ou ultérieure de conda 64 bits est installée. Vous pouvez vérifier le bit avec la commande `conda info`. La `platform` doit être `win-64` pour Windows ou `osx-64` pour Mac. Pour vérifier la version, utilisez la commande `conda -V`. Si une version précédente est installée, vous pouvez la mettre à jour à l’aide de la commande : `conda update conda`. Pour vérifier la version 32 bits, exécutez 

  * Vérifiez que conda est installé. 

  * Linux – `gcc: error trying to exec 'cc1plus'`

    1. Si vous rencontrez l’erreur `gcc: error trying to exec 'cc1plus': execvp: No such file or directory`, installez les outils de génération GCC pour votre distribution Linux. Par exemple, sur Ubuntu, utilisez la commande `sudo apt-get install build-essential`.

    1. Transmettez un nouveau nom comme premier paramètre à automl_setup pour créer un nouvel environnement Conda. Affichez les environnements Conda existants à l’aide de `conda env list` et supprimez-les avec `conda env remove -n <environmentname>`.

* **Échec d’automl_setup_linux.sh** : Si automl_setup_linus.sh échoue sur Ubuntu Linux avec l’erreur : `unable to execute 'gcc': No such file or directory`

  1. Assurez-vous que les ports de sortie 53 et 80 sont activés. Sur une machine virtuelle Azure, vous pouvez le faire à partir du portail Azure en sélectionnant la machine virtuelle et en cliquant sur **Réseau**.
  1. Exécutez la commande `sudo apt-get update`
  1. Exécutez la commande `sudo apt-get install build-essential --fix-missing`
  1. Exécutez de nouveau `automl_setup_linux.sh`

* **Échec de configuration.ipynb**  :

  * Pour un environnement conda local, vérifiez d’abord que `automl_setup` s’est bien exécuté.
  * Assurez-vous que le paramètre subscription_id est correct. Recherchez le subscription_id dans le portail Azure en sélectionnant Tous les services, puis Abonnements. Les caractères « < » et « > » ne doivent pas être inclus dans la valeur subscription_id. Par exemple, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` a le format valide.
  * Vérifiez que le Contributeur ou le Propriétaire a accès à l’abonnement.
  * Vérifiez que la région est l’une des régions prises en charge : `eastus2`, `eastus`, `westcentralus`, `southeastasia`, `westeurope`, `australiaeast`, `westus2`, `southcentralus`.
  * Vérifiez l’accès à la région à l’aide du portail Azure.
  
* **Échec de workspace.from_config** :

  Si l’appel `ws = Workspace.from_config()` échoue :

  1. Assurez-vous que le notebook configuration.ipynb s’est exécuté correctement.
  1. Si le notebook est exécuté à partir d’un dossier qui ne se trouve pas sous le dossier dans lequel `configuration.ipynb` a été exécuté, copiez le dossier aml_config et le fichier config.json qu’il contient dans le nouveau dossier. Workspace.from_config lit le fichier config.json pour le dossier du notebook ou son dossier parent.
  1. Si vous utilisez un nouvel abonnement, un nouveau groupe de ressources, un nouvel espace de travail ou une nouvelle région, veillez à exécuter à nouveau le notebook `configuration.ipynb`. La modification directe de config.json ne fonctionnera que si l’espace de travail existe déjà dans le groupe de ressources spécifié sous l’abonnement spécifié.
  1. Si vous souhaitez modifier la région, modifiez l’espace de travail, le groupe de ressources ou l’abonnement. `Workspace.create` ne crée pas ni ne met à jour un espace de travail s’il existe déjà, même si la région spécifiée est différente.

## <a name="tensorflow"></a>TensorFlow

Depuis la version 1.5.0 du Kit de développement logiciel (SDK), le Machine Learning automatisé n’installe pas de modèles TensorFlow par défaut. Pour installer TensorFlow et l’utiliser avec vos expériences de ML automatisé, installez `tensorflow==1.12.0` via `CondaDependencies`.

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Échecs de Numpy

* **`import numpy` échoue dans Windows** : Certains environnements Windows rencontrent une erreur lors du chargement de NumPy avec la dernière version de Python 3.6.8. Si vous rencontrez ce problème, essayez avec la version 3.6.7 de Python.

* **`import numpy` échoue**  : Vérifiez la version de TensorFlow dans l'environnement Conda de Machine Learning automatisé. Les versions prises en charge sont celles antérieures à la version 1.13. Désinstallez TensorFlow de l’environnement si la version est > = 1.13.

Vous pouvez vérifier la version de TensorFlow et la désinstaller comme suit :

  1. Démarrez un interpréteur de commandes, puis activez l’environnement Conda dans lequel les packages de ML automatisé sont installés.
  1. Entrez `pip freeze` et recherchez `tensorflow` : le cas échéant, la version indiquée doit être antérieure à la version 1.13.
  1. Si la version indiquée n’est pas une version prise en charge, exécutez `pip uninstall tensorflow` dans l’interpréteur de commandes et entrez « y » pour confirmer.

## `jwt.exceptions.DecodeError`

Message d’erreur exact : `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()`.

Pour les versions du SDK <= 1.17.0, l’installation peut aboutir à une version non prise en charge de PyJWT. Vérifiez que la version de PyJWT dans l’environnement conda de ml automatisé est une version prise en charge. Il doit s’agir d’une version de PyJWT < 2.0.0.

Pour vérifier la version de PyJWT, procédez comme suit :

1. Démarrez un interpréteur de commandes et activez l’environnement conda dans lequel les packages de ML automatisé sont installés.

1. Entrez `pip freeze` et recherchez `PyJWT` : le cas échéant, la version indiquée doit être antérieure à la version 2.0.0.

Si la version indiquée n'est pas prise en charge :

1. Envisagez une mise à niveau vers la dernière version du SDK AutoML : `pip install -U azureml-sdk[automl]`

1. Si cela se révèle impossible, désinstallez PyJWT de l'environnement et installez la version appropriée en procédant comme suit :

    1. `pip uninstall PyJWT` dans l'interpréteur de commandes et entrez `y` pour confirmer.
    1. Installez à l’aide de `pip install 'PyJWT<2.0.0'`.
  
## <a name="databricks"></a>Databricks
Consultez le [Guide pratique pour configurer une expérience de ML automatisé avec Databricks](how-to-configure-databricks-automl-environment.md#troubleshooting).

## <a name="forecasting-r2-score-is-always-zero"></a>Le score R2 de prévision est toujours égal à zéro

 Ce problème survient si les données d’apprentissage fournies contiennent des séries chronologiques contenant la même valeur pour les `n_cv_splits` + `forecasting_horizon` derniers points de données.

Si ce modèle est attendu dans votre série chronologique, vous pouvez faire passer votre métrique principale en **erreur quadratique moyenne normalisée**.

## <a name="failed-deployment"></a>Déploiement en échec

 Pour les versions <= 1.18.0 du SDK, l’image de base créée pour le déploiement peut échouer avec l’erreur suivante : `ImportError: cannot import name cached_property from werkzeug`.

  La procédure suivante peut permettre de contourner le problème :

  1. Téléchargez le package de modèle.
  1. Décompressez le package.
  1. Déployez à l’aide des ressources décompressées.

## <a name="azure-functions-application"></a>Application Azure Functions
  
  Le ML automatisé ne prend pas actuellement en charge les applications Azure Functions. 

## <a name="sample-notebook-failures"></a>Échecs d’exemple de notebook

 Si un exemple de notebook échoue avec une erreur indiquant que la propriété, la méthode ou la bibliothèque n'existe pas :

* Assurez-vous que le noyau approprié a été sélectionné dans Jupyter Notebook. Le noyau est affiché en haut à droite de la page du notebook. La valeur par défaut est *azure_automl*. Le noyau est enregistré dans le cadre du notebook. Si vous basculez vers un nouvel environnement conda, vous devez sélectionner le nouveau noyau dans le notebook.

  * Pour Azure Notebooks, il doit s’agir de Python 3.6.
  * Pour les environnements Conda locaux, il doit s'agir du nom de l'environnement Conda que vous avez spécifié dans automl_setup.

* Pour vérifier que le notebook convient pour la version du SDK que vous utilisez,
  * Vérifiez la version du SDK en exécutant `azureml.core.VERSION` dans une cellule Jupyter Notebook.
  * Vous pouvez télécharger la version précédente des exemples de notebook à partir de GitHub en effectuant ces étapes :
    1. Sélectionnez le bouton `Branch`.
    1. Accédez à l’onglet `Tags`.
    1. Sélectionnez la version.

## <a name="next-steps"></a>Étapes suivantes

+ Découvrez plus d’informations sur [comment entraîner un modèle de régression avec le machine learning automatisé](tutorial-auto-train-models.md) ou [comment entraîner avec le machine learning automatisé sur une ressource distante](how-to-auto-train-remote.md).

+ Découvrez plus d’informations sur [comment et où déployer un modèle](how-to-deploy-and-where.md).
