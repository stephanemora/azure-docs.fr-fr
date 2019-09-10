---
title: Qu’est-ce que la machine virtuelle Science des données Azure ?
description: Scénarios et composants d’analytique clés pour Azure Data Science Virtual Machine Windows et Linux.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: 3295a59ee4496d332f0d886c89ca900ab6b4bcd1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191897"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Qu’est-ce qu’Azure Data Science Virtual Machine pour Linux et Windows ?

La Data Science Virtual Machine (DSVM) est une image de machine virtuelle personnalisée sur la plateforme cloud Azure spécialement conçue pour la science des données. Elle inclut de nombreux outils de science des données populaires et d’autres outils sont préinstallés et préconfigurés afin d’accélérer la création d’applications intelligentes à des fins d’analyse avancée. 

Les configurations d’outils sont rigoureusement testées par des scientifiques des données et des développeurs de Microsoft, ainsi que par la communauté de la science des données. Ce test aide à garantir la stabilité et la viabilité générale.

La Data Science Virtual Machine est disponible sur :
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16.04 LTS et CentOS 7.4

> [!NOTE]
> Tous les outils de machine virtuelle pour le Deep Learning ont été intégrés à la DSVM. 


## <a name="what-can-i-do-with-the-dsvm"></a>À quoi sert la DSVM ?
L’objectif de la DSVM est de fournir aux experts en données, tous niveaux de compétence et rôles confondus, un environnement de science des données préconfiguré et totalement intégré. Au lieu de déployer un espace de travail comparable par vous-même, vous pouvez provisionner une DSVM. Ce choix peut vous faire gagner des jours, voire des _semaines_, sur les processus d’installation, de configuration et de gestion des packages. Une fois votre DSVM alloué, vous pouvez commencer immédiatement à travailler sur votre projet de science des données.

La DSVM a été conçue et configurée pour fonctionner dans une grande variété de scénarios. Vous pouvez faire évoluer ou réduire votre environnement en fonction des besoins de votre projet. Vous pouvez également utiliser le langage de votre choix pour programmer les tâches scientifiques et installer d’autres outils pour que le système convienne à vos besoins.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Bureau d’analyse préconfiguré dans le cloud
La DSVM fournit une configuration de référence pour les équipes de science des données souhaitant remplacer leurs ordinateurs de bureau locaux par un bureau cloud managé. Cette base garantit que tous les scientifiques de données de l’équipe disposent d’une configuration cohérente permettant de vérifier les expériences et encourageant la collaboration. Elle réduit également les coûts en allégeant la charge de travail de l’administrateur système. Cela permet de raccourcir le temps nécessaire à l’évaluation, à l’installation et à la gestion des packages logiciels pour l’analytique avancée.

### <a name="data-science-training-and-education"></a>Formation et éducation de la science des données
Les formateurs et instructeurs en entreprise qui enseignent la science des données fournissent généralement une image de machine virtuelle. Avec l’image, les étudiants disposent d’une installation cohérente et les exemples fonctionnent comme prévu. 

La DSVM crée un environnement à la demande avec une configuration cohérente qui simplifie le support et les soucis de compatibilité. Ceci est d’autant plus bénéfique dans les cas où ces environnements doivent être créés fréquemment, en particulier lors de formations plus courtes.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Flexibilité à la demande pour les projets d’envergure
Les concours de science des données ou la modélisation et l’exploration de données à grande échelle nécessitent une augmentation de la capacité matérielle, généralement sur une courte durée. La DSVM permet de répliquer rapidement et à la demande l’environnement de science des données sur des serveurs montés offrant ainsi des expériences d’exécution des ressources informatiques hautes performances.

### <a name="custom-compute-power-for-azure-notebooks"></a>Puissance de calcul personnalisée pour Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) est un service hébergé gratuit pour développer, exécuter et partager des blocs-notes Jupyter dans le cloud sans aucune installation. Le niveau de service gratuit est limité à 4 Go de mémoire et à 1 Go de données. 

Pour libérer toutes les limites, vous pouvez joindre un projet Notebooks à une DSVM ou à toute autre machine virtuelle s’exécutant sur un serveur Jupyter. Si vous vous connectez à Azure Notebooks avec un compte à l’aide d’Azure Active Directory (par exemple, un compte d’entreprise), Notebooks affiche automatiquement les DSVM dans tous les abonnements associés à ce compte. Vous pouvez [attacher une DSVM à Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) pour développer la puissance de calcul disponible.

### <a name="short-term-experimentation-and-evaluation"></a>Expérimentation et évaluation à court terme
Vous pouvez utiliser la DSVM pour évaluer ou découvrir des outils comme ceux-ci, avec un minimum d’efforts d’installation :

- Microsoft Machine Learning Server
- SQL Server
- Outils Visual Studio
- Jupyter
- Outils de deep learning et de machine learning
- Nouveaux outils populaires dans la communauté 

Étant donné que vous pouvez configurer rapidement la DSVM, vous pouvez l’appliquer dans d’autres scénarios d’utilisation à court terme. Ces scénarios incluent la réplication d’expériences publiées, l’exécution de démonstrations et le suivi de procédures pas-à-pas dans des sessions en ligne et des tutoriels de conférence.

### <a name="deep-learning"></a>Apprentissage approfondi
Dans la DSVM, vos modèles d’entraînement peuvent utiliser des algorithmes de deep learning sur du matériel basé sur des unités de traitement graphique (GPU). En tirant parti des fonctionnalités de mise à l’échelle des machines virtuelles de la plateforme Azure, la DSVM vous aide à utiliser le matériel basé sur GPU dans le cloud en fonction de vos besoins. Vous pouvez basculer vers une machine virtuelle basée sur GPU quand vous entraînez de grands modèles ou que vous avez besoin d’une grande rapidité de calcul, mais souhaitez garder le même disque de système d’exploitation.  

