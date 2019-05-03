---
title: 'Exécutez le Script Python : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module exécuter le Script Python dans le service Azure Machine Learning à exécuter le code Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029129"
---
# <a name="execute-python-script-module"></a>Exécuter le module de Script Python

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour exécuter le code Python. Pour plus d’informations sur les principes de conception et architecture de Python, consultez [l’article suivant.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Avec Python, vous pouvez effectuer les tâches qui ne sont pas actuellement pris en charge par les modules existants tels que :

+ Visualisation des données à l’aide `matplotlib`
+ À l’aide de bibliothèques Python pour énumérer les jeux de données et des modèles dans votre espace de travail
+ Lecture, de chargement et de manipulation de données à partir de sources non prises en charge par le [importer des données](./import-data.md) module
+ Exécutez votre propre code dlvm 


Azure Machine Learning utilise la distribution Anaconda de Python, qui inclut de nombreux utilitaires courantes pour le traitement des données. Nous mettrons à jour Anaconda version automatiquement. Version actuelle est :
 -  Distribution anaconda 4.5 + pour Python 3.6 

Les packages préinstallés sont :
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

 Pour installer d’autres packages pas dans la liste préinstallés, par exemple *scikit-misc*, ajoutez le code suivant à votre script : 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Utilisation

Le **exécuter le Script Python** module contient des exemples de code Python que vous pouvez utiliser comme point de départ. Pour configurer le **exécuter le Script Python** module, vous fournissez un ensemble d’entrées et de code Python à exécuter dans le **script Python** zone de texte.

1. Ajouter le **exécuter le Script Python** module à votre expérience.

2. Ajoutez et connectez sur **Dataset1** des jeux de données à partir de l’interface que vous souhaitez utiliser pour l’entrée. Faire référence à ce jeu de données dans votre script Python comme **DataFrame1**.

    Utilisation d’un jeu de données est facultative, si vous souhaitez générer des données à l’aide de Python ou utilisez de code Python pour importer les données directement dans le module.

    Ce module prend en charge l’ajout d’un deuxième dataset sur **Dataset2**. Référencer le deuxième jeu de données dans votre script Python comme DataFrame2.

    Jeux de données stockés dans Azure Machine Learning est automatiquement converties en **pandas** trames de données lors du chargement avec ce module.

    ![Exécuter le mappage d’entrée Python](media/module/python-module.png)

4. Pour inclure les nouveaux packages Python ou code, ajoutez le fichier compressé contenant ces ressources personnalisées sur **regroupement de Script**. L’entrée de **Script groupé** doit être un fichier zippé déjà téléchargé vers votre espace de travail. 

    N’importe quel fichier contenu dans l’archive ZIP téléchargée peut être utilisé pendant l’exécution d’expérience. Si l’archive inclut une structure de répertoires, la structure est conservée, mais vous devez ajouter un répertoire appelé **src** pour le chemin d’accès.

5. Dans le **script Python** zone de texte, tapez ou collez le script Python valide.

    Le **script Python** zone de texte est préremplie avec des instructions dans les commentaires et exemple de code pour l’accès aux données et de sortie. **Vous devez modifier ou remplacer ce code.** Veillez à respecter les conventions de Python sur la mise en retrait et la casse.

    + Le script doit contenir une fonction nommée `azureml_main` comme point d’entrée pour ce module.
    + La fonction de point d’entrée peut contenir jusqu'à deux arguments d’entrée : `Param<dataframe1>` et `Param<dataframe2>`
    + Connecté au troisième port d’entrée de fichiers compressés sont décompressés et stockés dans le répertoire, `.\Script Bundle`, qui est également ajouté au fichier Python `sys.path`. 

    Par conséquent, si votre fichier zip contient `mymodule.py`, importez-le à l’aide `import mymodule`.

    + Deux jeux de données peut être retournées à l’interface, ce qui doit être une séquence de type `pandas.DataFrame`. Vous pouvez créer d’autres sorties dans votre code Python et les écrire directement dans stockage Azure.

6. Exécutez l’expérience, ou sélectionnez le module et cliquez sur **exécuter sélection** pour exécuter le script Python.

    Toutes les données et le code est chargé dans une machine virtuelle et exécuter à l’aide de l’environnement Python spécifié.

## <a name="results"></a>Résultats

Les résultats de tous les calculs effectués par le code Python incorporé doivent être fournies comme un pandas. Trame de données, qui est automatiquement converti au format de jeu de données Azure Machine Learning, afin que vous puissiez utiliser les résultats avec d’autres modules dans l’expérience.

Le module renvoie deux jeux de données :  
  
+ **Résultats 1 du jeu de données**, défini par la première trame de données pandas retourné dans le script Python

+ **Entraîner le jeu de données 2**, défini par la deuxième trame de données pandas retourné dans le script Python


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 