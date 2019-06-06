---
title: Utilisez Azure Data Box pour migrer des données à partir de local HDFS stocker dans le stockage Azure
description: Migrer des données à partir d’un magasin HDFS en local vers le stockage Azure
services: storage
author: normesta
ms.service: storage
ms.date: 06/05/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 9a42135df38cde91cc6626a3f7d0328334af0a5d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729063"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Utiliser Azure Data Box pour migrer des données à partir d’un magasin HDFS en local vers le stockage Azure

Vous pouvez migrer des données à partir d’un magasin HDFS en local de votre cluster Hadoop dans Azure Storage (stockage d’objets blob ou Data Lake Storage Gen2) à l’aide d’un appareil Data Box. Vous pouvez choisir à partir d’une zone de données de 80 to ou une forte de zone de données de 770-to.

Cet article vous aide à effectuer ces tâches :

:heavy_check_mark: Copier vos données dans une zone de données ou un appareil lourd de zone de données.

:heavy_check_mark: Expédier l’appareil à Microsoft.

:heavy_check_mark: Déplacer les données sur votre compte de stockage Data Lake Storage Gen2.

## <a name="prerequisites"></a>Conditions préalables

Vous avez besoin de ces éléments pour effectuer la migration.

* Compte de stockage Azure qui **ne** possède des espaces de noms hiérarchique activée sur ce dernier.

* Si vous souhaitez utiliser le stockage Azure Data Lake Gen2 compte (un compte de stockage qui **est** possède des espaces de noms hiérarchique activée), vous pouvez ensuite créer à ce stade.

* Un cluster Hadoop en local qui contient vos données source.

