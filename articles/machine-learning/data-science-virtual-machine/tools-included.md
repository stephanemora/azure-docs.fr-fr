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
ms.date: 09/27/2019
ms.openlocfilehash: 79de3406e47b84a6120496acce5bba948fbb1a6d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803545"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Quels sont les outils qui sont inclus dans Azure Data Science Virtual Machine ?

Vous trouverez ci-dessous une liste actualisée des outils inclus dans Data Science Virtual Machine, ainsi que des liens pour comprendre comment chaque outil est configuré.


| **Outil**                                                           | **Machine virtuelle DSVM Windows** | **Machine virtuelle DSVM Linux** | **Notes d’utilisation** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) avec packages courants préinstallés   |<span class='green-check'>&#9989;</span>                     |<span class='green-check'>&#9989;</span>  | [Utilisation de R dans DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition inclut : <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) framework hautes performances parallèle et distribué (et & Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nouveaux algorithmes de machine learning de pointe de Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp; [Opérationnalisation R et Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus avec activation partagée : Excel, Word et PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2.7 et 3.5 avec packages populaires préinstallés    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) avec packages populaires préinstallés                         |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | [Utilisation de Julia dans DSVM](./dsvm-languages.md#julia) |
| Bases de données relationnelles                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | (./dsvm-data-platforms#sql-server-2016-developer-edition.md) |
| Outils de base de données                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Pilotes ODBC/JDBC|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (outil de requête), <br />  bcp, sqlcmd <br />  Pilotes ODBC/JDBC| |
| Analytiques en base de données scalables avec services SQL Server de Machine Learning (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Serveur Jupyter Notebook](https://jupyter.org/) avec les noyaux suivants :                                  |<span class='green-check'>&#9989;</span>     |<span class='green-check'>&#9989;</span> | [Exemples Jupyter Notebook](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Exemples R Jupyter](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Exemples Python Jupyter](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Exemples Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Exemples pySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu uniquement) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (serveur de bloc-notes multi-utilisateur)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (serveur de bloc-notes multi-utilisateur) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu uniquement) | |
| Outils de développement, IDE et éditeurs de code :| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) avec plug-in Git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) et [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio 2019 dans DSVM](./dsvm-tools-development.md#visual-studio-2019) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Visual Studio Code dans DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [RStudio Desktop dans DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio Server dans DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [PyCharm dans DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Juno dans DSVM](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; Vim et Emacs |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; Git et Git Bash |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| Power BI Desktop |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   |
| Kits de développement logiciel (SDK) pour accéder à Azure et à la suite de services Cortana Intelligence |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI Desktop dans DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Outils de gestion et de déplacement de données : | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Explorateur Stockage Azure |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Azcopy dans DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Pilote Blob FUSE](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [blobfuse dans DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Adlcopy dans DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Outil de migration de données Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB dans DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Passerelle de gestion des données Microsoft](https://msdn.microsoft.com/library/dn879362.aspx) : déplacement de données entre un environnement local et le cloud |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Utilisation de DSVM](./dsvm-tools-ingestion.md#microsoft-data-management-gateway) |
| &nbsp;&nbsp;&nbsp;&nbsp; Outils en ligne de commande Unix/Linux |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Apache Drill](https://drill.apache.org) pour l’exploration des données |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Apache Drill dans DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Outils Machine Learning : ||||
| &nbsp;&nbsp;&nbsp;&nbsp; Intégration à [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [SDK Azure ML](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [XGBoost dans DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Vowpal Wabbit dans DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Weka dans DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu uniquement) | [LightGBM dans DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu uniquement) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu uniquement) | [H20 dans DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Outils de deep learning qui fonctionnent sur un GPU ou un processeur : |  |  | Cliquez sur le nom de l’outil ci-dessous pour plus d’informations sur son utilisation |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) (Windows 2016) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |<span class='green-check'>&#9989;</span> (Windows 2016) |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe et Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](./dsvm-deep-learning-ai-frameworks.md#chainer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](./dsvm-deep-learning-ai-frameworks.md#keras)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Pilote CUDA, cuDNN, NVIDIA](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
