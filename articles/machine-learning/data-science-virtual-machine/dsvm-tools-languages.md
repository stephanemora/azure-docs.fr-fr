---
title: Langues prises en charge
titleSuffix: Azure Data Science Virtual Machine
description: Langages de programmation pris en charge et outils associés, préinstallés sur Data Science Virtual Machine.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: fcb2a4d9860687fb18409666f81839002329d54e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070895"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Langages pris en charge sur la machine virtuelle DSVM 

L’environnement DSVM (Data Science Virtual Machine) est fourni avec plusieurs langages et outils de développement prédéfinis qui permettent de créer vos applications d’intelligence artificielle (IA). Voici les plus connus.

## <a name="python"></a>Python

| Category | Valeur |
|--|--|
| Versions de langage prises en charge | Python 3.8 |
| Éditions DSVM prises en charge | Windows Server 2019, Ubuntu 18.04 |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ? | Il existe plusieurs environnements `conda` dans lesquels chacun de ces packages Python est préinstallé. Pour afficher la liste de tous les environnements disponibles sur votre ordinateur, exécutez `conda env list`. |

### <a name="how-to-use-and-run-it"></a>Comment l’utiliser et l’exécuter ?

* Exécutez à l’invite de commandes :

  Ouvrez une invite de commandes et utilisez l’une des méthodes suivantes, en fonction de la version de Python que vous voulez exécuter :

    ```
    conda activate <conda_environment_name>
    python --version
    ```
    
* Utiliser dans un IDE :

  Plusieurs environnements IDE sont installés sur les images DSVM, par exemple VSCode ou PyCharm. Vous pouvez les utiliser pour modifier, exécuter et déboguer vos scripts Python.

* Utilisation dans Jupyter Lab :

  Ouvrez un onglet Launcher dans Jupyter Lab et sélectionnez le type et le noyau de votre nouveau document. Si vous souhaitez placer votre document dans un dossier donné, accédez d’abord à ce dossier dans l’Explorateur de fichiers, sur le côté gauche.

* Installez les packages Python :

  Pour installer un nouveau package, vous devez d’abord activer l’environnement approprié. L’environnement est l’emplacement où votre nouveau package sera installé, et le package sera alors uniquement disponible dans cet environnement.

  Pour activer un environnement, exécutez `conda activate <environment_name>`. Une fois l’environnement activé, vous pouvez utiliser un gestionnaire de package comme `conda` ou `pip` pour installer ou mettre à jour un package.

  En guise d’alternative, si vous utilisez Jupyter, vous pouvez également installer des packages directement en exécutant `!pip install --upgrade <package_name>` dans une cellule.

## <a name="r"></a>R

| Category | Valeur |
|--|--|
| Versions de langage prises en charge | CRAN R 4.0.5 |
| Éditions DSVM prises en charge | Linux, Windows |

### <a name="how-to-use-and-run-it"></a>Comment l’utiliser et l’exécuter ?

* Exécutez à l’invite de commandes :

  Ouvrez une invite de commandes et tapez `R`.

* Utiliser dans un IDE :

  Pour modifier des scripts R dans un environnement de développement intégré (IDE), vous pouvez utiliser RStudio, qui est installé sur les images DSVM par défaut.

* Utiliser dans Jupyter Lab

  Ouvrez un onglet Launcher dans Jupyter Lab et sélectionnez le type et le noyau de votre nouveau document. Si vous souhaitez placer votre document dans un dossier donné, accédez d’abord à ce dossier dans l’Explorateur de fichiers, sur le côté gauche.

* Installer des packages R :

  Vous pouvez installer de nouveaux packages R à l’aide de la fonction `install.packages()` ou à l’aide de RStudio.

## <a name="julia"></a>Julia

| Category | Valeur |
| ------------- | ------------- |
| Versions de langage prises en charge | 1.0.5 |
| Éditions DSVM prises en charge      | Linux, Windows     |


### <a name="how-to-use-and-run-it"></a>Comment l’utiliser et l’exécuter ?    

* Exécuter à l’invite de commandes

  Ouvrez une invite de commandes et exécutez `julia`.

* Utiliser dans Jupyter :

  Ouvrez un onglet Launcher dans Jupyter et sélectionnez le type et le noyau de votre nouveau document. Si vous souhaitez placer votre document dans un dossier donné, accédez d’abord à ce dossier dans l’Explorateur de fichiers, sur le côté gauche.

* Installer les packages Julia :

  Vous pouvez utiliser des commandes du gestionnaire de package Julia comme `Pkg.add()` pour installer ou mettre à jour les packages.


## <a name="other-languages"></a>Autres langages

**C#**  : disponible sur Windows et accessible par le biais de l’édition Visual Studio Community ou à une `Developer Command Prompt for Visual Studio` où vous pouvez exécuter la commande `csc`.

**Java** : OpenJDK est disponible sur les éditions Linux et Windows de la machine virtuelle DSVM, et défini sur le chemin. Pour utiliser Java, vous pouvez taper la commande `javac` ou `java` à une invite de commandes dans Windows ou dans l’interpréteur de commandes Bash dans Linux.

**Node.JS** : Node.js est disponible sur les éditions Linux et Windows de la machine virtuelle DSVM, et défini sur le chemin. Pour accéder à Node.js, tapez la commande `node` ou `npm` à une invite de commandes dans Windows ou dans l’interpréteur de commandes Bash dans Linux. Sur Windows, l’extension Visual Studio pour les outils Node.js est installée afin de fournir un IDE graphique pour le développement de votre application Node.js.

**F#**  : disponible sur Windows et accessible par le biais de l’édition Visual Studio Community ou à une `Developer Command Prompt for Visual Studio` où vous pouvez exécuter la commande `fsc`.
