---
title: Ingestion des données avec Azure Data Factory
titleSuffix: Azure Machine Learning
description: Découvrez les options disponibles pour construire un pipeline d’ingestion des données avec Azure Data Factory et les avantages de chacune.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98796160"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Ingestion des données avec Azure Data Factory

Dans cet article, vous allez découvrir les options disponibles pour la création d’un pipeline d’ingestion de données avec [Azure Data Factory](../data-factory/introduction.md). Ce pipeline Azure Data Factory est utilisé pour ingérer des données à utiliser avec [Azure Machine Learning](overview-what-is-azure-ml.md). Data Factory vous permet d’extraire, de transformer et de charger (ETL) facilement des données. Une fois les données transformées et chargées dans le stockage, elles peuvent être utilisées pour entraîner vos modèles Machine Learning dans Azure Machine Learning.

Une transformation simple des données peut être effectuée avec les activités et les instruments Data Factory natifs, tels que le [flux de données](../data-factory/control-flow-execute-data-flow-activity.md). Lorsqu’il s’agit de scénarios plus compliqués, les données peuvent être traitées avec du code personnalisé. Par exemple, le code Python ou R.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Comparer les pipelines d’ingestion des données Azure Data Factory 
Il existe plusieurs techniques courantes d’utilisation de Data Factory pour transformer des données lors de l’ingestion. Chaque technique présente des avantages et des inconvénients qui permettent de déterminer si elle est adaptée à un cas d’usage spécifique :

| Technique | Avantages | Inconvénients |
| ----- | ----- | ----- |
| Data Factory + Fonctions Azure | <li> Faible latence, calcul serverless<li>Fonctions avec état<li>Fonctions réutilisables | Convient uniquement au traitement de courte durée |
| Data Factory + composant personnalisé | <li>Calcul parallèle à grande échelle<li>Convient aux algorithmes lourds | <li>Exige l’enveloppement du code dans un fichier exécutable<li>Complexité de la gestion des dépendances et des E/S |
| Data Factory + notebook Azure Databricks |<li> Apache Spark<li>Environnement Python natif |<li>Peut être coûteuse<li>La création initiale de clusters prend du temps et ajoute de la latence

## <a name="azure-data-factory-with-azure-functions"></a>Azure Data Factory avec des fonctions Azure

Azure Functions vous permet d’exécuter de petits morceaux de code (« fonctions ») sans vous préoccuper de l’infrastructure de l’application. Dans cette option, les données sont traitées avec du code Python personnalisé enveloppé dans une fonction Azure. 

La fonction est appelée à l’aide de l’[activité Azure Data Factory Azure Functions](../data-factory/control-flow-azure-function-activity.md). Cette approche est une bonne option pour les transformations légères de données. 

![Le diagramme illustre un pipeline Azure Data Factory, avec une fonction Azure et l’exécution du pipeline de ML, ainsi qu’un pipeline Azure Machine Learning, avec un modèle d’entraînement et la façon dont ils interagissent avec les données brutes et les données préparées.](media/how-to-data-ingest-adf/adf-function.png)



* Avantages :
    * Les données sont traitées sur un calcul serverless avec une latence relativement faible
    * Le pipeline Data Factory peut appeler une [fonction Azure durable](../azure-functions/durable/durable-functions-overview.md) qui peut implémenter un workflow sophistiqué de transformation des données 
    * Les détails de la transformation de données sont extraits par la fonction Azure qui peut être réutilisée et appelée à partir d’autres emplacements
* Inconvénients :
    * Les fonctions Azure Functions doivent être créées avant d’être utilisées avec ADF
    * Azure Functions n’est utile que pour le traitement de données de courte durée

## <a name="azure-data-factory-with-custom-component-activity"></a>Azure Data Factory avec une activité Composant personnalisé

Dans cette option, les données sont traitées avec du code Python personnalisé enveloppé dans un fichier exécutable. Il est appelé à l’aide d’une [activité Azure Data Factory Composant personnalisé](../data-factory/transform-data-using-dotnet-custom-activity.md). Cette approche est plus adaptée aux données volumineuses que la technique précédente.

![Le diagramme illustre un pipeline Azure Data Factory, avec un composant personnalisé et l’exécution du pipeline de ML, ainsi qu’un pipeline Azure Machine Learning, avec un modèle d’apprentissage et la façon dont ils interagissent avec les données brutes et les données préparées.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Avantages :
    * Les données sont traitées sur le pool [Azure Batch](../batch/batch-technical-overview.md), qui permet un calcul parallèle à grande échelle et à haute performance
    * Peut être utilisée pour exécuter des algorithmes lourds et traiter de grandes quantités de données
* Inconvénients :
    * Le pool Azure Batch doit être créé avant d’être utilisé avec Data Factory
    * Repose sur l’ingénierie liée à l’enveloppement du code Python dans un fichier exécutable. Complexité de la gestion des dépendances et des paramètres entrée/sortie

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Data Factory avec un notebook Python Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) est une plateforme d’analytique basée sur Apache Spark dans le cloud Microsoft.

Dans cette technique, la transformation des données est effectuée par un [notebook Python](../data-factory/transform-data-using-databricks-notebook.md) exécuté sur un cluster Azure Databricks. C’est probablement l’approche la plus courante qui tire parti de toute la puissance d’un service Azure Databricks. Elle est conçue pour le traitement des données distribuées à grande échelle.

