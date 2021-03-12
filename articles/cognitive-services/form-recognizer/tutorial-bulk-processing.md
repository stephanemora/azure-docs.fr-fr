---
title: 'Tutoriel : Extraire des données de formulaire en bloc avec Azure Data Factory - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Configurez des activités Azure Data Factory pour déclencher l’entraînement et l’exécution de modèles Form Recognizer et numériser un backlog important de documents.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 5b220652009f54482c757f01232517569596c562
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423894"
---
# <a name="tutorial-extract-form-data-in-bulk-by-using-azure-data-factory"></a>Tutoriel : Extraire des données de formulaire en bloc avec Azure Data Factory

Dans ce tutoriel, nous allons voir comment utiliser les services Azure pour ingérer un grand lot de formulaires dans du contenu multimédia numérique. Le tutoriel vous montre comment automatiser l’ingestion des données à partir d’un lac de données Azure de documents dans une base de données Azure SQL. Vous pourrez rapidement entraîner des modèles et traiter de nouveaux documents en quelques clics.

## <a name="business-need"></a>Besoin métier

Aujourd’hui, la majorité des organisations savent à quel point les données qu’elles ont sont précieuses, quels qu’en soient les formats (PDF, images, vidéos). Elles cherchent à adopter les bonnes pratiques et les moyens les plus rentables pour numériser ces ressources.

De plus, nos clients ont souvent des types de formulaires différents provenant de leurs nombreux clients à eux. À la différence des [guides de démarrage rapide](./quickstarts/client-library.md), ce tutoriel vous montre comment entraîner automatiquement un modèle avec des types de formulaires nouveaux et différents, en utilisant une approche pilotée par les métadonnées. Si vous n’avez pas de modèle existant pour le type de formulaire donné, le système en crée un et vous donne son ID. 

En extrayant les données des formulaires et en les associant à des systèmes et entrepôts de données existants déjà opérationnels, les entreprises peuvent obtenir des insights et ajouter de la valeur à leurs clients et utilisateurs.

Azure Form Recognizer aide les organisations à utiliser leurs données, à automatiser leurs processus (paiement des factures, traitement des taxes, etc.), à réduire les coûts et gagner du temps, ainsi qu’à améliorer la justesse des données.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer Azure Data Lake pour stocker vos formulaires
> * Utiliser une base de données Azure pour créer une table de paramétrage
> * Utiliser Azure Key Vault pour stocker des informations d’identification sensibles
> * Entraîner votre modèle Form Recognizer dans un notebook Azure Databricks
> * Extraire vos données de formulaire avec un notebook Databricks
> * Automatiser l’entraînement et l’extraction de formulaires avec Azure Data Factory

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. [Créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services/).
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Créer une ressource Form Recognizer"  target="_blank">créez une ressource Form Recognizer</a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois la ressource déployée, sélectionnez **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans votre code plus loin dans ce guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire gratuit (F0) pour tester le service. Vous pouvez ensuite passer à un niveau payant pour la production.
* Au minimum un ensemble de cinq formulaires du même type. Idéalement, ce workflow a vocation à prendre en charge des grands ensembles de documents. Consultez [Créer un jeu de données d’entraînement](./build-training-data-set.md) afin d’obtenir des conseils et des options pour constituer votre jeu de données d’entraînement. Dans le cadre de ce tutoriel, vous pouvez utiliser les fichiers disponibles dans le dossier Train de l’[exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2128080).


## <a name="project-architecture"></a>Architecture du projet 

Ce projet représente un ensemble de pipelines Azure Data Factory pour déclencher des notebooks Python qui entraînent, analysent et extraient des données de documents dans un compte Azure Data Lake Storage.

L’API REST Form Recognizer exige des paramètres sous forme d’entrée. Pour des raisons de sécurité, certains de ces paramètres sont stockés dans un coffre de clés Azure. D’autres moins sensibles, comme le nom du dossier des blobs de stockage, sont stockés dans une table de paramétrage dans une base de données Azure SQL.

Pour chaque type de formulaire à analyser, les ingénieurs Données ou les scientifiques Données remplissent une ligne de la table de paramètres. Ils utilisent ensuite Azure Data Factory pour itérer au sein de la liste des types de formulaires détectés et passer les paramètres appropriés à un notebook Databricks pour entraîner ou réentraîner les modèles Form Recognizer. Une fonction Azure peut également être utilisée ici.

