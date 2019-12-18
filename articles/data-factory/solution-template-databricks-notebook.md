---
title: Transformer des données à l’aide de Databricks
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
ms.date: 12/10/2018
ms.openlocfilehash: d21bab2f358e8ae9460bff2305957ed901c70926
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927746"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Transformer des données à l’aide de Databricks dans Azure Data Factory

Dans ce tutoriel, vous allez créer un pipeline de bout en bout contenant des activités **Lookup**, **Copy** et **Databricks notebook** dans Data Factory.

-   L’activité **Lookup** ou GetMetadata garantit que le jeu de données source est prêt pour la consommation en aval avant le déclenchement du travail de copie et d’analytique.

-   L’activité **Copy** copie le jeu de données/fichier source dans le stockage du récepteur. Le stockage du récepteur est monté en tant que système DBFS dans le notebook Databricks pour que le jeu de données puisse être directement consommé par Spark.

-   L’activité **Databricks notebook** déclenche le notebook Databricks qui transforme le jeu de données et l’ajoute à un entrepôt de données SQL/dossier traité.

Pour plus de simplicité, ce modèle ne crée aucun déclencheur planifié. Vous pouvez en ajouter un si nécessaire.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Prérequis

1.  Créez un **compte de stockage d’objets blob** et un conteneur appelé `sinkdata` à utiliser comme **récepteur**. Notez le **nom du compte de stockage**, le **nom du conteneur** et la **clé d’accès** dans la mesure où ils sont référencés plus loin dans le modèle.

2.  Vérifiez que vous disposez d’un **espace de travail Azure Databricks** ou créez-en un.

1.  **Importez le notebook pour ETL**. Importez le notebook Transform ci-dessous dans l’espace de travail Databricks. (Il ne doit pas nécessairement se trouver au même endroit que dans l’exemple ci-dessous, mais souvenez-vous du chemin choisi pour la suite.) Importez le notebook à partir de l’URL suivante. Pour cela, entrez cette URL dans le champ URL : `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Sélectionnez **Importer**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Nous allons maintenant mettre à jour le notebook **Transformation** avec les**informations de votre connexion de stockage** (nom et clé d’accès). Accédez à la **commande 5** dans le notebook importé ci-dessus, puis remplacez-la par l’extrait de code ci-dessous après avoir remplacé les valeurs en surbrillance. Vérifiez que ce compte est identique au compte de stockage créé précédemment et qu’il contient le conteneur `sinkdata`.

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

1.  Générez un **jeton d’accès Databricks** pour permettre à Data Factory d’accéder à Databricks. **Enregistrez le jeton d’accès**, semblable à « dapi32db32cbb4w6eee18b7d87e45exxxxxx », car vous en aurez besoin par la suite pour créer un service lié Databricks.

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5\.](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Créer des services liés et des jeux de données

1.  Créez des **services liés** dans l’interface utilisateur de Data Factory. Pour cela, accédez à *Connexions >Services liés > Nouveau*

    1.  **Source** : pour accéder aux données sources. Vous pouvez utiliser le stockage d’objets blob public contenant les fichiers sources pour cet exemple.

        Sélectionnez **Stockage Blob**, puis utilisez l’**URI SAS** pour vous connecter au stockage source (accès en lecture seule).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Récepteur** : emplacement où les données sont copiées.

        Sélectionnez un stockage créé dans le prérequis 1, dans le service lié du récepteur.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** : pour se connecter au cluster Databricks.

        Créer un service lié Databricks à l’aide de la clé d’accès générée dans le prérequis 2.c. Si vous avez un *cluster interactif*, vous pouvez le sélectionner. (Cet exemple utilise l’option *Nouveau cluster de travail*.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Créez des **jeux de données**.

    1.  Créez **sourceAvailability_Dataset** pour vérifier si les données sources sont disponibles.

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Jeu de données source** : pour copier les données sources (à l’aide d’une copie binaire).

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Jeu de données du récepteur** : pour copier les données dans l’emplacement du récepteur/de destination.

        1.  Service lié : sélectionnez « sinkBlob_LS » créé à l’étape 1.b.

        2.  Chemin de fichier : « sinkdata/staged_sink »

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Créer des activités

1.  Créez une activité Lookup « **Availability flag** » pour vérifier la disponibilité de la source (une activité Lookup ou GetMetadata peut être utilisée). Sélectionnez « sourceAvailability_Dataset » créé à l’étape 2.a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Créez une activité Copy « **file-to-blob** » pour copier le jeu de données de la source vers le récepteur. Dans ce cas, les données constituent un fichier binaire. Consultez les captures d’écran ci-dessous pour obtenir les configurations de la source et du récepteur dans l’activité Copy.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Définissez les **paramètres du pipeline**.

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Créez une **activité Databricks**.

    Sélectionnez le service lié créé au cours d’une étape précédente.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Configurez les **paramètres**. Créez des **paramètres de base** comme indiqué dans la capture d’écran, puis créez des paramètres à passer au notebook Databricks à partir de Data Factory. Recherchez et **sélectionnez** le **chemin de notebook approprié** chargé dans les **prérequis 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Exécutez le pipeline**. Vous trouverez un lien vers les journaux d’activité Databricks pour obtenir des journaux d’activité Spark plus détaillés.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Vous pouvez également vérifier le fichier de données à l’aide de l’explorateur de stockage. (Pour la corrélation avec les exécutions de pipeline Data Factory, cet exemple ajoute l’ID d’exécution de pipeline de la fabrique de données au dossier de sortie. De cette façon, vous pouvez retracer les fichiers générés par le biais de chaque exécution.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)
