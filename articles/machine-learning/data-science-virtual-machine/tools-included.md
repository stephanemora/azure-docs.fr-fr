---
title: Outils inclus dans Azure Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Liste des outils inclus dans les images DSVM Windows et Ubuntu
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/12/2021
ms.custom: contperf-fy20q4
ms.openlocfilehash: 63269512a33ce2743d1082001525030c85bc8a4a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110462885"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Quels sont les outils qui sont inclus dans Azure Data Science Virtual Machine ?

Data Science Virtual Machine est un moyen simple d’explorer des données et d’utiliser le Machine Learning dans le cloud. Les machines virtuelles de type Data Science Virtual Machine sont préconfigurées avec le système d’exploitation complet, les correctifs de sécurité, les pilotes et des logiciels de science des données et de développement très répandus. Vous pouvez choisir l’environnement matériel, qu’il soit constitué de machines économiques centrées sur le processeur ou de machines très puissantes dotées de plusieurs GPU, d’un stockage NVMe et de grandes quantités de mémoire. Pour les ordinateurs avec GPU, tous les pilotes sont installés, toutes les infrastructures de Machine Learning sont examinées pour déterminer si leur version est compatible avec les GPU, et l’accélération est activée dans tous les logiciels d’application qui prennent en charge les GPU.

Data Science Virtual Machine est fourni avec les outils de science des données les plus utiles préinstallés. 

## <a name="build-deep-learning-and-machine-learning-solutions"></a>Générer des solutions de deep learning et de machine learning

