---
title: Outils inclus dans DSVM
titleSuffix: Azure Data Science Virtual Machine tools
description: Liste des outils inclus dans les images DSVM Windows et Ubuntu
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 10/10/2019
ms.openlocfilehash: 2d5dc2d33252849dc0a3e11a65fc54e878dbb8a3
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555309"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Quels sont les outils qui sont inclus dans Azure Data Science Virtual Machine ?

Vous trouverez ci-dessous une liste actualisée des outils inclus dans Data Science Virtual Machine, ainsi que des liens pour comprendre comment chaque outil est configuré.


| **Outil**                                                           | **Machine virtuelle DSVM Windows** | **Machine virtuelle DSVM Linux** | **Notes d’utilisation** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) avec packages courants préinstallés   |<span class='green-check'>&#9989;</span></br> (3.4.3)                    |<span class='green-check'>&#9989;</span></br> (3.4.3)  | [R dans DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition inclut : <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) framework hautes performances parallèle et distribué (et & Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nouveaux algorithmes de machine learning de pointe de Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp; [Opérationnalisation R et Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> (9.3.0)                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://www.microsoft.com/p/office-365-proplus/CFQ7TTC0K8R0) ProPlus avec activation partagée : Excel, Word et PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2.7 et 3.5 avec packages populaires préinstallés    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4.2)        | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) avec packages populaires préinstallés                         |<span class='green-check'>&#9989;</span> </br> (0.6.4)                   |<span class='green-check'>&#9989;</span></br> (0.6.2)              | [Julia dans DSVM](./dsvm-languages.md#julia) |
| Bases de données relationnelles                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [SQL Server dans DSVM](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Outils de base de données                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Pilotes ODBC/JDBC|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (outil de requête), <br />  bcp, sqlcmd <br />  Pilotes ODBC/JDBC| |
| Analytiques en base de données scalables avec services SQL Server de Machine Learning (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Serveur Jupyter Notebook](https://jupyter.org/) avec les noyaux suivants :                                  |<span class='green-check'>&#9989;</span></br> (5.5.0)   |<span class='green-check'>&#9989;</span> | [Exemples Jupyter Notebook](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span></br> (3.4.3) |<span class='green-check'>&#9989;</span></br> (3.4.3) | [Exemples R Jupyter](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span></br> (3) |<span class='green-check'>&#9989;</span> | [Exemples Python Jupyter](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span></br> (0.6.4) |<span class='green-check'>&#9989;</span></br> (0.6.2) | [Exemples Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Exemples pySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Ubuntu uniquement) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (serveur de bloc-notes multi-utilisateur)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (serveur de bloc-notes multi-utilisateur) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> | |
| Outils de développement, IDE et éditeurs de code :| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) avec plug-in Git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) et [Outils R pour Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio dans DSVM](./dsvm-tools-development.md#visual-studio-community-2017) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1.31) | [Visual Studio Code dans DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.2.50xx) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [RStudio Desktop dans DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio Server dans DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (19.2.3)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [PyCharm dans DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [Juno dans DSVM](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> (8.1.5) |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Git](https://git-scm.com/) et Git Bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> (0.6.2) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [OpenJDK](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0_201) |<span class='green-check'>&#9989;</span></br> (1.8.0_222) | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span></br> (4.7.2) |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/) |<span class='green-check'>&#9989;</span></br> (2.73.55xx) |<span class='red-x'>&#10060;</span>   |
| Kits de développement logiciel (SDK) pour accéder à Azure et à la suite de services Cortana Intelligence |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI Desktop dans DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Outils de gestion et de déplacement de données : | | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> (1.10.1) |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> (8.1.0) |<span class='red-x'>&#10060;</span>   | [Azcopy dans DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Pilote Blob FUSE](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.0.2) | [blobfuse dans DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [Adlcopy dans DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Outil de migration de données Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB dans DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; Outils en ligne de commande Unix/Linux |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Apache Drill](https://drill.apache.org) pour l’exploration des données |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [Apache Drill dans DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Outils Machine Learning : ||||
| &nbsp;&nbsp;&nbsp;&nbsp; Intégration à [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span></br> (0.2.7) |<span class='green-check'>&#9989;</span></br> (1.0.45) | [SDK Azure ML](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0.81) |<span class='green-check'>&#9989;</span></br> (0.80) | [XGBoost dans DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8.1) | [Vowpal Wabbit dans DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3.8) |<span class='green-check'>&#9989;</span></br> (3.8.0) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> (5.1.0) |<span class='green-check'>&#9989;</span></br> (5.2.2) | [Weka dans DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (2.1.2) | [LightGBM dans DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.10.3) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [H20 dans DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Outils de deep learning qui fonctionnent sur un GPU ou un processeur : |  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe](https://github.com/BVLC/caffe) et [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Caffe](./dsvm-deep-learning-ai-frameworks.md#caffe) et [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) dans DSVM |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (5.2.0) | [Chainer dans DSVM](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Pilote CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [Pilote CUDA, cuDNN, NVIDIA dans DSVM](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [Horovod dans DSVM](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io) |<span class='green-check'>&#9989;</span></br> (2.2.4)   |<span class='green-check'>&#9989;</span></br> (2.2.4) | [Keras dans DSVM](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (2.5.1) | [CNTK dans DSVM](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.3.0) | [MXNet dans DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.0.1) | [MXNet Model Server dans DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [nvidia-smi dans DSVM](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.2.0) | [PyTorch dans DSVM](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1.13) |<span class='green-check'>&#9989;</span></br> (1.13) | [TensorFlow dans DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.12.0) | [TensorFlow Serving dans DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (1.0.3) | [Theano dans DSVM](./dsvm-deep-learning-ai-frameworks.md#theano)

