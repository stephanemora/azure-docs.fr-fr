---
title: Exemples et procédures pas à pas correspondant aux machines DSVM - Azure | Microsoft Docs
description: Découvrez-en davantage sur les exemples et les procédures pas à pas qui vous expliquent comment accomplir des tâches et des scénarios courants avec Data Science Virtual Machine.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: e61f0f4ba30b29fea1b2fd5f2a2ab253d3a6710c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502771"
---
# <a name="samples-on-data-science-virtual-machines"></a>Exemples sur les machines DSVM (Data Science Virtual Machine)

Les machines Azure DSVM incluent un ensemble complet d’exemple de code. Cet exemple de code se présente sous la forme de blocs-notes Jupyter Notebook et de scripts dans des langages tels que Python et R. 
> [!NOTE]
> Pour plus d’informations sur l’exécution de blocs-notes Jupyter Notebook sur vos machines DSVM, consultez la section [Accéder à Jupyter](#access-jupyter).

## <a name="quick-reference-of-samples"></a>Référence rapide d’exemples
| Catégorie d’exemples | Description | Emplacements |
| ------------- | ------------- | ------------- |
| Langage R  | Les exemples en langage R décrivent des scénarios, par exemple la connexion aux magasins de données cloud Azure. Ils expliquent également comment comparer les langages R de Microsoft et R open source, et comme faire fonctionner des modèles sur Microsoft R Server ou SQL Server. <br/> [Langage R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Langage Python  | Les exemples en Python décrivent des scénarios tels que la connexion à des magasins de données cloud Azure et l’utilisation d’Azure Machine Learning.  <br/> [Langage Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Langage Julia  | L’exemple en Julia détaille traçage et apprentissage profond dans ce langage. Il explique également comment appeler les langages C et Python à partir de Julia. <br/> [Langage Julia](#julia-language) |<br/> Windows :<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux :<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Générez des modèles Machine Learning et d’apprentissage profond avec Machine Learning. Déployez-les n’importe où. Utilisez le réglage intelligent des hyperparamètres et le réglage Machine Learning automatisé, ainsi que la gestion des modèles et la formation distribuée. <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Blocs-notes PyTorch  | Exemples d’apprentissage profond qui utilisent des réseaux neuronaux basés sur PyTorch. La gamme des blocs-notes s’étend des scénarios pour débutants aux scénarios pour utilisateurs avancés.  <br/> [Blocs-notes PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Exemples et techniques de réseau neuronal implémentés à l’aide de l’infrastructure TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Exemples d’apprentissage profond publiés par l’équipe Microsoft Cognitive Toolkit.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux :<br/> `~notebooks/CNTK`<br/> <br/>|
| caffe2 | Exemples d’apprentissage profond qui utilisent des réseaux neuronaux basés sur Caffe2. Plusieurs blocs-notes permettent de familiariser les utilisateurs avec l’infrastructure Caffe2 et d’apprendre à l’utiliser efficacement. Les exemples incluent la création de jeux de données et le prétraitement des images, ainsi que la régression et l’utilisation des modèles préentraînés. <br/> [caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Exemples basés sur Python qui utilisent H2O pour des problèmes autour de scénarios concrets. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Langage SparkML  | Exemples qui utilisent les fonctionnalités du kit de ressources MLlib de Spark via pySpark et MMLSpark - Microsoft Machine Learning pour Apache Spark sur Apache Spark 2.x.  <br/> [Langage SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Exemples de Machine Learning standard de la bibliothèque XGBoost pour des scénarios tels que la classification ou la régression. <br/> [XGBoost](#xgboost) | <br/>Windows :<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Accéder à Jupyter 

Pour accéder à Jupyter, sélectionnez l’icône `Jupyter` sur le Bureau ou dans le menu de l’application. Vous pouvez également accéder à Jupyter sur les éditions Linux des machines DSVM. Vous pouvez y accéder à distance à partir d’un navigateur web en vous rendant sur `https://<Full Domain Name or IP Address of the DSVM>:8000` sur Ubuntu.

Pour ajouter des exceptions et rendre l’accès à Jupyter disponible sur un navigateur, consultez la capture d’écran suivante.


![Activation d’une exception Jupyter](./media/ubuntu-jupyter-exception.png)


Connectez-vous en utilisant le mot de passe utilisé pour la connexion aux machines DSVM.
<br/>

**Page d’accueil de Jupyter**
<br/>![Page d’accueil de Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Langage R 
<br/>![Exemples R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Langage Python
<br/>![Exemples Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Langage Julia 
<br/>![Exemples Julia](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Exemples AzureML](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Exemples PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Exemples TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK (Computational Network Toolkit de Microsoft Research) 
<br/>![Exemples CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>caffe2 
<br/>![Exemples Caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Exemples H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Exemples SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Exemples XGBoost](./media/xgboost-samples.png)<br/>

