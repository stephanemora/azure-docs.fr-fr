---
title: Accès aux données basé sur l’identité pour les services de stockage sur Azure
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser l’accès aux données basé sur l’identité pour vous connecter aux services de stockage sur Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 68d07481e228b1d1b2f4571a783f925add261cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520011"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>Se connecter au stockage avec l’accès aux données basé sur l’identité (préversion)

>[!IMPORTANT]
> Les fonctionnalités présentées dans cet article sont en préversion et doivent être considérées comme des fonctionnalités [expérimentales](/python/api/overview/azure/ml/#stable-vs-experimental) en préversion pouvant changer à tout moment.

Dans cet article, vous apprenez à vous connecter aux services de stockage sur Azure en utilisant l’accès aux données basé sur l’identité et aux magasins de données Azure Machine Learning via le [kit SDK Python d’Azure Machine Learning](/python/api/overview/azure/ml/intro).  

En règle générale, les magasins de données utilisent un accès aux données basé sur des informations d’identification pour confirmer l’autorisation d’accéder au service de stockage. Ils conservent les informations de connexion, comme votre ID d’abonnement et votre autorisation de jeton, dans votre [coffre de clés](https://azure.microsoft.com/services/key-vault/) qui est associé à l’espace de travail. Lorsque vous créez un magasin de données qui utilise l’accès aux données basé sur l’identité, votre connexion Azure (le [jeton Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)) est utilisée pour confirmer votre autorisation d’accès au service de stockage. Dans ce scénario, aucune information d’authentification n’est enregistrée, et seules les informations du compte de stockage sont stockées dans le magasin de données. 

Pour créer des magasins de données qui utilisent l’authentification basée sur des informations d’identification, par exemple avec des clés d’accès ou des principaux de service, consultez [Se connecter aux services de stockage sur Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Accès aux données basé sur l’identité dans Azure Machine Learning

Il existe deux zones dans lesquelles appliquer l’accès aux données basé sur l’identité dans Azure Machine Learning. En particulier, lorsque vous travaillez avec des données confidentielles et que vous avez besoin d’une gestion plus précise de l’accès aux données. 

1. Accès aux services de stockage.
1. Entraînement des modèles Machine Learning avec des données privées.

### <a name="accessing-storage-services"></a>Accès aux services de stockage

Vous pouvez connecter des services de stockage par le biais de l’accès aux données basé sur l’identité à des magasins de données Azure Machine Learning ou à des [jeux de données Azure Machine Learning](how-to-create-register-datasets.md). 

En règle générale, vos informations d’authentification sont conservées dans un magasin de données, qui est utilisé pour garantir votre autorisation d’accéder au service de stockage. Lorsque ces informations d’identification sont inscrites auprès des magasins de données, tout utilisateur disposant du rôle de *Lecteur* d’espace de travail est en mesure de les récupérer, ce qui peut présenter un problème de sécurité pour certaines organisations. [Apprenez-en davantage sur le rôle de *Lecteur* d’espace de travail](how-to-assign-roles.md#default-roles). 

Lorsque vous utilisez l’accès aux données basé sur l’identité, Azure Machine Learning vous invite à indiquer votre jeton Azure Active Directory en guise d’authentification pour l’accès aux données, plutôt que de conserver vos informations d’identification dans le magasin de données. De cette façon, la gestion de l’accès aux données se fait au niveau du stockage et les informations d’identification demeurent confidentielles. 

Le même comportement s’applique lorsque vous :

* [créez un jeu de données directement à partir d’URL de stockage](#use-data-in-storage) ; 
* utilisez des données de manière interactive par le biais d’un notebook Jupyter sur votre machine locale ou [instance de calcul](concept-compute-instance.md).

> [!NOTE]
> Les informations d’identification stockées à l’aide de l’authentification basée sur des informations d’identification comprennent les éléments suivants : un ID d’abonnement, des jetons de signature d’accès partagé (SAS), des clés d’accès de stockage et des informations de principal de service, comme un ID client et un ID de locataire.

### <a name="model-training-on-private-data"></a>Entraînement de modèles sur des données privées

Certains scénarios Machine Learning supposent des modèles d’entraînement avec des données privées. Dans ces cas précis, les chercheurs de données doivent exécuter des workflows d’entraînement sans exposition des données d’entrée confidentielles. Dans ce scénario, une identité managée du calcul d’entraînement est utilisée pour l’authentification de l’accès aux données. De cette façon, les administrateurs du stockage peuvent accorder l’accès **Lecteur de données blob du stockage** à l’identité managée que le calcul d’entraînement utilise pour exécuter le travail d’entraînement, à la place des scientifiques des données individuels. Découvrez comment [configurer une identité managée sur une instance de calcul](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

- Un compte de stockage Azure avec un type de stockage pris en charge. Les types de stockage suivants sont pris en charge dans la préversion. 
    - [Stockage Blob Azure](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Base de données Azure SQL](../azure-sql/database/sql-database-paas-overview.md)

- Le [kit de développement logiciel (SDK) Azure Machine Learning pour Python](/python/api/overview/azure/ml/install).

- Un espace de travail Azure Machine Learning.
  
  [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md) ou utilisez [un espace de travail existant via le kit SDK Python](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Autorisations d’accès au stockage

Pour vous garantir une connexion sécurisée à votre service de stockage sur Azure, Azure Machine Learning exige que vous disposiez de l’autorisation d’accéder au stockage de données correspondant.

L’accès aux données basé sur l’identité prend uniquement en charge les connexions aux services de stockage suivants :

* Stockage Blob Azure
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Base de données Azure SQL

Pour accéder à ces services de stockage, vous devez disposer au minimum de l’accès **Lecteur des données blob de stockage**. En savoir plus sur le [Lecteur des données blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Seuls les propriétaires de comptes de stockage peuvent [modifier votre niveau d’accès via le portail Azure](../storage/common/storage-auth-aad-rbac-portal.md).

Si vous entraînez un modèle sur une cible de calcul distante, l’identité de calcul doit être accordée avec au moins le rôle de **lecteur des données blob de stockage** du service de stockage. Découvrez comment [configurer une identité managée sur une instance de calcul](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Utiliser les réseaux virtuels

Par défaut, Azure Machine Learning ne peut pas communiquer avec un compte de stockage situé derrière un pare-feu, ou dans un réseau virtuel.

Les comptes de stockage peuvent être configurés pour n’autoriser l’accès qu’à partir de réseaux virtuels spécifiques, ce qui nécessite des configurations supplémentaires pour s’assurer que les données ne sont pas divulguées en dehors du réseau. Ce comportement est identique pour l’accès aux données basé sur les informations d’identification. Consultez [Configurations nécessaires et comment les appliquer pour les scénarios de réseau virtuel](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Créer et inscrire des magasins de données

Quand vous inscrivez un service de stockage Azure en tant que magasin de données, vous créez et inscrivez automatiquement ce magasin dans un espace de travail spécifique. Consultez les sections suivantes : [Autorisations d’accès au stockage](#storage-access-permissions) pour obtenir des conseils sur les types d’autorisation demandés et [Utiliser les réseaux virtuels](#work-with-virtual-networks) pour obtenir plus d’informations sur la façon de se connecter à un stockage de données derrière des réseaux virtuels.

Dans le code suivant, notez l’absence de paramètres d’authentification, comme `sas_token`, `account_key` `subscription_id` ou le principal de service `client_id`. Cette omission indique qu’Azure Machine Learning utilise l’accès aux données basé sur l’identité pour l’authentification. Étant donné que la création de magasins de données se produit généralement de manière interactive dans un notebook ou via le studio, votre jeton Azure Active Directory est utilisé pour l’authentification de l’accès aux données.

> [!NOTE]
> Les noms des magasins de données doivent contenir uniquement des lettres minuscules, des chiffres et des traits de soulignement. 

### <a name="azure-blob-container"></a>Conteneur d’objets blob Azure

Pour inscrire un conteneur d’objets blob Azure comme magasin de données, utilisez [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Le code suivant crée et inscrit le magasin de données `credentialless_blob` dans l’espace de travail `ws` et l’affecte à la variable `blob_datastore`. Ce magasin de données accède au conteneur d’objets blob `my_container_name` sur le compte de stockage `my-account-name`.

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage Gen1

Pour un magasin de données Azure Data Lake Storage Gen1 (ADLS Gen 1), utilisez [register_azure_data_lake_gen1()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) qui inscrit un magasin de données se connectant à un stockage Azure DataLake de génération 1.

Le code suivant crée et inscrit le magasin de données `credentialless_adls1` dans l’espace de travail `workspace` et l’affecte à la variable `adls_dstore`. Ce magasin de données accède au compte de stockage Azure Data Lake Store `adls_storage`.

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Gen 2

Pour un magasin de données Azure Data Lake Storage Gen 2 (ADLS Gen 2), utilisez [register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) qui inscrit un magasin de données se connectant à un stockage Azure DataLake de génération 2.

Le code suivant crée et inscrit le magasin de données `credentialless_adls2` dans l’espace de travail `ws` et l’affecte à la variable `adls2_dstore`. Ce magasin de données accède au système de fichiers `tabular` dans le compte de stockage `myadls2`.  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Utiliser les données dans le stockage

Les [jeux de données Azure Machine Learning](how-to-create-register-datasets.md) sont le moyen conseillé pour interagir avec vos données dans le stockage à l’aide d’Azure Machine Learning. 

Les jeux de données intègrent vos données dans un objet consommable évalué tardivement pour les tâches de Machine Learning, comme la formation. Avec les jeux de données, vous pouvez également [télécharger ou monter](how-to-train-with-datasets.md#mount-vs-download) sur une cible de calcul des fichiers de n’importe quel format, depuis des services de stockage Azure, tels que le stockage d’objets blob Azure et les lacs de données Azure.


**Pour créer des jeux de données avec un accès aux données basé sur l’identité**, vous disposez des options suivantes. Ce type de création de jeu de données utilise votre jeton Azure Active Directory pour l’authentification de l’accès aux données. 

*  Les chemins de référence à partir des magasins de données qui utilisent également l’accès aux données basé sur l’identité. 
<br>Dans l’exemple suivant, `blob_datastore` a été créé précédemment avec l’accès aux données basé sur l’identité.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Ignorez la création du magasin de données et créez des jeux de données directement à partir des URL de stockage. Actuellement, cette caractéristique prend uniquement en charge les objets blob Azure ainsi qu’Azure Data Lake Storage des générations 1 et 2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**Toutefois, lorsque vous soumettez un travail d’entraînement qui consomme un jeu de données créé avec un accès aux données basé sur l’identité**, l’identité managée du calcul d’entraînement est utilisée à la place de votre jeton Azure Active Directory pour l’authentification de l’accès aux données. Pour ce scénario, assurez-vous que l’identité managée du calcul est accordée avec au moins le rôle de **Lecteur des données blob de stockage** à partir du service de stockage. Découvrez comment [configurer une identité managée sur une instance de calcul](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Étapes suivantes

* [Créer un jeu de données Azure Machine Learning](how-to-create-register-datasets.md).
* [Effectuer l’apprentissage avec des jeux de données](how-to-train-with-datasets.md).
* [Créer un magasin de données avec un accès aux données basé sur des clés](how-to-access-data.md).
