---
title: Extensibilité Python des images Docker prédéfinies
titleSuffix: Azure Machine Learning
description: Étendre des images Docker prédéfinies avec la solution d’extensibilité des packages Python
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 688c0fd03573737ee97084025251c9082a8cb915
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382619"
---
# <a name="python-package-extensibility-for-prebuilt-docker-images-preview"></a>Extensibilité des packages Python pour les images Docker prédéfinies (préversion)

Les [images Docker prédéfinies pour l’inférence de modèle](concept-prebuilt-docker-images-inference.md) contiennent des packages pour les frameworks de machine learning courants. Deux méthodes peuvent être utilisées pour ajouter des packages Python __sans regénérer l’image Docker__ :

* [Installation dynamique](#dynamic) : cette approche utilise un fichier de [spécifications](https://pip.pypa.io/en/stable/cli/pip_install/#requirements-file-format) pour restaurer automatiquement les packages Python lors du démarrage du conteneur Docker.

    Envisagez cette méthode __pour le prototypage rapide__. Quand l’image démarre, les packages sont restaurés en utilisant le fichier `requirements.txt`. Cette méthode augmente le temps de démarrage de l’image, et vous devez attendre plus longtemps pour que le déploiement puisse traiter les demandes.

* [Packages Python préinstallés](#preinstalled) : vous fournissez un répertoire contenant les packages Python préinstallés. Lors du déploiement, ce répertoire est monté dans le conteneur pour que votre script d’entrée (`score.py`) puisse l’utiliser.

    Utilisez cette approche __pour les déploiements de production__. Comme le répertoire contenant les packages est monté sur l’image, il peut être utilisé même si vos déploiements ne disposent pas d’un accès Internet public. Par exemple, c’est le cas lors du déploiement dans un réseau virtuel Azure sécurisé.

> [!IMPORTANT]
> L’utilisation d’images Docker prédéfinies avec Azure Machine Learning est actuellement en préversion. La fonctionnalité en préversion est fournie en l’état, sans garantie de support ni contrat de niveau de service. Pour plus d’informations, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Pour obtenir un tutoriel de création d’espace de travail, consultez [Bien démarrer avec Azure Machine Learning](quickstart-create-resources.md).
* Vous êtes familiarisé avec l’utilisation d’[environnements](how-to-use-environments.md) Azure Machine Learning.
* Vous savez [où et comment déployer des modèles](how-to-deploy-and-where.md) avec Azure Machine Learning.

<a id="dynamic"></a>

## <a name="dynamic-installation"></a>Installation dynamique

Cette approche utilise un fichier de [spécifications](https://pip.pypa.io/en/stable/cli/pip_install/#requirements-file-format) pour restaurer automatiquement les packages Python au démarrage de l’image.

Pour étendre votre image de conteneur Docker prédéfinie via un fichier requirements.txt, effectuez ces étapes :

1. Créez un fichier `requirements.txt` à côté de votre script `score.py`.
2. Ajoutez **tous** les packages nécessaires au fichier `requirements.txt`.
3. Définissez la variable d’environnement `AZUREML_EXTRA_REQUIREMENTS_TXT` dans votre [environnement](how-to-use-environments.md) Azure Machine Learning sur l’emplacement du fichier `requirements.txt`.

Une fois déployés, les packages sont automatiquement restaurés pour votre script de scoring.

> [!TIP]
> Même pendant un prototypage, nous vous recommandons de référencer chaque version de package dans `requirements.txt`.
> Par exemple, utilisez `scipy == 1.2.3` au lieu de seulement `scipy`, ou même `scipy > 1.2.3`.
> Si vous ne référencez pas une version exacte et que `scipy` publie une nouvelle version, cela peut provoquer l’arrêt de votre script de scoring, et des échecs lors du déploiement et de la mise à l’échelle.

L’exemple suivant montre la définition de la variable d’environnement `AZUREML_EXTRA_REQUIRMENTS_TXT` :

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies 

myenv = Environment(name="my_azureml_env")
myenv.docker.enabled = True
myenv.docker.base_image = <MCR-path>
myenv.python.user_managed_dependencies = True

myenv.environment_variables = {
    "AZUREML_EXTRA_REQUIREMENTS_TXT": "requirements.txt"
}
```

Le diagramme suivant est une représentation visuelle du processus d’installation dynamique :

:::image type="content" source="./media/how-to-prebuilt-docker-images-inference-python-extensibility/dynamic-install-python-extend.svg" alt-text="Diagramme du processus d’installation dynamique":::

<a id="preinstalled"></a>

## <a name="pre-installed-python-packages"></a>Packages Python préinstallés

Cette approche monte un répertoire que vous fournissez dans l’image. Les packages Python de ce répertoire peuvent ensuite être utilisés par le script d’entrée (`score.py`).

Pour étendre votre image de conteneur Docker préconfigurée via des packages Python préinstallés, effectuez ces étapes :

> [!IMPORTANT]
> Vous devez utiliser des packages compatibles avec Python 3.7. Toutes les images actuelles référencent Python 3.7.

1. Créez un environnement virtuel en utilisant [virtualenv](https://virtualenv.pypa.io/).

1. Installez vos dépendances. Par exemple, si vous avez une liste de dépendances dans un fichier `requirements.txt`, vous pouvez l’utiliser pour installer avec `pip install -r requirements.txt` ou simplement des dépendances individuelles `pip install`.

1. Quand vous spécifiez la variable d’environnement `AZUREML_EXTRA_PYTHON_LIB_PATH`, veillez à pointer vers le répertoire des packages de site correct, qui va varier en fonction du nom de votre environnement et de votre version de Python. Le code suivant montre la définition du chemin pour un environnement virtuel nommé `myenv` et Python 3.7 :


    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies 

    myenv = Environment(name='my_azureml_env')
    myenv.docker.enabled = True
    myenv.docker.base_image = <MCR-path>
    myenv.python.user_managed_dependencies = True

    myenv.environment_variables = {
        "AZUREML_EXTRA_PYTHON_LIB_PATH": "myenv/lib/python3.7/site-packages"
    }
    ```

Le diagramme suivant est une représentation visuelle du processus des packages préinstallés :

:::image type="content" source="./media/how-to-prebuilt-docker-images-inference-python-extensibility/pre-install-python-extend.svg" alt-text="Diagramme du processus utilisant des packages préinstallés":::

### <a name="common-problems"></a>Problèmes courants

La solution de montage va fonctionner seulement quand votre répertoire de packages de site `myenv` contient toutes vos dépendances. Si votre environnement local utilise des dépendances installées à un emplacement différent, elles ne celles-ci ne seront pas disponibles dans l’image.

Voici quelques éléments qui peuvent être à l’origine de ce problème :

* `virtualenv` crée un environnement isolé par défaut. Une fois que vous activez l’environnement virtuel, les __dépendances globales ne peuvent pas être utilisées__.
* Si vous avez une variable d’environnement `PYTHONPATH` qui pointe vers vos dépendances globales, __elle peut interférer avec votre environnement virtuel__. Exécutez `pip list` et `pip freeze` après l’activation de votre environnement pour vérifier qu’aucune dépendance indésirable n’est présente dans votre environnement.
* __Conda et les environnements `virtualenv` peuvent interférer__. Veillez à ne pas utiliser en même temps l’[environnement Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) et `virtualenv`.

## <a name="limitations"></a>Limites

### <a name="modelpackage"></a>Model.package()

* La méthode [Model.package()](/python/api/azureml-core/azureml.core.model(class)) vous permet de créer un package de modèle sous la forme d’une image Docker ou d’un contexte de génération de fichier Dockerfile. L’utilisation de Model.package() avec des images Docker d’inférence prédéfinis déclenche la génération d’une image intermédiaire qui remplace l’utilisateur non-racine par l’utilisateur racine.

* Nous vous encourageons à utiliser nos solutions d’extensibilité de package Python. Si d’autres dépendances sont nécessaires (comme des packages `apt`), créez votre propre [fichier Dockerfile en étendant à partir de l’image d’inférence](how-to-extend-prebuilt-docker-image-inference.md#buildmodel).

## <a name="frequently-asked-questions"></a>Forum aux questions

* Dans l’approche d’extensibilité « requirements.txt », est-il obligatoire que le nom de fichier soit `requirements.txt` ?

        
    ```python
    myenv.environment_variables = {
        "AZUREML_EXTRA_REQUIREMENTS_TXT": "name of your pip requirements file goes here"
    }
    ```

* Pouvez-vous résumer l’approche `requirements.txt` et l’*approche par montage* ?

    Commencez le prototypage avec l’approche *requirements.txt*.
    Après quelques itérations, quand vous êtes certain des packages (et des versions) dont vous avez besoin pour un déploiement de modèle réussi, passez à la *solution de montage*.
        
    Voici une comparaison détaillée.

    | Élément comparé | Requirements.txt (installation dynamique) | Montage de packages |
    | ----- | ----- | ------ |
    | Solution  | Créez un fichier `requirements.txt` qui installe les packages spécifiés au démarrage du conteneur. | Créez un environnement Python local avec toutes les dépendances. Montez ce répertoire dans le conteneur au moment de l’exécution. |
    | Installation des packages           | Aucune installation supplémentaire (en supposant que pip est déjà installé)                                                                                                          | Installation de l’environnement virtuel ou de Conda.                                                                                   |
    | Configuration de l’environnement virtuel              | Aucune configuration supplémentaire de l’environnement virtuel n’est nécessaire, car les utilisateurs peuvent extraire l’environnement utilisateur local actuel avec pip freeze si nécessaire pour créer le fichier `requirements.txt`. | Nécessité de configurer un environnement virtuel propre ; peut nécessiter des étapes supplémentaires en fonction de l’environnement local de l’utilisateur actuel.                        |
    | Débogage                  | Le serveur est facile à configurer et à déboguer, car les dépendances sont listées clairement. | Un environnement virtuel non propre peut provoquer des problèmes lors du débogage du serveur. Par exemple, il peut ne pas apparaître clairement si les erreurs proviennent de l’environnement ou du code utilisateur. |
    | Cohérence lors du scale-out | Non cohérent car dépendant des packages PyPi externes et des utilisateurs référençant leurs dépendances. Ces téléchargements externes peuvent être peu fiables.                                 | S’appuie seulement sur l’environnement utilisateur, donc pas de problèmes de cohérence.                                                                             |

* Pourquoi mon fichier `requirements.txt` et mon répertoire des dépendances montées sont-ils introuvables dans le conteneur ?

    Vérifiez localement que les variables d’environnement sont correctement définies. Ensuite, vérifiez que les chemins spécifiés sont orthographiés correctement et qu’ils existent.
    Vérifiez si vous avez correctement défini le répertoire source dans le constructeur de [configuration d’inférence](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor) .

* Puis-je remplacer les dépendances de package Python dans l’image Docker d’inférence prédéfinie ?

    Oui. Si vous voulez utiliser une autre version du package Python déjà installée dans une image d’inférence, notre solution d’extensibilité va respecter votre version. Vérifiez qu’il n’existe pas de conflits entre les deux versions.

## <a name="best-practices"></a>Bonnes pratiques

* Reportez-vous aux documents [Charger un modèle inscrit](how-to-deploy-advanced-entry-script.md#load-registered-models). Quand vous inscrivez un répertoire de modèle, n’incluez pas votre script de scoring, votre répertoire des dépendances montées ou le fichier `requirements.txt` dans ce répertoire.


* Pour plus d’informations sur le chargement d’un modèle inscrit ou local, consultez [Où et comment déployer](how-to-deploy-and-where.md?tabs=azcli#define-a-dummy-entry-script).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le déploiement d’un modèle, consultez [Guide pratique pour déployer un modèle](how-to-deploy-and-where.md).

Pour savoir comment résoudre les problèmes de déploiement d’images Docker prédéfinies, consultez [Guide pratique pour résoudre les problèmes de déploiement d’images Docker prédéfinies](how-to-troubleshoot-prebuilt-docker-image-inference.md).