Le notebook Azure Databricks utilise ensuite les modèles entraînés pour extraire les données de formulaire. Il exporte ces données vers une base de données Azure SQL.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="Diagramme illustrant l’architecture du projet.":::


## <a name="set-up-azure-data-lake"></a>Configurer le lac de données Azure

Votre backlog de formulaires peut se trouver dans votre environnement local ou sur un serveur (s)FTP. Ce tutoriel utilise des formulaires situés dans un compte Azure Data Lake Storage Gen2. Vous pouvez y transférer vos fichiers avec Azure Data Factory, l’Explorateur Stockage Azure ou AzCopy. Les jeux de données d’entraînement et de scoring peuvent se trouver dans des conteneurs différents, mais les jeux de données d’entraînement de tous les types de formulaires doivent se trouver dans le même conteneur (ils peuvent se trouver dans des dossiers différents).

Pour créer un lac de données, suivez les instructions données dans [Créer un compte de stockage à utiliser avec Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md).

## <a name="create-a-parameterization-table"></a>Créer une table de paramétrage

Ensuite, nous allons créer une table de métadonnées dans une base de données Azure SQL. Cette table contient les données non sensibles nécessaires à l’API REST Form Recognizer. S’il y a un nouveau type de formulaire dans notre jeu de données, nous insérons un nouvel enregistrement dans cette table pour déclencher le pipeline d’entraînement et de scoring. (Nous implémenterons ces pipelines plus tard.)

Ces champs sont utilisés dans la table :

* **form_description**. Ce champ n’est pas obligatoire dans le cadre de l’entraînement. Il fournit une description du type de formulaire pour lequel nous entraînons le modèle (par exemple, « formulaires du client A », « formulaires de l’hôtel B »).
* **training_container_name**. Nom du conteneur de comptes de stockage dans lequel nous avons stocké le jeu de données d’entraînement. Il peut s’agir du même conteneur que **scoring_container_name**.
* **training_blob_root_folder**. Il s’agit du dossier au sein du compte de stockage dans lequel nous allons stocker les fichiers pour l’entraînement du modèle.
* **scoring_container_name**. Nom du conteneur de comptes de stockage dans lequel nous avons stocké les fichiers desquels nous voulons extraire les paires clé/valeur. Il peut s’agir du même conteneur que **training_container_name**.
* **scoring_input_blob_folder**. Il s’agit du dossier du compte de stockage dans lequel nous allons stocker les fichiers à partir desquels extraire des données.
* **model_id**. ID du modèle que nous voulons réentraîner. Pour la première exécution, la valeur doit être égale à -1, ce qui va amener le notebook d’entraînement à créer un modèle personnalisé à entraîner. Le notebook d’entraînement va retourner l’ID du modèle nouvellement créé à l’instance Azure Data Factory. À l’aide d’une activité de procédure stockée, nous allons mettre à jour cette valeur dans la base de données Azure SQL.

  Chaque fois que vous voulez ingérer un nouveau type de formulaire, vous devez réinitialiser manuellement l’ID du modèle à -1 avant d’entraîner le modèle.

* **file_type**. Les types de formulaires pris en charge sont `application/pdf`, `image/jpeg`, `image/png` et `image/tif`.

  Si vos formulaires sont d’autres types de fichiers, vous devez changer cette valeur et **model_id** quand vous entraînez un nouveau type de formulaire.
* **form_batch_group_id**. Au fil du temps, vous pouvez avoir plusieurs types de formulaires que vous entraînez par rapport au même modèle. Le champ **form_batch_group_id** vous permet de spécifier tous les types de formulaires qui ont été entraînés avec un modèle spécifique.

### <a name="create-the-table"></a>Créer la table


[Créez une base de données Azure SQL](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase), puis exécutez ce script SQL dans l’[éditeur de requête](../../azure-sql/database/connect-query-portal.md) pour créer la table nécessaire :


