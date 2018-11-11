---
title: Environnements de développement pour Azure Machine Learning | Microsoft Docs
description: Vue d’ensemble des environnements de développement que vous pouvez utiliser avec le service Azure Machine Learning. La seule exigence pour votre environnement de développement est Python 3. Cependant, nous vous recommandons d’utiliser également des environnements Conda. Pour les outils de développement, nous vous recommandons Jupyter Notebooks, Azure Notebooks et les éditeurs de code ou les IDE.
services: machine-learning
author: rastala
ms.author: roastala
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 9/24/2018
ms.openlocfilehash: 4d25e147044053aa76afe2da482b71c24efc2325
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242872"
---
# <a name="development-environment-for-azure-machine-learning"></a>Environnements de développement pour Azure Machine Learning 

Découvrez les environnements de développement que vous pouvez utiliser avec le service Azure Machine Learning. 

Le service Azure Machine Learning est indépendant de toute plateforme et ne nécessite pas d’environnement de développement spécifique. Il a besoin de __Python 3__, et il est recommandé d’utiliser __Conda__ pour créer des environnements isolés. __Si vous disposez déjà d’un environnement de développement qui répond à ces exigences__, vous pouvez ignorer ce document et accéder au document [Configurer votre environnement de développement](how-to-configure-environment.md).

Le reste de ce document aborde les environnements de développement que nous recommandons :

* __Blocs-notes Jupyter__
* __Azure Notebooks__
* __Environnements de développement intégrés (IDE) et éditeurs de texte__
* __Data Science Virtual Machine__ (Machine virtuelle Science des données)

Il est difficile de comparer ces environnements, car aussi bien les blocs-notes que les IDE peuvent être étendus. Par exemple, certains IDE peuvent être utilisés comme clients de Jupyter Notebooks. En règle générale, les __blocs-notes__ sont conçus pour une __expérimentation interactive__ et pour la __visualisation__. Les __éditeurs de code et les IDE__ fournissent des outils pour __améliorer la qualité du code__ et __s’intègrent aux systèmes externes__ tels que la gestion de versions.

> [!TIP]
> L’utilisation de l’un de ces environnements ne vous empêche pas d’utiliser l’autre. Les blocs-notes et les IDE sont des outils et peuvent donc être combinés en fonction des besoins. Par exemple, vous pouvez commencer vos expériences dans un bloc-notes, puis l’exporter vers un script Python en vue de le modifier et de le déboguer dans un IDE.
>
> C’est pourquoi Data Science Virtual Machine fournit à la fois des blocs-notes Jupyter et plusieurs IDE Python populaires.

## <a name="jupyter-notebooks"></a>Blocs-notes Jupyter

Jupyter Notebooks fait partie de [Jupyter Project](https://jupyter.org/). Il est destiné à fournir une expérience de codage interactive dans laquelle vous créez des documents qui associent du code en direct, du graphisme et du texte descriptif. Vous pouvez installer Jupyter Notebooks sur diverses plateformes.

Le fait de disposer de votre propre installation de Jupyter Notebook vous permet d’installer et de configurer l’environnement selon vos besoins. Toutefois, il vous revient de vous occuper de la maintenance du système.

## <a name="azure-notebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (préversion) est un environnement de blocs-notes pour le cloud Azure. Il est basé sur Jupyter et fournit un environnement dans lequel ont été préchargées des bibliothèques populaires. Le SDK Azure Machine Learning est déjà installé dans Azure Notebooks. Vous pouvez donc commencer à expérimenter sans presque aucune configuration préalable.

Azure Notebooks simplifie également le processus de partage de vos blocs-notes. Vous pouvez partager l’URL de vos blocs-notes, rendre votre bibliothèque publique ou partager des médias sociaux à partir de l’interface Azure Notebooks.

L’inconvénient d’Azure Notebooks est que vous ne disposez pas d’un contrôle total sur l’environnement, et que vous risquez de ne pas pouvoir installer les logiciels personnalisés dont vous avez besoin. Il s’agit également d’un environnement partagé. Vos blocs-notes peuvent donc s’exécuter plus lentement que sur une installation dédiée de Jupyter Notebook.

## <a name="ides-and-code-editors"></a>Éditeurs de code et IDE

De nombreux IDE et éditeurs de code fonctionnent avec Azure Machine Learning. La seule exigence logicielle est le SDK Azure Machine Learning, qui peut être installé à l’aide de l’utilitaire `pip`.

Nous vous recommandons d’utiliser [Visual Studio Code](https://code.visualstudio.com/), car il fournit les outils nécessaires pour travailler avec le langage Python et Azure Machine Learning. Il est disponible pour les plateformes Linux, macOS et Windows.

## <a name="data-science-virtual-machine"></a>Machine virtuelle de science des données

Data Science Virtual Machine (DSVM) est une combinaison des environnements précédents. Il s’agit d’une machine virtuelle présente sur la plateforme Azure, sur laquelle ont été préinstallés Jupyter Notebooks, Visual Studio Code et le SDK Azure Machine Learning. Étant donné que les logiciels nécessaires sont préinstallés dans l’image de machine virtuelle, vous pouvez commencer à expérimenter avec Azure Machine Learning rapidement une fois la machine virtuelle créée.

DSVM vous permet de sélectionner les ressources de calcul dont vous avez besoin, telles que le processeur, le GPU et la mémoire. D’autres éditeurs y sont également préinstallés, tels que PyCharm, ainsi que d’autres logiciels de machine learning, tels que TensorFlow, Keras et PyTorch. Si le logiciel dont vous avez besoin n’est pas installé, vous pouvez l’installer vous-même.

Pour plus d’informations sur les éléments préinstallés, consultez le document [Présentation de Data Science Virtual Machine](../data-science-virtual-machine/overview.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les environnements de développement, découvrez [comment configurer un environnement de développement](how-to-configure-environment.md).

