---
title: Exécuter des charges de travail Azure Machine Learning avec Machine learning automatisé (AutoML) sur Apache Spark dans Azure HDInsight
description: Découvrez comment exécuter des charges de travail Azure Machine Learning avec Machine learning automatisé (AutoML) sur Apache Spark dans Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 896cae9b7fc43765e340ba3b92351e04b5512efd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762549"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Exécuter des charges de travail Azure Machine Learning avec Machine learning automatisé (AutoML) sur Apache Spark dans Azure HDInsight

Azure Machine Learning est un outil collaboratif fonctionnant par glisser-déplacer qui vous permet de générer, tester et déployer des solutions d’analytique prédictive à partir de vos données. Azure Machine Learning publie des modèles en tant que services web pouvant facilement être consommés par des applications personnalisées ou des outils décisionnels tels qu’Excel. Machine learning automatisé (AutoML) vous permet de créer des modèles machine learning de haute qualité à l’aide d’une optimisation et d’une automatisation intelligentes. AutoML décide de l’algorithme approprié et des hyperparamètres à utiliser pour des types de problèmes spécifiques.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Installer Azure Machine Learning sur un cluster HDInsight

> [!Note]
> L’espace de travail Azure ML est actuellement disponible dans les régions suivantes : eastus, eastus2 et westcentralus. Le cluster HDInsight doit également être créé dans l’une de ces régions.

Pour obtenir des tutoriels généraux sur Azure Machine Learning et Machine learning automatisé, consultez [Tutoriel : Créer votre première expérience de science des données dans Azure Machine Learning Studio](../../machine-learning/studio/create-experiment.md) et [Tutoriel : Utiliser le Machine Learning automatisé pour générer votre modèle de régression](../../machine-learning/service/tutorial-auto-train-models.md).
Pour installer AzureML sur votre cluster Azure HDInsight, exécutez l’action de script - [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh) - sur les nœuds principaux et les nœuds worker d’un cluster HDInsight 3.6 Spark 2.3.0 (recommandé). Vous pouvez exécuter cette action de script dans le cadre du processus de création du cluster ou sur un cluster existant par le biais du portail Azure.

Pour plus d’informations sur les actions de script, consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’actions de script](../hdinsight-hadoop-customize-cluster-linux.md). En plus d’installer les packages Azure Machine Learning et les dépendances, le script télécharge aussi un exemple de bloc-notes Jupyter (à l’emplacement `HdiNotebooks/PySpark` du magasin par défaut). Ce bloc-notes Jupyter montre comment utiliser un classifieur de machine learning automatisé pour un problème de classification simple.

> [!Note]
> Les packages Azure Machine Learning sont installés dans l’environnement conda Python3. Le bloc-notes Jupyter installé doit être exécuté à l’aide du noyau PySpark3.

## <a name="authentication-for-workspace"></a>Authentification pour l’espace de travail

La création d’espace de travail et la soumission d’expérience nécessitent un jeton d’authentification. Ce jeton peut être généré à l’aide d’une [application Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Un [utilisateur Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) peut également générer le jeton d’authentification requis, si l’authentification multifacteur n’est pas activée sur le compte.  

L’extrait de code suivant crée un jeton d’authentification à l’aide d’une **application Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
                )
```
L’extrait de code suivant crée un jeton d’authentification à l’aide d’un **utilisateur Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>Chargement de jeu de données

Machine learning automatisé sur Spark utilise des **dataflows**, qui sont des opérations immuables et évaluées de manière différée sur des données.  Un dataflow peut charger un jeu de données à partir d’un objet blob avec accès en lecture public, ou à partir d’une URL d’objet blob avec un jeton SAP.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Vous pouvez également inscrire le magasin de données auprès de l’espace de travail à l’aide d’une inscription unique.

## <a name="experiment-submission"></a>Soumission d’expérimentations

Dans la configuration de machine learning automatisé, la propriété `spark_context` doit être définie pour que le package s’exécute en mode distribué. La propriété `concurrent_iterations`, qui est le nombre maximal d’itérations exécutées en parallèle, doit avoir pour valeur un nombre inférieur aux cœurs d’exécuteur de l’application Spark.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la motivation pour le machine learning automatisé, consultez [Release models at pace using Microsoft’s automated machine learning!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/).
* [Projet AutoML de Microsoft Research](https://www.microsoft.com/research/project/automl/)