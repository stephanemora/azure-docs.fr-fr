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
ms.date: 10/22/2019
ms.openlocfilehash: 1d82261d4b5c1a66498c33610670d7a38acbb197
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152327"
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
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0.24.2
- Pillow==6.0.0
- pip==19.0.3
- pyarrow==0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- requests==2.21.0
- scikit-learn==0.20.3
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 Pour installer d’autres packages absents de la liste des packages préinstallés, par exemple *scikit-misc*, ajoutez le code ci-après à votre script : 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Utilisation

Le module **Exécuter un script Python** contient un exemple de code Python que vous pouvez utiliser comme point de départ. Pour configurer le module **Exécuter un script Python**, vous fournissez un ensemble d’entrées et de code Python à exécuter dans la zone de texte **Script Python**.

1. Ajoutez le module **Exécuter un script Python** à votre pipeline.

2. À partir du concepteur, ajoutez et connectez dans **Jeu de données 1** tous les jeux de données que vous souhaitez utiliser pour l’entrée. Référencez ce jeu de données dans votre script Python sous le nom **DataFrame1**.

    L’utilisation d’un jeu de données est facultative, si vous souhaitez générer des données à l’aide de Python ou utiliser du code Python pour importer les données directement dans le module.

    Ce module prend en charge l’ajout d’un second jeu de données dans **Jeu de données 2**. Référencez ce second jeu de données dans votre script Python sous le nom DataFrame2.

    Les jeux de données stockés dans Azure Machine Learning sont automatiquement convertis en dataframes **pandas** lorsqu’ils sont chargés avec ce module.

    ![Mappage des entrées de l’exécution d’un script Python](media/module/python-module.png)

4. Pour inclure du code ou de nouveaux packages Python, ajoutez le fichier zip contenant ces ressources personnalisées dans **Script groupé**. L’entrée dans **Script groupé** doit correspondre à un fichier zip déjà chargé dans votre espace de travail. 

    Tous les fichiers qui figurent dans l’archive zip chargée sont utilisables lors de l’exécution du pipeline. Si l’archive inclut une structure de répertoires, cette structure est préservée, mais vous devez ajouter au chemin d’accès un répertoire appelé **src**.

5. Dans la zone de texte **Script Python**, saisissez ou collez un script Python valide.

    La zone de texte **Script Python** est préremplie avec certaines instructions en commentaires, ainsi qu’avec un exemple de code pour l’accès aux données et la sortie. Vous devez modifier ou remplacer ce code. Veillez à suivre les conventions Python concernant la mise en retrait et la casse.

    + Le script doit contenir une fonction nommée `azureml_main` comme point d’entrée pour ce module.
    + La fonction de point d’entrée peut contenir jusqu’à deux arguments d’entrée : `Param<dataframe1>` et `Param<dataframe2>`.
    + Les fichiers zip connectés au troisième port d’entrée sont décompressés et stockés dans le répertoire `.\Script Bundle`, qui est également ajouté à l’élément `sys.path` Python. 

    Par conséquent, si votre fichier zip contient `mymodule.py`, importez-le à l’aide de `import mymodule`.

    + Deux jeux de données peuvent être renvoyés au concepteur, ce qui doit constituer une séquence de type `pandas.DataFrame`. Vous pouvez créer d’autres sorties dans votre code Python et les écrire directement dans le service Stockage Azure.

6. Exécutez le pipeline, ou sélectionnez le module et cliquez sur **Exécuter la sélection** pour exécuter uniquement le script Python.

    La totalité des données et du code sont chargés sur une machine virtuelle et s’exécutent à l’aide de l’environnement Python spécifié.

## <a name="results"></a>Résultats

Les résultats des calculs effectués par le code Python incorporé doivent être fournis sous la forme pandas.DataFrame, qui est automatiquement convertie au format de jeu de données Azure Machine Learning. Ceci vous permet d’utiliser les résultats avec d’autres modules du pipeline.

Le module renvoie deux jeux de données :  
  
+ **Résultats du jeu de données 1**, défini par le premier dataframe pandas renvoyé dans le script Python ;

+ **Résultats du jeu de données 2**, défini par le second dataframe pandas renvoyé dans le script Python.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 