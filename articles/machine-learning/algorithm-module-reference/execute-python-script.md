---
title: 'Exécuter un script Python : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Exécuter un script Python dans Azure Machine Learning pour exécuter du code Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 0f86d1ad03062797764af6a0d49beacaa3458a8f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365550"
---
# <a name="execute-python-script-module"></a>Module Exécuter un script Python

Cet article décrit un module dans le concepteur Azure Machine Learning (version préliminaire).

Utilisez ce module pour exécuter du code Python. Pour plus d’informations sur les principes de conception et d’architecture de Python, consultez [cet article](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

Python vous permet d’exécuter des tâches qui ne sont actuellement pas prises en charge par les modules existants, telles que :

+ visualisation des données à l’aide de `matplotlib` ;
+ utilisation de bibliothèques Python pour énumérer les jeux de données et les modèles de votre espace de travail ;
+ lecture, chargement et manipulation de données à partir de sources non prises en charge par le module [Importer des données](./import-data.md).
+ Exécuter votre propre code Deep Learning 


Azure Machine Learning utilise la distribution Anaconda de Python, qui inclut de nombreux utilitaires courants pour le traitement des données. Nous mettrons à jour la version d’Anaconda automatiquement. La version actuelle est la suivante :
 -  distribution Anaconda 4.5+ pour Python 3.6 

Les packages préinstallés sont les suivants :
-    adal==1.2.2
-    applicationinsights==0.11.9
-    attrs==19.3.0
-    azure-common==1.1.25
-    azure-core==1.3.0
-    azure-graphrbac==0.61.1
-    azure-identity==1.3.0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    azure-mgmt-keyvault==2.2.0
-    azure-mgmt-resource==8.0.1
-    azure-mgmt-storage==8.0.0
-    azure-storage-blob==1.5.0
-    azure-storage-common==1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-native==14.1.0
-    azureml-dataprep==1.3.5
-    azureml-defaults==1.1.5.1
-    azureml-designer-classic-modules==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-internal==0.0.18
-    azureml-model-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core==1.1.5
-    azureml-telemetry==1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3==1.12.29
-    botocore==1.15.29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    click==7.1.1
-    cloudpickle==1.3.0
-    configparser==3.7.4
-    contextlib2==0.6.0.post1
-    cryptography==2.8
-    cycler==0.10.0
-    dill==0.3.1.1
-    distro==1.4.0
-    docker==4.2.0
-    docutils==0.15.2
-    dotnetcore2==2.1.13
-    flask==1.0.3
-    fusepy==3.0.1
-    gensim==3.8.1
-    google-api-core==1.16.0
-    google-auth==1.12.0
-    google-cloud-core==1.3.0
-    google-cloud-storage==1.26.0
-    google-resumable-media==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicorn==19.9.0
-    idna==2.9
-    imbalanced-learn==0.4.3
-    isodate==0.6.0
-    itsdangerous==1.1.0
-    jeepney==0.4.3
-    jinja2==2.11.1
-    jmespath==0.9.5
-    joblib==0.14.0
-    json-logging-py==0.2
-    jsonpickle==1.3
-    jsonschema==3.0.1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe==1.1.1
-    matplotlib==3.1.3
-    more-itertools==6.0.0
-    msal-extensions==0.1.3
-    msal==1.1.0
-    msrest==0.6.11
-    msrestazure==0.6.3
-    ndg-httpsclient==0.5.1
-    nimbusml==1.6.1
-    numpy==1.18.2
-    oauthlib==3.1.0
-    pandas==0.25.3
-    pathspec==0.7.0
-    pip==20.0.2
-    portalocker==1.6.0
-    protobuf==3.11.3
-    pyarrow==0.16.0
-    pyasn1-modules==0.2.8
-    pyasn1==0.4.8
-    pycparser==2.20
-    pycryptodomex==3.7.3
-    pyjwt==1.7.1
-    pyopenssl==19.1.0
-    pyparsing==2.4.6
-    pyrsistent==0.16.0
-    python-dateutil==2.8.1
-    pytz==2019.3
-    requests-oauthlib==1.3.0
-    requests==2.23.0
-    rsa==4.0
-    ruamel.yaml==0.15.89
-    s3transfer==0.3.3
-    scikit-learn==0.22.2
-    scipy==1.4.1
-    secretstorage==3.1.2
-    setuptools==46.1.1.post20200323
-    six==1.14.0
-    smart-open==1.10.0
-    urllib3==1.25.8
-    websocket-client==0.57.0
-    werkzeug==0.16.1
-    wheel==0.34.2

 Pour installer d’autres packages absents de la liste des packages préinstallés, par exemple *scikit-misc*, ajoutez le code ci-après à votre script : 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Charger des fichiers
Le module **Exécuter un script Python** prend en charge le chargement de fichiers à l’aide du [Kit de développement logiciel (SDK) Python Azure Machine Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

L’exemple suivant montre comment charger un fichier image dans le module **Exécuter un script Python** :

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Une fois l’exécution du pipeline terminée, vous pouvez afficher un aperçu de l’image dans le panneau de droite du module

> [!div class="mx-imgBorder"]
> ![Image chargée](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Comment configurer le module Exécuter un script Python

Le module **Exécuter un script Python** contient un exemple de code Python que vous pouvez utiliser comme point de départ. Pour configurer le module **Exécuter un script Python**, vous fournissez un ensemble d’entrées et de code Python à exécuter dans la zone de texte **Script Python**.

1. Ajoutez le module **Exécuter un script Python** à votre pipeline.

2. À partir du concepteur, ajoutez et connectez dans **Jeu de données 1** tous les jeux de données que vous souhaitez utiliser pour l’entrée. Référencez ce jeu de données dans votre script Python sous le nom **DataFrame1**.

    L’utilisation d’un jeu de données est facultative, si vous souhaitez générer des données à l’aide de Python ou utiliser du code Python pour importer les données directement dans le module.

    Ce module prend en charge l’ajout d’un second jeu de données dans **Jeu de données 2**. Référencez ce second jeu de données dans votre script Python sous le nom DataFrame2.

    Les jeux de données stockés dans Azure Machine Learning sont automatiquement convertis en dataframes **pandas** lorsqu’ils sont chargés avec ce module.

    ![Mappage des entrées de l’exécution d’un script Python](media/module/python-module.png)

4. Pour inclure du code ou de nouveaux packages Python, ajoutez le fichier zip contenant ces ressources personnalisées dans **Script groupé**. L’entrée dans **Script groupé** doit correspondre à un fichier zip chargé dans votre espace de travail comme jeu de données de type fichier. Vous pouvez charger le jeu de données dans la page de ressource **Jeux de données** et glisser-déposer le module du jeu de données à partir de la liste **Mes jeux de données** dans l’arborescence du module de gauche de la page de création. 

    Tous les fichiers qui figurent dans l’archive zip chargée sont utilisables lors de l’exécution du pipeline. Si l’archive inclut une structure de répertoires, cette structure est préservée, mais vous devez ajouter au chemin d’accès un répertoire appelé **src**.

5. Dans la zone de texte **Script Python**, saisissez ou collez un script Python valide.

    La zone de texte **Script Python** est préremplie avec certaines instructions en commentaires, ainsi qu’avec un exemple de code pour l’accès aux données et la sortie. Vous devez modifier ou remplacer ce code. Veillez à suivre les conventions Python concernant la mise en retrait et la casse.

    + Le script doit contenir une fonction nommée `azureml_main` comme point d’entrée pour ce module.
    + La fonction de point d’entrée peut contenir jusqu’à deux arguments d’entrée : `Param<dataframe1>` et `Param<dataframe2>`.
    + Les fichiers zip connectés au troisième port d’entrée sont décompressés et stockés dans le répertoire `.\Script Bundle`, qui est également ajouté à l’élément `sys.path` Python. 

    Par conséquent, si votre fichier zip contient `mymodule.py`, importez-le à l’aide de `import mymodule`.

    + Deux jeux de données peuvent être renvoyés au concepteur, ce qui doit constituer une séquence de type `pandas.DataFrame`. Vous pouvez créer d’autres sorties dans votre code Python et les écrire directement dans le service Stockage Azure.

6. Soumettez le pipeline, ou sélectionnez le module et cliquez sur **Exécuter la sélection** pour exécuter uniquement le script Python.

    La totalité des données et du code sont chargés sur une machine virtuelle et s’exécutent à l’aide de l’environnement Python spécifié.

## <a name="results"></a>Résultats

Les résultats des calculs effectués par le code Python incorporé doivent être fournis sous la forme pandas.DataFrame, qui est automatiquement convertie au format de jeu de données Azure Machine Learning. Ceci vous permet d’utiliser les résultats avec d’autres modules du pipeline.

Le module renvoie deux jeux de données :  
  
+ **Résultats du jeu de données 1**, défini par le premier dataframe pandas renvoyé dans le script Python ;

+ **Résultats du jeu de données 2**, défini par le second dataframe pandas renvoyé dans le script Python.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 