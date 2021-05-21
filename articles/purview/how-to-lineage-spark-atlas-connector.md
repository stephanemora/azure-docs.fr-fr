---
title: Métadonnées et traçabilité à partir du connecteur Apache Atlas Spark
description: Cet article décrit l’extraction de traçabilité des données à partir de Spark à l’aide du connecteur Atlas Spark.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/28/2021
ms.openlocfilehash: 1ba281dfd0accbac486abb00785fbd6c552fff8d
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109520372"
---
# <a name="how-to-use-apache-atlas-connector-to-collect-spark-lineage"></a>Comment utiliser le connecteur Apache Atlas pour collecter la traçabilité Spark

Le connecteur Apache Atlas Spark est un hook pour suivre les mouvements de données Spark SQL/DataFrame et les modifications des métadonnées push sur le point de terminaison Purview Atlas. 

## <a name="supported-scenarios"></a>Scénarios pris en charge

Ce connecteur prend en charge le suivi suivant :
1.  DDL SQL comme « CREATE/ALTER DATABASE », « CREATE/ALTER TABLE ».
2.  DML SQL comme « CREATE TABLE HelloWorld AS SELECT », « INSERT INTO... », « LOAD DATA [LOCAL] INPATH », « INSERT OVERWRITE [LOCAL] DIRECTORY », etc.
3.  Déplacements DataFrame qui ont des entrées et des sorties.

Ce connecteur s’appuie sur l’écouteur de requête pour récupérer la requête et examiner les impacts. Il se met en corrélation avec d’autres systèmes tels que Hive et HDFS pour suivre le cycle de vie des données dans Atlas.
Étant donné que Purview prend en charge l’API Atlas et le hook natif Atlas, le connecteur peut signaler la traçabilité à Purview après avoir été configuré avec Spark. Le connecteur peut être configuré par travail ou comme paramètre par défaut du cluster. 

## <a name="configuration-requirement"></a>Configuration requise

Les connecteurs requièrent une Spark version 2.4.0 ou plus. Mais Spark version 3 n’est pas pris en charge. Spark prend en charge trois types d’écouteurs qui doivent être définis :  

| Écouteur |    Depuis Spark version|
| ------------------- | ------------------- | 
| spark.extraListeners | 1.3.0 |
| spark.sql.queryExecutionListeners | 2.3.0 |
| spark.sql.streaming.streamingQueryListeners | 2.4.0 |

Si la version du cluster Spark est inférieure à 2.4.0, la traçabilité des requêtes de flux et la plupart des lignes de requête ne sont pas capturées.

### <a name="step-1-prepare-spark-atlas-connector-package"></a>Étape 1. Préparer le package du connecteur Spark Atlas
Les étapes suivantes sont documentées en prenant DataBricks comme exemple :

1.  Générer le package
    1. Extraire du code à partir de GitHub : https://github.com/hortonworks-spark/spark-atlas-connector
    2. [Pour Windows] Commentez **maven-enforcer-plugin** dans spark-atlas-connector\pom.xml pour supprimer la dépendance sur Unix.

    ```web
    <requireOS>
        <family>unix</family>
    </requireOS>
    ```
    
    c. Exécutez la commande **mvn package -DskipTests** dans la racine du projet pour générer. 
    
    d. Récupérez le fichier jar à partir de *~\spark-atlas-connector-assembly\target\spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar*
    
    e. Placez le package à un endroit auquel le cluster Spark peut accéder. Pour le cluster DataBricks, le package peut être téléchargé vers un dossier dbfs, tel que /FileStore/jars.