![Le diagramme illustre un pipeline Azure Data Factory, avec Azure Databricks Python et l’exécution du pipeline de ML, ainsi qu’un pipeline Azure Machine Learning, avec un modèle d’apprentissage et la façon dont ils interagissent avec les données brutes et les données préparées.](media/how-to-data-ingest-adf/adf-databricks.png)

* Avantages :
    * Les données sont transformées sur le service Azure de traitement des données le plus puissant, qui est assisté par l’environnement Apache Spark.
    * Prise en charge native de Python avec les infrastructures et bibliothèques de science des données, notamment TensorFlow, PyTorch etscikit-learn.
    * Il n’est pas nécessaire d’envelopper le code Python dans des fonctions ou des modules exécutables. Le code fonctionne comme tel quel.
* Inconvénients :
    * L’infrastructure Azure Databricks doit être créée avant d’être utilisée avec Data Factory
    * Peut coûter cher en fonction de la configuration Azure Databricks
    * La rotation des clusters de calcul à partir du mode « froid » prend un certain temps, ce qui confère une latence élevée à la solution 
    

## <a name="consume-data-in-azure-machine-learning"></a>Consommer des données dans Azure Machine Learning 

Le pipeline Data Factory enregistre les données préparées dans votre stockage cloud (par exemple, un blob Azure ou un lac de données Azure). <br>
Consommez vos données préparées dans Azure Machine Learning de la manière suivante : 

* En appelant un pipeline Azure Machine Learning à partir de votre pipeline Data Factory.<br>**OR**
* En créant un [magasin de données Azure Machine Learning](how-to-access-data.md#create-and-register-datastores) et un [jeu de données Azure machine Learning](how-to-create-register-datasets.md) pour les utiliser ultérieurement.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Appeler un pipeline Azure Machine Learning à partir de Data Factory

Cette méthode est recommandée pour les [workflows Machine Learning Operations (MLOps)](concept-model-management-and-deployment.md#what-is-mlops). Si vous ne voulez pas configurer un pipeline Azure Machine Learning, consultez [Lire des données directement à partir du stockage](#read-data-directly-from-storage).

À chaque exécution du pipeline Data Factory : 

1. Les données sont enregistrées à un emplacement différent dans le stockage. 
1. Pour transmettre l’emplacement à Azure Machine Learning, le pipeline Data Factory appelle un [pipeline Azure Machine Learning](concept-ml-pipelines.md). Lors de l’appel du pipeline ML, l’emplacement des données et l’ID d’exécution sont envoyés en tant que paramètres. 
1. Le pipeline ML peut ensuite créer un magasin de données et un jeu de données Azure Machine Learning avec l’emplacement des données. Pour plus d’informations, consultez [Exécuter des pipelines Azure Machine Learning dans Data Factory](../data-factory/transform-data-machine-learning-service.md).

![Le diagramme illustre un pipeline Azure Data Factory et un pipeline Azure Machine Learning, et la façon dont ils interagissent avec les données brutes et les données préparées. Le pipeline Data Factory alimente les données dans la base de données préparée, qui alimente un magasin de données, qui alimente les jeux de données de l’espace de travail Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> Les jeux de données [prennent en charge le contrôle de version](./how-to-version-track-datasets.md), de sorte que le pipeline ML peut inscrire une nouvelle version du jeu de données qui pointe vers les données les plus récentes à partir du pipeline ADF.

Une fois que les données sont accessibles par le biais d’un magasin de données ou d’un jeu de données, vous pouvez les utiliser pour effectuer l’apprentissage d’un modèle ML. Le processus d’apprentissage peut faire partie du même pipeline ML appelé à partir d’ADF. Il peut également s’agir d’un processus distinct, tel que l’expérimentation dans un notebook Jupyter.

Étant donné que les jeux de données prennent en charge le contrôle de version et que chaque exécution à partir du pipeline crée une nouvelle version, il est facile de comprendre quelle version des données a été utilisée pour effectuer l’apprentissage d’un modèle.

### <a name="read-data-directly-from-storage"></a>Lire des données directement à partir du stockage

Si vous ne voulez pas créer un pipeline ML, vous pouvez accéder aux données directement à partir du compte de stockage dans lequel vos données préparées sont enregistrées avec un magasin de données et un jeu de données Azure Machine Learning. 

Le code Python suivant montre comment créer un magasin de données qui se connecte au stockage Azure DataLake Generation 2. [Découvrez-en plus sur les magasins de données et l’emplacement des autorisations du principal de service](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Ensuite, créez un jeu de données pour référencer les fichiers que vous voulez utiliser dans votre tâche de Machine Learning. 

Le code suivant crée un TabularDataset à partir d’un fichier csv, `prepared-data.csv`. Découvrez-en plus sur les [types de jeux de données et les formats de fichiers acceptés](how-to-create-register-datasets.md#dataset-types). 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

À partir de là, utilisez `prepared_dataset` pour référencer vos données préparées, comme dans vos scripts d’entraînement. Découvrez comment [entraîner des modèles avec des jeux de données dans Azure Machine Learning](./how-to-train-with-datasets.md).

## <a name="next-steps"></a>Étapes suivantes

* [Exécuter un notebook Databricks dans Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Accéder aux données dans les services de stockage Azure](./how-to-access-data.md#create-and-register-datastores)
* [Entraîner des modèles avec des jeux de données dans Azure Machine Learning](./how-to-train-with-datasets.md)
* [DevOps pour un pipeline d’ingestion des données](./how-to-cicd-data-ingestion.md)