L’édition Windows Server 2016 de la DSVM est préinstallée avec les pilotes GPU, les frameworks et les versions GPU des frameworks de deep learning. Sur l’édition Linux, le deep learning sur GPU est activé sur les DSVM CentOS et Ubuntu. 

Vous pouvez également déployer l’édition Ubuntu, CentOS ou Windows 2016 de la DSVM sur une machine virtuelle Azure qui n’est pas basée sur des unités GPU. Dans ce cas, toutes les infrastructures d’apprentissage profond repasseront en mode UC.
 
[Découvrez-en plus sur les frameworks de deep learning et d’intelligence artificielle disponibles](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Ce qui est inclut dans la DSVM
De nombreux outils de science des données et d’apprentissage approfondi populaires sont déjà installés et configurés sur la machine virtuelle Science des données. Elle inclut également des outils simplifiant l’utilisation de différents produits de données et d’analyse Azure. Ces produits incluent Microsoft Machine Learning Server (R, Python) pour la création de modèles prédictifs, et SQL Server 2017 pour l’exploration à grande échelle des jeux de données. La DSVM comprend d’autres outils de la communauté open source et de Microsoft ainsi qu’un [exemple de code et des notebooks](dsvm-samples-and-walkthroughs.md). 

Voici une liste d’outils et de plateformes :
+ [Langages de programmation pris en charge](dsvm-languages.md)

+ [Plateformes de données prises en charge](dsvm-data-platforms.md)

+ [IDE et outils de développement](dsvm-tools-development.md)

+ [Frameworks de deep learning et d’intelligence artificielle](dsvm-deep-learning-ai-frameworks.md)

+ [Outils de science des données et d’apprentissage automatique](dsvm-ml-data-science-tools.md)

+ [Outils d’ingestion de données](dsvm-tools-ingestion.md)

+ [Outils d’exploration et de visualisation de données](dsvm-tools-explore-and-visualize.md)

Le tableau suivant détaille et compare les principaux composants inclus dans les éditions Windows et Linux de la machine virtuelle Science des données.

| **Outil**                                                           | **Édition Windows** | **Édition Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) avec packages courants préinstallés   |O                      | O             |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition inclut : <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) framework hautes performances parallèle et distribué (et & Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nouveaux algorithmes de machine learning de pointe de Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp; [Opérationnalisation R et Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |O                      | O |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus avec activation partagée : Excel, Word et PowerPoint   |O                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7 et 3.5 avec packages populaires préinstallés    |O                      |O              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) avec packages populaires préinstallés                         |O                      |O              |
| Bases de données relationnelles                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Outils de base de données                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Pilotes ODBC/JDBC|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (outil de requête), <br />  bcp, sqlcmd <br />  Pilotes ODBC/JDBC|
| Analytiques en base de données scalables avec services SQL Server de Machine Learning (R, Python) | O     |N              |
| [Serveur Jupyter Notebook](https://jupyter.org/) avec les noyaux suivants :                                  | O     | O |
|     &nbsp;&nbsp;&nbsp;&nbsp; R | O | O |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python | O | O |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia | O | O |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark | O | O |
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | O (Ubuntu uniquement) |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     | N | O |
| JupyterHub (serveur de bloc-notes multi-utilisateur)| N | O |
| JupyterLab (serveur de bloc-notes multi-utilisateur) | N | O (Ubuntu uniquement) |
| Outils de développement, IDE et éditeurs de code :| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) avec plug-in Git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) et [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | O | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)| O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; Vim et Emacs | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; Git et Git Bash | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework | O | N |
| Power BI Desktop | O | N |
| Kits de développement logiciel (SDK) pour accéder à Azure et à la suite de services Cortana Intelligence | O | O |
| Outils de gestion et de déplacement de données : | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Explorateur Stockage Azure | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure) | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell | O | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | O | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Pilote Blob FUSE](https://github.com/Azure/azure-storage-fuse) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | O | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Outil de migration de données Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | O | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Passerelle de gestion des données Microsoft](https://msdn.microsoft.com/library/dn879362.aspx) : déplacement de données entre un environnement local et le cloud | O | N |
| &nbsp;&nbsp;&nbsp;&nbsp; Outils en ligne de commande Unix/Linux | O | O |
| [Apache Drill](https://drill.apache.org) pour l’exploration des données | O | O |
| Outils Machine Learning : |||
| &nbsp;&nbsp;&nbsp;&nbsp; Intégration à [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) | N | O (Ubuntu uniquement) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) | N | O (Ubuntu uniquement) |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | O (Ubuntu uniquement) |
| Outils de deep learning qui fonctionnent sur un GPU ou un processeur : |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | O | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org/) | O (Windows 2016) | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/uber/horovod) | N | O (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.io/) | O (Windows 2016) | O|
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe et Caffe2](https://github.com/caffe2/caffe2) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org/) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Torch](http://torch.ch/) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io/)| N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org/)| N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorRT](https://developer.nvidia.com/tensorrt) | N | O |
| &nbsp;&nbsp;&nbsp;&nbsp; [Pilote CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit) | O | O |

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

+ Windows :
  + [Configurer une DSVM Windows](provision-vm.md)
  + [Dix choses que vous pouvez effectuer sur une DSVM Windows](vm-do-ten-things.md)

+ Linux :
  + [Configurer une DSVM Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Configurer une DSVM Linux (CentOS)](linux-dsvm-intro.md)
  + [Science des données sur une DSVM Linux](linux-dsvm-walkthrough.md)