```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

Exécutez le script suivant pour créer la procédure de mise à jour automatique de **model_id** à l’issue de l’entraînement.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Utiliser Azure Key Vault pour stocker des informations d’identification sensibles

Pour des raisons de sécurité, nous ne souhaitons pas stocker certaines informations sensibles dans la table de paramétrage de la base de données Azure SQL. Nous allons stocker les paramètres sensibles sous forme de secrets Azure Key Vault.

### <a name="create-an-azure-key-vault"></a>Créer un coffre de clés Azure

[Créez une ressource Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Ensuite, accédez à la ressource Key Vault créée, puis, dans la section des **paramètres**, sélectionnez **secrets** pour ajouter les paramètres.

Une nouvelle fenêtre s'affiche. Sélectionnez **Générer/importer**. Entrez le nom du paramètre et sa valeur, puis sélectionnez **Créer**. Effectuez ces étapes pour les paramètres suivants :

* **CognitiveServiceEndpoint**. URL du point de terminaison de votre API Form Recognizer.
* **CognitiveServiceSubscriptionKey**. Clé d’accès pour votre service Form Recognizer. 
* **StorageAccountName**. Compte de stockage où sont stockés le jeu de données d’entraînement et les formulaires desquels vous voulez extraire des paires clé/valeur. Si ces éléments se trouvent dans des comptes différents, entrez chaque nom de compte sous la forme d’un secret distinct. N’oubliez pas que les jeux de données d’entraînement doivent se trouver dans le même conteneur pour tous les types de formulaires. Ils peuvent se trouver dans des dossiers différents.
* **StorageAccountSasKey**. Signature d’accès partagé (SAS) du compte de stockage. Pour récupérer l’URL SAS, accédez à votre ressource de stockage. Dans l’**Explorateur Stockage**, accédez à votre conteneur, cliquez avec le bouton droit, puis sélectionnez **Obtenir une signature d’accès partagé**. Il est important d’obtenir la signature d’accès partagé de votre conteneur, et non celle du compte de stockage. Vérifiez que les autorisations **Lecture** et **Écriture** sont sélectionnées, puis sélectionnez **Créer**. Copiez alors la valeur dans la section **URL**. Elle doit avoir ce format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Entraîner votre modèle Form Recognizer dans un notebook Databricks

Vous allez utiliser Azure Databricks pour stocker et exécuter le code Python qui interagit avec le service Form Recognizer.

### <a name="create-a-notebook-in-databricks"></a>Créer un notebook dans Databricks

[Créez une ressource Azure Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) dans le portail Azure. Accédez à la ressource après sa création, puis ouvrez l’espace de travail.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Créer une étendue de secrets sauvegardée par Azure Key Vault


Pour référencer les secrets dans le coffre de clés Azure que nous avons créé ci-dessus, vous devez créer une étendue de secrets dans Databricks. Effectuez les étapes indiquées ici : [Créer une étendue de secrets sauvegardée par Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Créer un cluster Databricks

Un cluster est une collection de ressources de calcul Databricks. Pour créer un cluster :

1. Dans le volet gauche, sélectionnez le bouton **Clusters**.
1. Dans la page **Clusters**, sélectionnez **Créer un cluster**.
1. Dans la page **Créer un cluster**, spécifiez un nom de cluster, puis sélectionnez **7.2 (Scala 2.12, Spark 3.0.0)** dans la liste **Version de Databricks Runtime**.
1. Sélectionnez **Créer un cluster**.

### <a name="write-a-settings-notebook"></a>Écrire un notebook de paramètres

Vous êtes maintenant prêt à ajouter des notebooks Python. Tout d’abord, créez un notebook appelé **Settings**. Ce notebook va affecter les valeurs indiquées dans votre table de paramétrage aux variables incluses dans le script. Azure Data Factory passera par la suite les valeurs en tant que paramètres. Nous allons également affecter aux variables des valeurs issues des secrets inclus dans le coffre de clés. 

1. Pour créer le notebook **Settings**, sélectionnez le bouton **Espace de travail**. Sous le nouvel onglet, sélectionnez la liste déroulante, puis **Créer** et **Notebook**.
1. Dans la fenêtre contextuelle, entrez le nom que vous voulez donner au notebook, puis sélectionnez **Python** comme langage par défaut. Sélectionnez votre cluster Databricks, puis **Créer**.
1. Dans la première cellule du notebook, nous récupérons les paramètres passés par Azure Data Factory :

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. Dans la deuxième cellule, nous récupérons les secrets dans le coffre de clés pour les attribuer aux variables :

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Écrire un notebook d’entraînement

Maintenant que nous avons terminé de créer le notebook **Settings**, nous pouvons créer un notebook pour entraîner le modèle. Comme indiqué ci-dessus, nous allons utiliser des fichiers stockés dans un dossier dans un compte Azure Data Lake Storage Gen2 (**training_blob_root_folder**). Le nom du dossier a été passé en tant que variable. Chaque ensemble de types de formulaires se trouve dans le même dossier. À mesure que nous effectuons une boucle sur la table de paramètres, nous allons entraîner le modèle en utilisant tous les types de formulaires. 

1. Créez un notebook appelé **TrainFormRecognizer**. 
1. Dans la première cellule, exécutez le notebook **Settings** :

    ```python
    %run "./Settings"
    ```

1. Dans la cellule suivante, affectez des variables provenant du fichier Settings et entraînez dynamiquement le modèle pour chaque type de formulaire, en appliquant le code indiqué dans le guide de [démarrage rapide REST](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20).

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # If you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key/value pairs.
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# If you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. La dernière étape du processus d’entraînement consiste à obtenir le résultat de cet entraînement dans un format JSON :

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-by-using-a-notebook"></a>Extraire des données de formulaire avec un notebook

### <a name="mount-the-azure-data-lake-storage"></a>Monter le stockage Azure Data Lake

L’étape suivante consiste à scorer les différents formulaires que nous avons avec le modèle entraîné. Nous allons monter le compte Azure Data Lake Storage dans Databricks et nous référer au montage pendant le processus d’ingestion.

Comme nous l’avons fait dans la phase d’entraînement, nous allons utiliser Azure Data Factory pour appeler l’extraction des paires clé/valeur des formulaires. Nous allons effectuer une boucle sur les formulaires dans les dossiers spécifiés dans la table de paramètres.

1. Créez le notebook pour monter le compte de stockage dans Databricks. Nommez-le **MountDataLake**. 
1. Vous devez d’abord appeler le notebook **Settings** :

    ```python
    %run "./Settings"
    ```

1. Dans la deuxième cellule, définissez des variables pour les paramètres sensibles que nous allons récupérer à partir de nos secrets Key Vault :

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Essayez de démonter le compte de stockage au cas où il aurait été monté auparavant :

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Montez le compte de stockage :


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > Nous avons uniquement monté le compte de stockage d’entraînement. Dans ce cas, les fichiers d’entraînement et les fichiers desquels nous voulons extraire des paires clé/valeur se trouvent dans le même compte de stockage. Si vos comptes de stockage de scoring et d’entraînement sont différents, vous devez monter les deux comptes de stockage. 

### <a name="write-the-scoring-notebook"></a>Écrire le notebook de scoring

Nous pouvons maintenant créer un notebook de scoring. À l’instar de ce que nous avons fait pour le notebook d’entraînement, nous allons utiliser les fichiers stockés dans les dossiers du compte Azure Data Lake Storage que nous venons de monter. Le nom du dossier est passé en tant que variable. Nous allons effectuer une boucle sur tous les formulaires dans le dossier spécifié pour en extraire les paires clé/valeur. 

1. Créez un notebook et nommez-le **ScoreFormRecognizer**. 
1. Exécutez les notebooks **Settings** et **MountDataLake** :

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Ajoutez ce code, qui appelle l’API [Analyser](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) :

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Dans la cellule suivante, nous obtenons les résultats de l’extraction des paires clé/valeur. Cette cellule génère le résultat. Nous voulons que le résultat soit au format JSON pour pouvoir le traiter ultérieurement dans Azure SQL Database ou Azure Cosmos DB. Nous allons donc écrire le résultat dans un fichier .json. Le nom du fichier de sortie va être celui du fichier scoré, concaténé avec « _output.json ». Le fichier est stocké dans le même dossier que le fichier source.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. Procédez à l’écriture du fichier dans une nouvelle cellule :

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-by-using-azure-data-factory"></a>Automatiser l’entraînement et le scoring avec Azure Data Factory

La seule étape restante consiste à configurer le service Azure Data Factory pour automatiser les processus d’entraînement et de scoring. Tout d’abord, suivez les étapes sous [Créer une fabrique de données](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory). Une fois que vous avez créé la ressource Azure Data Factory, vous devez créer trois pipelines : un pour l’entraînement et deux pour le scoring. (Nous en donnerons l’explication plus tard.)

### <a name="training-pipeline"></a>Pipeline d’entraînement

La première activité dans le pipeline d’entraînement est une recherche pour lire et retourner les valeurs indiquées dans la table de paramétrage dans la base de données Azure SQL. Tous les jeux de données d’entraînement se trouvent dans le même compte de stockage et le même conteneur (mais éventuellement dans des dossiers différents). Nous allons donc garder la valeur d’attribut par défaut **Première ligne uniquement** dans les paramètres de l’activité de recherche. Pour chaque type de formulaire, nous allons entraîner le modèle en utilisant tous les fichiers inclus dans le dossier **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="Capture d’écran montrant un pipeline d’entraînement dans Data Factory.":::

La procédure stockée accepte deux paramètres : **model_id** et **form_batch_group_id**. Le code permettant de retourner l’ID du modèle à partir du notebook Databricks est `dbutils.notebook.exit(model_id)`. Le code permettant de lire le code dans l’activité de la procédure stockée dans Data Factory est `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id`.

### <a name="scoring-pipelines"></a>Pipelines de scoring

Pour extraire les paires clé/valeur, nous allons analyser tous les dossiers de la table de paramétrage. Pour chaque dossier, nous allons extraire les paires clé/valeur de tous les fichiers qu’il contient. À l’heure actuelle, Azure Data Factory ne prend pas en charge les boucles ForEach imbriquées. Nous allons plutôt créer deux pipelines. Le premier pipeline effectue la recherche à partir de la table de paramétrage, puis passe la liste des dossiers en tant que paramètre au second pipeline.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="Capture d’écran montrant le premier pipeline de scoring dans Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="Capture d’écran montrant les détails du premier pipeline de scoring dans Data Factory.":::

Le deuxième pipeline va utiliser une activité GetMeta pour obtenir la liste des fichiers dans le dossier et la passer en tant que paramètre au notebook Databricks de scoring.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="Capture d’écran montrant le deuxième pipeline de scoring dans Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="Capture d’écran montrant les détails du deuxième pipeline de scoring dans Data Factory.":::

### <a name="specify-a-degree-of-parallelism"></a>Spécifier un degré de parallélisme

Dans les pipelines d’entraînement et de scoring, vous pouvez spécifier le degré de parallélisme pour traiter plusieurs formulaires simultanément.

Pour définir le degré de parallélisme dans le pipeline Azure Data Factory :

1. Sélectionnez l’activité **ForEach**.
1. Décochez la case **Séquentiel**.
1. Définissez le degré de parallélisme dans la zone **Nombre de lots**. Nous recommandons de définir un nombre maximal de 15 lots pour le scoring.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="Capture d’écran montrant la configuration du parallélisme pour l’activité de scoring dans Azure Data Factory.":::

## <a name="how-to-use-the-pipeline"></a>Comment utiliser le pipeline

Vous avez maintenant un pipeline automatisé pour numériser votre backlog de formulaires en vue d’exécuter des analyses. Quand vous ajoutez des nouveaux formulaires de type connu à un dossier de stockage existant, réexécutez simplement les pipelines de scoring. Ils mettent à jour tous vos fichiers de sortie, notamment ceux des nouveaux formulaires. 

Si vous ajoutez des nouveaux formulaires dont le type est nouveau, vous devez aussi charger un jeu de données d’entraînement dans le conteneur approprié. Ensuite, ajoutez une nouvelle ligne à la table de paramétrage, en entrant les emplacements des nouveaux documents et de leur jeu de données d’entraînement. Entrez la valeur -1 pour **model_ID** afin d’indiquer qu’un nouveau modèle doit être entraîné pour les formulaires. Exécutez ensuite le pipeline d’entraînement dans Azure Data Factory. Le pipeline va lire la table, entraîner un modèle et remplacer l’ID du modèle dans la table. Vous pouvez ensuite appeler les pipelines de scoring pour commencer à écrire les fichiers de sortie.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré des pipelines Azure Data Factory pour déclencher l’entraînement et l’exécution de modèles Form Recognizer et numériser un backlog important de fichiers. À présent, explorez l’API Form Recognizer pour voir ce qu’elle vous permet aussi de faire.

* [API REST Form Recognizer](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