* Un [appareil Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

    - [Commandez votre Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) ou [Data Box lourds](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Lors de la commande de votre appareil, n’oubliez pas de choisir un compte de stockage qui **ne** possède des espaces de noms hiérarchique activée sur ce dernier. Il s’agit, car les appareils de la zone de données ne gèrent pas encore ingestion directe dans Azure Data Lake Storage Gen2. Vous devrez copier dans un compte de stockage, puis effectuez une deuxième copie dans le compte ADLS Gen2. Les instructions de cette sont indiquées dans les étapes ci-dessous.
    - Et connecter votre [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) ou [importante de données boîte](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) à un réseau local.

Si vous êtes prêt, commençons.

## <a name="copy-your-data-to-a-data-box-device"></a>Copier vos données sur un appareil Data Box

Pour copier les données à partir de votre magasin HDFS en local sur un appareil Data Box, vous allez configurer quelques éléments, puis utiliser le [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) outil.

Si la quantité de données que vous copiez est supérieure à la capacité d’une zone de données unique ou celui du nœud unique sur lourd de zone de données, divisez votre jeu de données dans des tailles qui entrent dans vos appareils.

Suivez ces étapes pour copier des données via le stockage REST API de/objet Blob sur votre appareil Data Box. L’interface de l’API REST sera le rendre apparaissent sous la forme d’un magasin HDFS à votre cluster. 


1. Avant de copier les données via l’API REST, identifiez les primitives de sécurité et de connexion pour se connecter à l’interface REST sur la zone données ou lourd de zone de données. Connectez-vous à l’interface utilisateur de Data Box de web locale et accédez à **Connect et copie** page. Dans le stockage Azure du compte de votre appareil, sous **accéder aux paramètres**, recherchez et sélectionnez **REST**.

    ![Page « Se connecter et copier »](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Dans le compte de stockage d’accès et le chargement de données boîte de dialogue, copiez la **point de terminaison de service Blob** et **clé de compte de stockage**. Du point de terminaison de service blob, omettez le `https://` et la barre oblique de fin.

    Dans ce cas, le point de terminaison est : `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. La partie hôte de l’URI que vous allez utiliser est : `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Pour obtenir un exemple, consultez Comment [se connecter à REST sur http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Boîte de dialogue « Accéder aux compte de stockage et charger des données »](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Ajouter le point de terminaison et l’adresse IP du nœud Data Box ou élevée de zone de données à `/etc/hosts` sur chaque nœud.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Si vous utilisez un autre mécanisme pour DNS, vous devez vous assurer que la zone de données de point de terminaison peut être résolu.
    
4. Définir une variable d’environnement `azjars` pour pointer vers le `hadoop-azure` et `microsoft-windowsazure-storage-sdk` fichiers jar. Ces fichiers sont sous le répertoire d’installation de Hadoop (vous pouvez vérifier si ces fichiers existent à l’aide de cette commande `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` où `<hadoop_install_dir>` est le répertoire où vous avez installé Hadoop) utiliser les chemins d’accès complets. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

5. Créer le conteneur de stockage que vous souhaitez utiliser pour la copie des données. Vous devez également spécifier un dossier de destination dans le cadre de cette commande. Cela peut être un dossier de destination factice à ce stade.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -mkdir -p  wasb://[container_name]@[blob_service_endpoint]/[destination_folder]
    ```

6. Exécuter une commande de la liste pour vous assurer que votre conteneur et le dossier ont été créés.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -ls -R  wasb://[container_name]@[blob_service_endpoint]/
    ```

7. Copier des données à partir du HDFS Hadoop pour le stockage d’objets Blob de zone de données, dans le conteneur que vous avez créé précédemment. Si le dossier que vous copiez dans est introuvable, la commande le crée automatiquement.

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   Le `-libjars` option est utilisée pour rendre le `hadoop-azure*.jar` et dépendantes `azure-storage*.jar` fichiers disponibles pour `distcp`. Cela peut se produire déjà pour certains clusters.
   
   L’exemple suivant montre comment la `distcp` commande est utilisée pour copier des données.
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
Pour améliorer la vitesse de copie :
- Essayez de modifier le nombre de mappeurs. (L’exemple ci-dessus utilise `m` = 4 mappeurs.)
- Essayez d’exécuter plusieurs `distcp` en parallèle.
- N’oubliez pas que des fichiers volumineux plus performantes que les petits fichiers.
    
## <a name="ship-the-data-box-to-microsoft"></a>Expédition de la zone de données à Microsoft

Suivez ces étapes pour préparer et expédier l’appareil Data Box à Microsoft.

1. Une fois la copie des données est terminée, exécutez :
    
    - [Préparer l’expédition sur votre zone de données ou importante de données boîte](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).
    - Une fois la préparation de l’appareil terminée, téléchargez les fichiers de nomenclature. Vous utilisez ces BOM ou manifeste des fichiers plus tard pour vérifier les données chargées sur Azure. 
    - Arrêtez l’appareil et débranchez les câbles.
2.  Planifiez un enlèvement avec UPS. Suivez les instructions pour :

    - [Expédier votre Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) 
    - [Expédier votre lourds de zone de données](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).
3.  Une fois que Microsoft reçoit votre appareil, il est connecté au réseau de centre de données et les données sont téléchargées vers le compte de stockage que vous avez spécifié (avec les espaces de noms hiérarchique désactivé) lorsque vous avez placé l’ordre de l’appareil. Vérifier les fichiers de nomenclature que toutes vos données est téléchargé vers Azure. Vous pouvez maintenant déplacer ces données vers un compte de stockage Data Lake Storage Gen2.


## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Déplacer les données sur votre compte de stockage Data Lake Storage Gen2

Cette étape est nécessaire si vous utilisez le stockage Azure Data Lake Gen2 comme magasin de données. Si vous utilisez simplement un compte de stockage d’objets blob sans espace de noms hiérarchique comme magasin de données, il est inutile d’effectuer cette étape.

Vous pouvez pour cela de deux manières.

- Utilisez [Azure Data Factory pour déplacer des données vers ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Vous devrez spécifier **stockage Blob Azure** comme source.

- Utiliser votre cluster Hadoop basé sur Azure. Vous pouvez exécuter cette commande DistCp :

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

Cette commande copie les données et les métadonnées à partir de votre compte de stockage dans votre compte de stockage Data Lake Storage Gen2.

## <a name="next-steps"></a>Étapes suivantes

Découvrez le fonctionnement de Data Lake Storage Gen2 avec des clusters HDInsight. Consultez [Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