| Outil | DSVM Windows Server 2019 | DSVM Ubuntu 18.04 | Notes d’utilisation |
|--|:-:|:-:|:-:|
| [Pilote CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [Pilote CUDA, cuDNN, NVIDIA dans DSVM](./dsvm-tools-deep-learning-frameworks.md#cuda-cudnn-nvidia-driver) |
| [Horovod](https://github.com/horovod/horovod) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [Horovod dans DSVM](./dsvm-tools-deep-learning-frameworks.md#horovod) |
| [NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [nvidia-smi dans DSVM](./dsvm-tools-deep-learning-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| [PyTorch](https://pytorch.org) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [PyTorch dans DSVM](./dsvm-tools-deep-learning-frameworks.md#pytorch) |
| [TensorFlow](https://www.tensorflow.org) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [TensorFlow dans DSVM](./dsvm-tools-deep-learning-frameworks.md#tensorflow) |
| Intégration à [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | <span class='green-check'>&#9989;</span></br> (SDK Python, exemples) | <span class='green-check'>&#9989;</span></br> (SDK Python/R, CLI, exemples) | [SDK Azure ML](./dsvm-tools-data-science.md#azure-machine-learning-sdk-for-python) |
| [XGBoost](https://github.com/dmlc/xgboost) | <span class='green-check'>&#9989;</span></br> (prise en charge de la configuration CUDA) | <span class='green-check'>&#9989;</span></br> (prise en charge de la configuration CUDA) | [XGBoost dans DSVM](./dsvm-tools-data-science.md#xgboost) |
| [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> | [Vowpal Wabbit dans DSVM](./dsvm-tools-data-science.md#vowpal-wabbit) |
| [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | <span class='red-x'>&#10060;</span> | <span class='red-x'>&#10060;</span> |  |
| LightGBM | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (Prise en charge MPI, GPU) |  |
| H2O | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| CatBoost | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Intel MKL | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| OpenCV | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Dlib | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Docker | <span class='green-check'>&#9989;</span> <br/> (Conteneurs Windows uniquement) | <span class='green-check'>&#9989;</span> |  |
| Nccl | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Rattle | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| ONNX Runtime | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |



## <a name="store-retrieve-and-manipulate-data"></a>Stocker, récupérer et manipuler des données

| Outil | DSVM Windows Server 2019 | DSVM Ubuntu 18.04 | Notes d’utilisation |
|--|-:|:-:|:-:|
| Bases de données relationnelles | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer Edition | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer Edition | [SQL Server dans DSVM](./dsvm-tools-data-platforms.md#sql-server-developer-edition) |
| Outils de base de données | SQL Server Management Studio<br/> SQL Server Integration Services<br/> [bcp, sqlcmd](/sql/tools/command-prompt-utility-reference-database-engine) | [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (outil de requête), <br /> bcp, sqlcmd <br /> Pilotes ODBC/JDBC |  |
| [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [Azure CLI](/cli/azure) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [AZCopy](../../storage/common/storage-use-azcopy-v10.md) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> | [AzCopy dans DSVM](./dsvm-tools-ingestion.md#azcopy) |
| [Pilote Blob FUSE](https://github.com/Azure/azure-storage-fuse) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [blobfuse dans DSVM](./dsvm-tools-ingestion.md#blobfuse) |
| [Outil de migration de données Azure Cosmos DB](../../cosmos-db/import-data.md) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [Cosmos DB dans DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| Outils en ligne de commande Unix/Linux | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Apache Spark 3.1 (autonome) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> |  |

## <a name="program-in-python-r-julia-and-nodejs"></a>Programmer en Python, R, Julia et Node.js

| Outil | DSVM Windows Server 2019 | DSVM Ubuntu 18.04 | Notes d’utilisation |
|--|:-:|:-:|:-:|
| [CRAN-R](https://cran.r-project.org/) avec packages courants préinstallés | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| [Anaconda Python](https://www.continuum.io/) avec packages populaires préinstallés | <span class='green-check'>&#9989;</span><br/> (Miniconda) | <span class='green-check'>&#9989;</span></br> (Miniconda) |  |
| [Julia (Julialang)](https://julialang.org/) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterHub (serveur de bloc-notes multi-utilisateur) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterLab (serveur de bloc-notes multi-utilisateur) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| Node.js | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| [Serveur Jupyter Notebook](https://jupyter.org/) avec les noyaux suivants : | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span> | [Exemples Jupyter Notebook](./dsvm-samples-and-walkthroughs.md) |
| &nbsp;&nbsp;&nbsp;&nbsp; R |  |  | [Exemples R Jupyter](./dsvm-samples-and-walkthroughs.md#r-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Python |  |  | [Exemples Python Jupyter](./dsvm-samples-and-walkthroughs.md#python-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Julia |  |  | [Exemples Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; PySpark |  |  | [Exemples pySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml) |

Les **DSVM Ubuntu 18.04 et Windows Server 2019** présentent les noyaux Jupyter suivants :</br> 
* Python 3.8 – par défaut</br>  
* Python 3.8 – PyTorch</br>  
* Python 3.8 – TensorFlow</br>  
* Python 3.6 - AzureML - TensorFlow</br>  
* Python 3.6 - AzureML - PyTorch</br>  
* Python 3.6 - AzureML - AutoML</br>  
* R</br>  
* Python 3.7 - Spark (local)</br>  
* Julia 1.2.0</br>  
* R Spark – HDInsight</br>  
* Scala Spark – HDInsight</br>  
* Python 3 Spark – HDInsight</br>  

Les **DSVM Ubuntu 18.04 et Windows Server 2019** présentent les environnements conda suivants :</br> 
* py38_default  </br>
* py38_tensorflow </br> 
* py38_pytorch  </br>
* azureml_py36_tensorflow  </br>
* azureml_py36_pytorch  </br>
* azureml_py36_automl  </br>


## <a name="use-your-preferred-editor-or-ide"></a>Utiliser votre éditeur ou IDE préféré

| Outil | DSVM Windows Server 2019 | DSVM Ubuntu 18.04 | Notes d’utilisation |
|--|:-:|:-:|:-:|
| [Notepad++](https://notepad-plus-plus.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Nano](https://www.nano-editor.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Visual Studio 2019 Community Edition](https://www.visualstudio.com/community/) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [Visual Studio dans DSVM](dsvm-tools-development.md#visual-studio-community-edition) |
| [Visual Studio Code](https://code.visualstudio.com/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [Visual Studio Code dans DSVM](./dsvm-tools-development.md#visual-studio-code) |
| [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [RStudio Desktop dans DSVM](./dsvm-tools-development.md#rstudio-desktop) |
| [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) ; <br/> (désactivé par défaut) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [PyCharm dans DSVM](./dsvm-tools-development.md#pycharm) |
| [IntelliJ IDEA](https://www.jetbrains.com/idea/) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [Vim](https://www.vim.org) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Git](https://git-scm.com/) et Git Bash | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [OpenJDK](https://openjdk.java.net) 11 | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| .NET Framework | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Kit de développement logiciel (SDK) Azure | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |

## <a name="organize--present-results"></a>Organiser et présenter les résultats

| Outil | DSVM Windows Server 2019 | DSVM Ubuntu 18.04 | Notes d’utilisation |
|--|:-:|:-:|:-:|
| [Microsoft 365](https://www.microsoft.com/microsoft-365) (Word, Excel, PowerPoint) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Microsoft Teams](https://www.microsoft.com/microsoft-teams) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Power BI Desktop](https://powerbi.microsoft.com/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Navigateur Microsoft Edge | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
