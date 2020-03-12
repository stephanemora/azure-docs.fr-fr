---
title: Transformation avec Azure Databricks
description: Découvrez comment utiliser un modèle de solution pour transformer des données à l’aide d’un notebook Databricks dans Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: e771bc152ab50f907a8f2ad384e887c00d3f627a
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933951"
---
# <a name="transformation-with-azure-databricks"></a>Transformation avec Azure Databricks

Dans ce tutoriel, vous allez créer un pipeline de bout en bout contenant des activités **Validation**, **Copy** et **Notebook** dans Data Factory.

-   L’activité **Validation** garantit que le jeu de données source est prêt pour la consommation en aval avant le déclenchement du travail de copie et d’analytique.

-   L’activité **Copy** copie le jeu de données/fichier source dans le stockage du récepteur. Le stockage du récepteur est monté en tant que système DBFS dans le notebook Databricks pour que le jeu de données puisse être directement consommé par Spark.

-   L’activité **Databricks Notebook** déclenche le notebook Databricks qui transforme le jeu de données et l’ajoute à un entrepôt de données SQL/dossier traité.

Pour plus de simplicité, ce modèle ne crée aucun déclencheur planifié. Vous pouvez en ajouter un si nécessaire.

![1](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Prérequis

1. Créez un **compte de stockage d’objets blob** et un conteneur appelé `sinkdata` à utiliser comme **récepteur**. Notez le **nom du compte de stockage**, le **nom du conteneur** et la **clé d’accès** dans la mesure où ils sont référencés plus loin dans le modèle.

2. Vérifiez que vous disposez d’un **espace de travail Azure Databricks** ou créez-en un.

3. **Importez le notebook pour Transformation**. 
    1. Dans votre Azure Databricks, reportez-vous aux captures d’écran suivantes pour importer un notebook **Transformation** dans l’espace de travail Databricks. Il ne doit pas nécessairement se trouver au même endroit que dans l’exemple ci-dessous, mais souvenez-vous du chemin choisi pour la suite.
   
       ![2](media/solution-template-Databricks-notebook/import-notebook.png)    
    
    1. Sélectionnez « Importer depuis : **URL** », puis entrez l’URL suivante dans la zone de texte :
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/import-from-url.png)    

4. Nous allons maintenant mettre à jour le notebook **Transformation** avec vos informations de connexion au stockage. Accédez à la **commande 5** (comme indiqué dans l’extrait de code ci-dessous) dans le notebook importé ci-dessus, puis remplacez `<storage name>` et `<access key>` par vos propres informations de connexion au stockage. Vérifiez que ce compte est identique au compte de stockage créé précédemment et qu’il contient le conteneur `sinkdata`.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

5.  Générez un **jeton d’accès Databricks** pour permettre à Data Factory d’accéder à Databricks. **Enregistrez le jeton d’accès**, semblable à « dapi32db32cbb4w6eee18b7d87e45exxxxxx », car vous en aurez besoin par la suite pour créer un service lié à Databricks.

    ![4](media/solution-template-Databricks-notebook/user-setting.png)

    ![5](media/solution-template-Databricks-notebook/generate-new-token.png)

## <a name="how-to-use-this-template"></a>Comment utiliser ce modèle

1.  Accédez au modèle **Transformation avec Azure Databricks**. Créez des services liés pour les connexions suivantes. 
    
    ![Configuration des connexions](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **Connexion aux blobs sources** : pour accéder aux données sources. 
        
        Vous pouvez utiliser le stockage d’objets blob public contenant les fichiers sources pour cet exemple. Reportez-vous à la capture d’écran suivante pour la configuration. Utilisez l’**URL SAS** ci-dessous pour vous connecter au stockage source (accès en lecture seule) : 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/source-blob-connection.png)

    1.  **Connexion au blob de destination** : emplacement où copier les données. 
        
        Dans le service lié au récepteur, sélectionnez un stockage créé dans le **prérequis** 1.

        ![7](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    1.  **Azure Databricks** : pour se connecter au cluster Databricks.

        Créez un service lié à Databricks à l’aide de la clé d’accès générée dans le **prérequis** 2.c. Si vous avez un *cluster interactif*, vous pouvez le sélectionner. (Cet exemple utilise l’option *Nouveau cluster de travail*.)

        ![8](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Sélectionnez **Utiliser ce modèle**, et vous verrez un pipeline créé comme indiqué ci-dessous :
    
    ![Créer un pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>Présentation et configuration du pipeline

Dans le nouveau pipeline créé, la plupart des paramètres ont été configurés automatiquement avec les valeurs par défaut. Vérifiez les configurations et mettez-les à jour si nécessaire en fonction de vos propres paramètres. Pour plus d’informations, vous pouvez consulter les instructions et les captures d’écran ci-dessous à titre de référence.

1.  Un **indicateur de disponibilité** est créé dans l’activité Validation pour vérifier la disponibilité de la source. Le jeu *SourceAvailabilityDataset* créé à l’étape précédente est sélectionné en tant que jeu de données.

    ![12](media/solution-template-Databricks-notebook/validation-settings.png)

1.  Une conversion **file-to-blob** est créée dans l’activité Copy pour copier le jeu de données de la source vers le récepteur. Consultez les captures d’écran ci-dessous pour obtenir les configurations de la source et du récepteur dans l’activité Copy.

    ![13](media/solution-template-Databricks-notebook/copy-source-settings.png)

    ![14](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1.  Une activité du Notebook **Transformation** est créée, et le service lié créé à l’étape précédente est sélectionné.
    ![16](media/solution-template-Databricks-notebook/notebook-activity.png)

     1. Sélectionnez l’onglet **Paramètres**. Pour *Chemin d’accès du Notebook*, le modèle définit un chemin d’accès par défaut. Vous devrez peut-être parcourir et sélectionner le chemin d’accès du notebook approprié chargé dans **prérequis** 2. 

         ![17](media/solution-template-Databricks-notebook/notebook-settings.png)
    
     1. Consultez les *Paramètres de base* créés comme indiqué dans la capture d’écran. Ils doivent être transmis au notebook Databricks à partir de Data Factory. 

         ![Paramètres de base](media/solution-template-Databricks-notebook/base-parameters.png)

1.  Les **Paramètres de pipeline** sont définis comme indiqué ci-dessous.

    ![15](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Configuration des jeux de données.
    1.  **SourceAvailabilityDataset** est créé pour vérifier si les données sources sont disponibles.

        ![9](media/solution-template-Databricks-notebook/source-availability-dataset.png)

    1.  **SourceFilesDataset** :pour la copie des données sources.

        ![10](media/solution-template-Databricks-notebook/source-file-dataset.png)

    1.  **DestinationFilesDataset** : pour copier les données dans l’emplacement du récepteur/de destination.

        1.  Service lié : *sinkBlob_LS* créé à l’étape précédente.

        2.  Chemin d’accès au fichier : *sinkdata/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/destination-dataset.png)


1.  Sélectionnez **Déboguer** pour exécuter le pipeline. Vous trouverez un lien vers les journaux d’activité Databricks pour obtenir des journaux d’activité Spark plus détaillés.

    ![18](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Vous pouvez également vérifier le fichier de données à l’aide de l’explorateur de stockage. (Pour la corrélation avec les exécutions de pipeline Data Factory, cet exemple ajoute l’ID d’exécution de pipeline de la fabrique de données au dossier de sortie. De cette façon, vous pouvez retracer les fichiers générés par le biais de chaque exécution.)

    ![19](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)
