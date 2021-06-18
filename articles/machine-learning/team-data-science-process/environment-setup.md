---
title: Configuration d’environnements de science des données dans Azure - Processus TDSP
description: Configurer des environnements de science des données dans Azure à utiliser dans le processus TDSP (Team Data Science Process).
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8d8244384abe42819fed61329409e150d334c8d8
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111814304"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Configuration d’environnements de science des données à utiliser dans le processus TDSP (Team Data Science Process)
Le processus TDSP (Team Data Science Process) utilise différents environnements de science de données pour le stockage, le traitement et l’analyse de données. Il peut s’agir par exemple de Stockage Blob Azure, de plusieurs types de machines virtuelles Azure, de clusters HDInsight (Hadoop) et d’espaces de travail Azure Machine Learning. Le choix de l’environnement à utiliser dépend du type et de la quantité des données à modéliser, ainsi que de la destination cible de ces données dans le cloud. 

* Pour plus d’informations sur les questions à prendre en compte lors de cette décision, consultez la page [Planifier votre environnement de science des données Azure Machine Learning](plan-your-environment.md). 
* Pour accéder à un catalogue de scénarios que vous pouvez rencontrer lorsque vous effectuez des analyses avancées, consultez la page [Scénarios du processus TDSP (Team Data Science Process)](plan-sample-scenarios.md)

Les articles suivants expliquent comment configurer les différents environnements de science de données utilisés par le processus TDSP (Team Data Science Process).

* [Compte de stockage Azure](../../storage/common/storage-account-create.md)
* Installation de R et de Python sur des clusters HDInsight
  * [Présentation de Spark sur HDInsight](../../hdinsight/spark/apache-spark-overview.md)
  * [Cluster HDInsight (Hadoop)](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
  * [Noyaux PySpark pour le Notebook Jupyter](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)
  * [R Server sur HDInsight](../../hdinsight/r-server/r-server-overview.md)
  * [Commencer à utiliser R Server sur HDInsight](../../hdinsight/r-server/r-server-overview.md)
* [Espace de travail Azure Machine Learning Studio (classique)](../classic/create-workspace.md)

La **machine virtuelle pour la science des données de Microsoft (DSVM)** est également disponible sous forme d’image de machine virtuelle Azure. Cette machine virtuelle est préinstallée et configurée avec plusieurs outils couramment utilisés dans le cadre de l’analyse de données et de l’apprentissage automatique. La machine virtuelle DSVM est disponible sur Windows et Linux. Pour plus d’informations, consultez [Présentation de la machine virtuelle Science des données basée sur le cloud pour Linux et Windows](../data-science-virtual-machine/overview.md).

Découvrez comment créer :

- [Machine virtuelle DSVM Windows](../data-science-virtual-machine/provision-vm.md)
- [Machine virtuelle DSVM Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [Machine virtuelle DSVM CentOS](../data-science-virtual-machine/release-notes.md)