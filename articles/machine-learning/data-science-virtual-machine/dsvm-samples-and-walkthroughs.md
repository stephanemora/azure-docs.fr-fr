---
title: Exemples et procédures pas à pas sur la machine virtuelle DSVM - Azure | Microsoft Docs
description: Exemples et procédures pas à pas sur la machine virtuelle DSVM
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392563"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Exemples sur les machines virtuelles DSVM (Data Science Virtual Machine)

Les machines virtuelles DSVM incluent un ensemble complet d’exemples de code, à la fois sous forme de notebooks Jupyter et de scripts dans des langages comme Python et R.    
> [!NOTE]
> Pour plus d’informations sur l’exécution de notebooks Jupyter sur votre machine virtuelle DSVM, reportez-vous à la section [Notebooks Jupyter](#access-jupyter).

## <a name="quick-reference-of-samples"></a>Référence rapide d’exemples
| Catégorie d’exemples | Description | Emplacements |
| ------------- | ------------- | ------------- |
| Langage **R**  | Exemples en **R** expliquant des scénarios tels que la connexion à des magasins de données cloud Azure, la comparaison d’Open Source R et de Microsoft R et la mise en application de modèles sur Microsoft R Server ou SQL Server. <br/> [Capture d’écran](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Langage **Python**  | Exemples en **Python** expliquant des scénarios tels que la connexion à des magasins de données cloud Azure et l’utilisation **d’Azure Machine Learning**.  <br/> [Capture d’écran](#python-language) | <br/>`~notebooks` <br/><br/>|
| Langage **Julia**  | Exemples en **Julia** qui décrivent la réalisation de graphiques en Julia, l’apprentissage profond en Julia, l’appel de C et Python depuis Julia, etc. <br/> [Capture d’écran](#julia-language) |<br/> **Windows** :<br/> `~notebooks/Julia_notebooks`<br/><br/> **Linux** :<br/> `~notebooks/julia`<br/><br/> |
| **Azure Machine Learning** AzureML  | Générez des modèles de machine learning et d’apprentissage profond (deep learning) avec le service **Azure Machine Learning** et déployez des modèles n’importe où. Tirez parti des fonctionnalités comme le machine learning automatisé, l’optimisation intelligente des hyperparamètres, la gestion des modèles et l’entraînement distribué. <br/> [Capture d’écran](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notebooks **PyTorch**  | Exemples d’apprentissage profond utilisant des réseaux neuronaux basés sur **PyTorch**. Il existe une multitude de blocs-notes allant des scénarios d’initiation aux scénarios avancés.  <br/> [Capture d’écran](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| **TensorFlow**  | Plusieurs exemples et techniques de réseau neuronal implémentés à l’aide du framework **TensorFlow**. <br/> [Capture d’écran](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| **CNTK (COMPUTATIONAL NETWORK TOOLKIT DE MICROSOFT RESEARCH)** <br/> (Microsoft Cognitive Toolkit)  | Exemples d’apprentissage profond publiés par l’équipe Microsoft Cognitive Toolkit.  <br/> [Capture d’écran](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> **Linux** :<br/> `~notebooks/CNTK`<br/> <br/>|
| **caffe2** | Exemples d’apprentissage profond utilisant des réseaux neuronaux basés sur **caffe2**. Il existe plusieurs blocs-notes conçus pour familiariser les utilisateurs avec caffe2 et sur la façon de l’utiliser efficacement, y compris des exemples comme le prétraitement des images, la création de jeux de données, la régression et l’utilisation de modèles préformés. <br/> [Capture d’écran](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | Exemples basés sur Python utilisant **H2O** pour de nombreux problèmes autour de scénarios concrets. <br/> [Capture d’écran](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Langage **SparkML**  | Exemple utilisant les fonctionnalités et les capacités du kit de ressources **MLlib** de Spark via **pySpark** et **MMLSpark - Microsoft Machine Learning pour Apache Spark** sur **Apache Spark 2.x**.  <br/> [Capture d’écran](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| **XGBoost** | Exemples d’apprentissage automatique standard dans **XGBoost** pour les scénarios tels que la classification ou la régression. <br/> [Capture d’écran](#xgboost) | <br/>**Windows** :<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Accéder à Jupyter 

Vous pouvez accéder à Jupyter en cliquant sur l’icône `Jupyter` sur le Bureau ou dans le menu de l’application. Vous pouvez également accéder à distance à Jupyter sur les éditions Linux de la machine virtuelle DSVM depuis un navigateur web en accédant à **`https://<Full Domain Name or IP Address of the DSVM>:8000`** sur Ubuntu.

Consultez la capture d’écran pour voir comment ajouter l’exception et activer l’accès à Jupyter par le biais du navigateur.


![Activer une exception Jupyter](./media/ubuntu-jupyter-exception.png)


Connectez-vous avec le même mot de passe que pour la connexion à la machine virtuelle DSVM.
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
<br/>![Exemples AzurekML](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Exemples PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Exemples TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK (Computational Network Toolkit de Microsoft Research) 
<br/>![Exemples CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>caffe2 
<br/>![Exemples caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Exemples H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Exemples SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Exemples XGBoost](./media/xgboost-samples.png)<br/>

