---
title: Exécution des scripts d’apprentissage automatique Python
titleSuffix: Azure Machine Learning Studio
description: Découvrez comment utiliser Python dans Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 4b4f3877b56752756050de0af226571ac2a93293
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888147"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Exécution des scripts d’apprentissage automatique Python dans Azure Machine Learning Studio

Python est un outil précieux dans le coffre à outils de nombreux scientifiques de données. Il est utilisé à chaque étape du notamment l’exploration de données, l’extraction des caractéristiques, apprentissage du modèle et la validation et le déploiement des flux de travail typique de machine learning.

Cet article décrit comment vous pouvez utiliser le module exécuter le Script Python à utiliser le code Python dans vos expériences d’Azure Machine Learning Studio et les services web.

## <a name="using-the-execute-python-script-module"></a>L’utilisation du module exécuter le Script Python

L’interface principale de Python dans Studio consiste à utiliser le [exécuter le Script Python] [ execute-python-script] module. Il accepte jusqu'à trois entrées et génère jusqu'à deux sorties, similaire à la [Execute R Script] [ execute-r-script] module. Le code Python est entré dans la zone paramètre via une fonction de point d’entrée spécialement nommée appelée `azureml_main`.

![Exécuter le module de Script Python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Exemple de code python dans la zone paramètre de module](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Paramètres d'entrée

Entrées pour le module Python sont exposées sous forme de trames de données Pandas. Le `azureml_main` fonction accepte jusqu'à deux trames Pandas facultatif en tant que paramètres.

Le mappage entre les ports d’entrée et les paramètres de fonction est positionnel :

- Le premier port d’entrée connecté est mappé au premier paramètre de la fonction.
- La seconde entrée (si elle est connectée) est mappée au second paramètre de la fonction.
- La troisième entrée est utilisée pour [importer des modules Python supplémentaires](#import-modules).

Plus la sémantique de la façon dont les ports d’entrée sont mappées aux paramètres de la `azureml_main` (fonction) sont présentées ci-dessous.

![Table des configurations de port d’entrée et de la signature résultante de Python](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Valeurs de retour de sortie

Le `azureml_main` fonction doit renvoyer une trame de données Pandas unique empaquetée dans un Python [séquence](https://docs.python.org/2/c-api/sequence.html) comme un tuple, liste ou un tableau NumPy. Le premier élément de cette séquence est retourné pour le premier port de sortie du module. Le second port de sortie du module est utilisé pour [visualisations](#visualizations) et ne nécessite pas une valeur de retour. Ce schéma est illustré ci-dessous.

![Mappage de ports aux paramètres d’entrée et retourner la valeur pour le port de sortie](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Traduction des types de données d’entrée et de sortie

Jeux de données de Studio n’est pas le même que les trames de données Pandas. Par conséquent, des jeux de données d’entrée dans Studio sont converties en données Pandas et les trames de données de sortie sont reconverties en jeux de données de Studio. Pendant ce processus de conversion, les conversions suivantes sont également effectuées :

 **Type de données de Python** | **Procédure de traduction de Studio** |
| --- | --- |
| Chaînes et valeurs numériques| Traduit en l’état |
| Pandas « NA » | Traduit en tant que 'Value manquant' |
| Vecteurs d’index | Non pris en charge * |
| Noms de colonne non-chaîne | Appeler `str` sur les noms de colonne |
| Noms de colonnes dupliqués | Ajoutez un suffixe numérique : (1), (2), (3), et ainsi de suite.

**Toutes les trames de données d’entrée dans la fonction Python ont toujours un index numérique de 64 bits à partir de 0 au nombre de lignes moins 1*

## <a id="import-modules"></a>L’importation des modules de script Python existants

Le serveur principal utilisé pour exécuter Python est basé sur [Anaconda](https://store.continuum.io/cshop/anaconda/), un largement utilisé distribution scientifique Python. Il est fourni avec près de 200 des packages Python courants utilisés dans les charges de travail orientés données. Studio ne prend pas actuellement en charge l’utilisation de systèmes de gestion de package telles que Pip ou Conda pour installer et gérer les bibliothèques externes.  Si vous trouvez la nécessité d’incorporer des bibliothèques supplémentaires, utilisez le scénario suivant comme guide.

Un cas d’usage courant consiste à incorporer les scripts Python existants dans des expériences de Studio. Le [exécuter le Script Python] [ execute-python-script] module accepte un fichier zip contenant les modules Python au troisième port d’entrée. Le fichier est décompressé par l’infrastructure d’exécution lors de l’exécution, et le contenu est ajouté au chemin de bibliothèque de l’interpréteur Python. La fonction du point d'entrée `azureml_main` peut ensuite importer ces modules directement. 

Prenez pour exemple le fichier Hello.py contenant une fonction simple « Hello, World ».

![Fonction définie par l’utilisateur dans le fichier Hello.py](./media/execute-python-scripts/figure4.png)

Ensuite, nous créons un fichier Hello.zip contenant Hello.py :

![Fichier ZIP contenant le code Python défini par l’utilisateur](./media/execute-python-scripts/figure5.png)

Téléchargez le fichier zip comme un jeu de données dans Studio. Puis créez et exécutez une expérience qui utilise le code Python dans le fichier Hello.zip en le joignant au troisième port d’entrée de la **exécuter le Script Python** module, comme indiqué dans l’image suivante.

![Exemple d’expérience avec Hello.zip en tant qu’entrée à un module Execute Python Script](./media/execute-python-scripts/figure6a.png)

![Code Python défini par l’utilisateur est chargé dans un fichier zip](./media/execute-python-scripts/figure6b.png)

La sortie du module montre que le fichier zip a été décompressé et que la fonction `print_hello` a été exécutée.

![Sortie de module montrant la fonction définie par l’utilisateur](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Accéder aux objets BLOB de stockage Azure

Vous pouvez accéder aux données stockées dans un compte de stockage d’objets Blob Azure à l’aide de ces étapes :

1. Téléchargez le [package de stockage d’objets Blob Azure pour Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) localement.
1. Télécharger le fichier zip à votre espace de travail Studio comme un jeu de données.
1. Créer votre objet BlobService avec `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Désactiver **transfert sécurisé requis** dans votre stockage **Configuration** onglet Paramètres

![Désactiver le transfert sécurisé requis dans le portail Azure](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Mise en place des scripts Python

Les modules [Exécuter le script Python][execute-python-script] utilisés dans une expérience de notation sont appelés lorsqu’ils sont publiés en tant que service web. Par exemple, l’image ci-dessous montre une expérience de notation qui contient le code pour évaluer une seule expression Python.

![Espace de travail Studio pour un service web](./media/execute-python-scripts/figure3a.png)

![Expression de Pandas de Python](./media/execute-python-scripts/python-script-with-python-pandas.png)

Un service web créé à partir de cette expérience prendrait les actions suivantes :

1. Prendre une expression Python comme entrée (en tant que chaîne)
1. Envoyer l’expression de Python à l’interpréteur Python
1. Retourne une table qui contient l’expression et le résultat évalué.

## <a id="visualizations"></a>Utilisation des visualisations

Les graphiques créés à l’aide de MatplotLib peuvent être renvoyés par le [exécuter le Script Python][execute-python-script]. Toutefois, les tracés ne sont pas automatiquement redirigés aux images tels qu’ils sont lors de l’utilisation de R. Par conséquent, l’utilisateur doit enregistrer explicitement les graphiques en fichiers PNG.

Pour générer des images à partir de MatplotLib, vous devez effectuer les étapes suivantes :

1. Basculer le serveur principal sur « AGG » du convertisseur de basé sur Qt-par défaut.
1. Créer un nouvel objet figure.
1. Obtenir de l’axe et générer tous les tracés dedans.
1. Enregistrer la figure dans un fichier PNG.

Ce processus est illustré dans les images suivantes qui créent une matrice de nuage de points à l’aide de la fonction scatter_matrix dans Pandas.

![Code pour enregistrer les figures MatplotLib dans des images](./media/execute-python-scripts/figure-v1-8.png)

![Cliquez sur visualiser sur un module Execute Python Script pour afficher les chiffres](./media/execute-python-scripts/figure-v2-9a.png)

![Visualisation des graphiques pour un exemple d’expérience à l’aide de code Python](./media/execute-python-scripts/figure-v2-9b.png)

Il est possible de renvoyer plusieurs figures en les enregistrant dans différentes images. Le runtime Studio récupère toutes les images et les concatène pour la visualisation.

## <a name="advanced-examples"></a>Exemples avancés

L’environnement Anaconda installé dans Studio contient les packages courants tels que NumPy, SciPy et Scikits-Learn. Ces packages peuvent être utilisés efficacement pour le traitement des données dans un pipeline d’apprentissage.

Par exemple, les expériences suivantes et le script illustrent l’utilisation des apprenants d’ensemble dans Scikits-Learn pour calculer les notations d’importance de fonctionnalité d’un jeu de données. Les notations peuvent servir à effectuer une sélection de fonctionnalités supervisée avant d’être introduits dans un autre modèle.

Voici la fonction Python permettant de calculer les notations d’importance et de classer les fonctionnalités conformément à ces dernières :

![Fonction pour classer les fonctionnalités par scores](./media/execute-python-scripts/figure8.png)

L’expérience suivante calcule, puis renvoie les notations d’importance des fonctionnalités dans l’ensemble de données « Diabète chez les indiens Pima » dans Azure Machine Learning Studio :

![Faire des essais pour classer des fonctionnalités dans le jeu de données Pima indiens PIMA à l’aide de Python](./media/execute-python-scripts/figure9a.png)

![Visualisation de la sortie du module exécuter le Script Python](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Limites

Le [exécuter le Script Python] [ execute-python-script] module présente actuellement les limitations suivantes :

### <a name="sandboxed-execution"></a>Exécution de sable

Le runtime Python est actuellement sandbox et n’autorise pas l’accès au réseau ou le système de fichiers local de manière persistante. Tous les fichiers enregistrés localement sont isolés et supprimés une fois que le module se termine. Le code Python ne peut pas accéder à la plupart des répertoires sur la machine qui les exécute, excepté le répertoire actuel et ses sous-répertoires.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Manque de développement sophistiquées et en charge du débogage

 Le module Python ne prend pas en charge les fonctionnalités IDE telles que Intellisense et Débogage. En outre, si le module échoue lors de l’exécution, l’arborescence complète des appels de procédure Python est disponible. Mais elle doit être affichée dans le journal de sortie du module. Nous vous recommandons de développer et de déboguer vos scripts Python dans un environnement tel qu’IPython, puis d’importer le code dans le module.

### <a name="single-data-frame-output"></a>Sortie de trame de données unique

 Le point d'entrée Python n'est autorisé qu'à renvoyer une trame de données unique en tant que sortie. Il n’est pas encore possible de retourner des objets arbitraires Python tels que les modèles formés directement à l’exécution du Studio. Comme avec [Exécuter le script R][execute-r-script], qui présente les mêmes restrictions, il est souvent possible de stocker des objets dans un tableau d’octets et de les renvoyer ensuite dans une trame de données.

### <a name="inability-to-customize-python-installation"></a>Impossibilité de personnaliser l’installation de Python

L'ajout de modules Python personnalisés n'est, à l'heure actuelle, possible qu'à l'aide du mécanisme de fichiers zip décrit précédemment. Bien que cela soit faisable pour les petits modules, il est assez lourde pour les gros modules (notamment les modules avec des DLL natives) ou un grand nombre de modules.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script