2. Préparer la configuration du connecteur
    1. Obtenir le point de terminaison Kafka et les informations d’identification dans le portail Azure du compte Purview
        1. Fournir votre compte avec l’autorisation *Curateur de données Purview* 

        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/assign-purview-data-curator-role.png" alt-text="Capture d’écran montrant l’attribution du rôle Curateur de données" lightbox="./media/how-to-lineage-spark-atlas-connector/assign-purview-data-curator-role.png":::

        1. Point de terminaison : obtenez-le à partir de la *chaîne de connexion principale de point de terminaison Atlas Kafka*. Partie du point de terminaison
        1. Informations d’identification : La *chaîne de connexion principale de point de terminaison Atlas Kafka*
        
        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/atlas-kafka-endpoint.png" alt-text="Capture d’écran montrant le point de terminaison Atlas Kafka" lightbox="./media/how-to-lineage-spark-atlas-connector/atlas-kafka-endpoint.png":::        
        
    1. Préparez le fichier *atlas-application.properties*, remplacez la valeur *atlas.kafka.bootstrap.servers* et la valeur de mot de passe dans *atlas.kafka.sasl.jaas.config*

    ```script
    atlas.client.type=kafka
    atlas.kafka.sasl.mechanism=PLAIN
    atlas.kafka.security.protocol=SASL_SSL
    atlas.kafka.bootstrap.servers= atlas-46c097e6-899a-44aa-9a30-6ccd0b2a2a91.servicebus.windows.net:9093
    atlas.kafka.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="<connection string got from your Purview account>";
    ```
    
    c.  Assurez-vous que le fichier de configuration Atlas figure dans le classpath du pilote généré à l’[Étape 1 : Générer le package ci-dessus](../purview/how-to-lineage-spark-atlas-connector.md#step-1-prepare-spark-atlas-connector-package). En mode cluster, expédiez ce fichier de configuration au lecteur distant *--files atlas-application.properties*


### <a name="step-2-prepare-your-purview-account"></a>Étape 2. Préparer votre compte Purview
Une fois la définition du modèle Atlas Spark correctement créée, suivez les étapes ci-dessous
1. Obtenir la définition de type Spark à partir de GitHub https://github.com/apache/atlas/blob/release-2.1.0-rc3/addons/models/1000-Hadoop/1100-spark_model.json

2. Attribuer un rôle :
    1. Ouvrez le centre d’administration Purview et choisissez Attribuer des rôles
    1. Ajoutez des utilisateurs et accordez le rôle *Administrateur de la source de données Purview* à votre principal de service
3. Obtenir le jeton d’authentification :
    1. Ouvrez « postman » ou des outils similaires 
    1. Utilisez le principal de service de l’étape précédente pour obtenir le jeton du porteur :
        * Point de terminaison : https://login.windows.net/microsoft.com/oauth2/token
        * grant_type : client_credentials
        * client_id : {ID de principal du service}
        * client_secret : {clé du principal du service}
        * resource : https://projectbabylon.azure.net

        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/postman-examples.png" alt-text="Capture d’écran montrant un exemple postman" lightbox="./media/how-to-lineage-spark-atlas-connector/postman-examples.png":::      

4. Publiez la définition du modèle Spark Atlas sur le compte Purview :
    1.  Obtenez le point de terminaison Atlas du compte Purview à partir de la section des propriétés du portail Azure.
    1. Publiez la définition du type Spark dans le compte Purview :
       * Publication : {{endpoint}}/api/atlas/v2/types/typedefs
       * Utiliser le jeton d’accès généré 
       * Corps : choisissez raw et copiez tout le contenu de GitHub https://github.com/apache/atlas/blob/release-2.1.0-rc3/addons/models/1000-Hadoop/1100-spark_model.json

:::image type="content" source="./media/how-to-lineage-spark-atlas-connector/postman-example-type-definition.png" alt-text="Capture d’écran montrant un exemple postman pour la définition de type" lightbox="./media/how-to-lineage-spark-atlas-connector/postman-example-type-definition.png":::

### <a name="step-3-prepare-spark-job"></a>Étape 3. Préparer un travail Spark
1. Écrire votre travail Spark normalement
2. Ajoutez des paramètres de connecteur dans le code source de votre travail Spark. Définissez la valeur de la propriété système *atlas.conf* dans le code comme ci-dessous pour vous assurer que le fichier *atlas-application.properties* peut être trouvé.

    **System.setProperty("atlas.conf", "/dbfs/FileStore/jars/")**

3. Générez le code source de votre travail Spark pour générer un fichier jar. 
4. Placez le fichier jar de l’application Spark à un emplacement accessible à votre cluster. Par exemple, placez le fichier jar dans *"/dbfs/FileStore/jars/"DataBricks* 

### <a name="step-4-prepare-to-run-job"></a>Étape 4. Préparer l’exécution du travail
 
1. Vous trouverez ci-dessous des instructions pour chaque paramètre de travail : pour capturer la traçabilité de travaux spécifique, utilisez spark-submit pour lancer un travail avec leur paramètre. 

    Dans le jeu de paramètres de travail :
* Chemin d’accès du fichier jar du connecteur. 
* Trois écouteurs : extraListeners, queryExecutionListeners, streamingQueryListeners comme connecteur. 

| Écouteur | Détails |
| ------------------- | ------------------- | 
| spark.extraListeners  | com.hortonworks.spark.atlas.SparkAtlasEventTracker|
| spark.sql.queryExecutionListeners | com.hortonworks.spark.atlas.SparkAtlasEventTracker
| spark.sql.streaming.streamingQueryListeners | com.hortonworks.spark.atlas.SparkAtlasStreamingQueryEventTracker |

* Chemin d’accès du fichier jar de votre application de travail Spark.

Configurer la tâche Databricks : la partie clé est d’utiliser spark-submit pour exécuter un travail avec les écouteurs configurés correctement. Définissez les informations de l’écouteur dans le paramètre de tâche.   

Vous trouverez ci-dessous un exemple de paramètre pour le travail Spark.

```script
["--jars","dbfs:/FileStore/jars/spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar ","--conf","spark.extraListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker","--conf","spark.sql.queryExecutionListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker","--conf","spark.sql.streaming.streamingQueryListeners=com.hortonworks.spark.atlas.SparkAtlasStreamingQueryEventTracker","--class","com.microsoft.SparkAtlasTest","dbfs:/FileStore/jars/08cde51d_34d8_4913_a930_46f376606d7f-sparkatlas_1_6_SNAPSHOT-17452.jar"]
```

Vous trouverez ci-dessous un exemple d’envoi de commande Spark à partir de la ligne de commande :

```script
spark-submit --class com.microsoft.SparkAtlasTest --master yarn --deploy-mode --files /data/atlas-application.properties --jars /data/ spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar 
--conf spark.extraListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker 
--conf spark.sql.queryExecutionListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker 
--conf spark.sql.streaming.streamingQueryListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker
/data/worked/sparkApplication.jar
```

2. Les instructions ci-dessous concernent le paramètre de cluster : les paramètres jar et de l’écouteur doivent être placés dans les clusters Spark : *conf/spark-defaults.conf*. Spark-submit va lire les options dans *conf/spark-defaults.conf* et les transmettre à votre application. 
 
### <a name="step-5-run-and-check-lineage-in-purview-account"></a>Étape 5. Exécuter et vérifier la traçabilité dans le compte Purview
Démarrez le travail Spark et vérifiez les informations de lignage dans votre compte Purview. 

:::image type="content" source="./media/how-to-lineage-spark-atlas-connector/purview-with-spark-lineage.png" alt-text="Capture d’écran montrant Purview avec la traçabilité Spark" lightbox="./media/how-to-lineage-spark-atlas-connector/purview-with-spark-lineage.png":::

## <a name="known-limitations-with-the-connector-for-spark-lineage"></a>Limitations connues avec le connecteur pour la traçabilité Spark
1. Prend en charge l’API SQL/DataFrame (en d’autres termes, ne prend pas en charge RDD). Ce connecteur s’appuie sur l’écouteur de requête pour récupérer la requête et examiner les impacts.
    
2. Toutes les « entrées » et « sorties » de plusieurs requêtes sont combinées en une seule entité « spark_process ».
    
    « spark_process » est mappé à un « applicationId » dans Spark. Cela permet à l’administrateur d’effectuer le suivi de toutes les modifications qui se sont produites dans le cadre d’une application. Toutefois, cela rendra le graphique de traçabilité/relation dans « spark_process » plus complexe et moins significatif.
3. Seule une partie des entrées est suivie dans une requête de diffusion en continu.

* La source Kafka prend en charge l’abonnement avec « pattern » et ce connecteur n’énumère pas toutes les rubriques correspondantes existantes, ou même toutes les rubriques possibles 
 
* « executed plan » fournit des sujets réels avec des (micro-)lectures par lots et processus. Par conséquent, seules les entrées participant au (micro-)lot sont incluses comme « entrées » de l’entité « spark_process ».
    
4. Ce connecteur ne prend pas en charge la traçabilité au niveau des colonnes.

5. Il n’effectue pas le suivi des tables qui sont supprimées (modèles Spark).

    L’événement « drop table » de Spark fournit uniquement la base de code et le nom de la table, ce qui n’est pas suffisant pour créer la clé unique permettant de reconnaître la table.

    Le connecteur dépend de la lecture du catalogue Spark pour recevoir des informations sur la table. Spark a déjà supprimé la table quand ce connecteur constate que la table est supprimée, drop table ne fonctionne donc pas.


## <a name="next-steps"></a>Étapes suivantes

- [Découvrir la traçabilité des données dans Azure Purview](catalog-lineage-user-guide.md)
- [Lier Azure Data Factory pour pousser la traçabilité automatisée](how-to-link-azure-data-factory.md)
