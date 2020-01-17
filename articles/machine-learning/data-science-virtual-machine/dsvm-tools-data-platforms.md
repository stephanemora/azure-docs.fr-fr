---
title: Plateformes de données prises en charge
titleSuffix: Azure Data Science Virtual Machine
description: Apprenez-en davantage sur les outils et plateformes de données pris en charge par l’environnement Azure Data Science Virtual Machine.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bfae8147c348c76fa0e406fec283144ebc26e86b
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615328"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Plateformes de données prises en charge sur la machine virtuelle DSVM

DSVM (Data Science Virtual Machine) vous permet de générer votre analytique sur un large éventail de plateformes de données. En plus des interfaces sur les plateformes de données distantes, la machine virtuelle DSVM fournit une instance locale pour le développement et le prototypage rapides.

Les outils de plateforme de données pris en charge sur DSVM sont les suivants.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Une instance de base de données relationnelle locale      |
| Éditions DSVM prises en charge      | Windows : SQL Server 2017, Windows 2019 (préversion) : SQL Server 2019      |
| Utilisations classiques      | Développement rapide localement avec le plus petit jeu de données <br/> Exécution de R en base de données   |
| Liens vers des exemples      |    Un petit échantillon du jeu de données New York City est chargé dans la base de données SQL :<br/>  `nyctaxi` <br/> Vous trouverez un exemple Jupyter illustrant l’analytique Microsoft Machine Learning Server et en base de données à l’emplacement suivant :<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Outils connexes sur la machine virtuelle DSVM       | SQL Server Management Studio <br/> Pilotes ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> L’édition Développeur de SQL Server peut uniquement être utilisée à des fins de test et de développement. Vous avez besoin d’une licence ou de l’une des machines virtuelles SQL Server pour l’exécuter en production.


### <a name="setup"></a>Programme d’installation

Le serveur de base de données est déjà préconfiguré et les services Windows associés à SQL Server (comme `SQL Server (MSSQLSERVER)`) sont définis pour s’exécuter automatiquement. La seule étape manuelle implique l’activation de l’analytique en base de données à l’aide de Microsoft Machine Learning Server. Vous pouvez activer les analyses en exécutant la commande suivante une seule fois dans SQL Server Management Studio (SSMS). Exécutez cette commande après vous être connecté en tant qu’administrateur de l’ordinateur, ouvrez une nouvelle requête dans SSMS et assurez-vous que la base de données sélectionnée est `master` :

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Pour exécuter l’outil SQL Server Management Studio, vous pouvez rechercher « SQL Server Management Studio » dans la liste des programmes ou utiliser Windows Search pour le rechercher et l’exécuter. Quand vous êtes invité à fournir des informations d’identification, séelctionnez **Authentification Windows** et utilisez le nom d’ordinateur ou ```localhost``` dans le champ **Nom du serveur SQL**.

### <a name="how-to-use-and-run-it"></a>Comment l’utiliser et l’exécuter ?

Par défaut, le serveur de base de données avec l’instance de base de données par défaut s’exécute automatiquement. Vous pouvez utiliser des outils tels que SQL Server Management Studio sur la machine virtuelle pour accéder à la base de données SQL Server localement. Les comptes administrateurs locaux ont un accès administrateur à la base de données.

De plus, l’environnement DSVM est fourni avec des pilotes ODBC et JDBC pour communiquer avec SQL Server, les bases de données Azure SQL et Azure SQL Data Warehouse à partir d’applications écrites dans plusieurs langages, notamment Python et Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Comment est-il configuré et installé sur la machine virtuelle DSVM ? 

 SQL Server est installé de manière standard. Il se trouve dans `C:\Program Files\Microsoft SQL Server`. L’instance de Machine Learning Server en base de données se trouve à l’emplacement `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. L’environnement DSVM a aussi une instance de Machine Learning Server autonome distincte, installée à l’emplacement `C:\Program Files\Microsoft\R Server\R_SERVER`. Ces deux instances de Machine Learning Server ne partagent pas de bibliothèques.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autonome)

| | |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Une instance (nœud unique In-process) autonome de la plateforme populaire Apache Spark, un système pour le traitement des données et l’apprentissage automatique rapides et à grande échelle     |
| Éditions DSVM prises en charge      | Linux     |
| Utilisations classiques      | * Développement rapide d’applications Spark/PySpark localement avec un jeu de données plus petit et un déploiement ultérieur sur des clusters Spark volumineux tels qu’Azure HDInsight<br/> * Test du contexte Spark de Microsoft Machine Learning Server <br />* Utilisation de la bibliothèque [MMLSpark](https://github.com/Azure/mmlspark) open source de SparkML ou de Microsoft pour créer des applications ML |
| Liens vers des exemples      |    Exemple Jupyter : <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (contexte Spark) : /dsvm/samples/MRS/MRSSparkContextSample.R |
| Outils connexes sur la machine virtuelle DSVM       | PySpark, Scala<br/>Jupyter (noyaux Spark/PySpark)<br/>Microsoft Machine Learning Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Comment l’utiliser ?
Vous pouvez envoyer des travaux Spark sur la ligne de commande en exécutant la commande `spark-submit` ou `pyspark`. Vous pouvez également créer un bloc-notes Jupyter en créant un bloc-notes avec le noyau Spark.

Vous pouvez utiliser Spark à partir de R à l’aide de bibliothèques comme SparkR, Sparklyr et Microsoft Machine Learning Server, qui sont disponibles sur DSVM. Consultez les pointeurs vers les exemples dans le tableau précédent.

### <a name="setup"></a>Programme d’installation
Avant toute exécution dans un contexte Spark dans Microsoft Machine Learning Server sur l’édition DSVM Ubuntu Linux, vous devez effectuer une opération de configuration unique pour activer une instance Yarn et HDFS Hadoop à nœud unique locale. Par défaut, les services Hadoop sont installés mais désactivés sur la DSVM. Pour les activer, exécutez les commandes suivantes en tant que racine la première fois :

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Vous pouvez arrêter les services liés à Hadoop lorsque vous n’en avez plus besoin en exécutant ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

Vous trouverez dans le répertoire `/dsvm/samples/MRS` un exemple montrant comment développer et tester MRS dans un contexte Spark distant (l’instance Spark autonome sur DSVM).


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Comment est-il configuré et installé sur la machine virtuelle DSVM ? 
|Plateforme|Emplacement d’installation ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Les bibliothèques pour l’accès aux données à partir du Stockage Blob Azure ou d’Azure Data Lake Storage, à l’aide des bibliothèques d’apprentissage automatique MMLSpark de Microsoft, sont préinstallées dans $SPARK_HOME/jars. Ces fichiers JAR sont automatiquement chargés au démarrage de Spark. Par défaut, Spark utilise des données sur le disque local. 

Pour que l’instance Spark sur DSVM accède aux données stockées dans le Stockage Blob ou Azure Data Lake Storage, vous devez créer et configurer le fichier `core-site.xml` en fonction du modèle figurant dans $SPARK_HOME/conf/core-site.xml.template. Vous devez également disposer des informations d’identification appropriées pour accéder au Stockage Blob et à Azure Data Lake Storage. (Notez que les fichiers de modèle utilisent des espaces réservés pour les configurations du Stockage Blob et d’Azure Data Lake Storage.)

Pour plus d’informations sur la création des informations d’identification du service Azure Data Lake Storage, consultez [Authentification avec Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Une fois que les informations d’identification pour le Stockage Blob Azure ou Azure Data Lake Storage sont entrées dans le fichier core-site.xml, vous pouvez référencer les données stockées dans ces sources par le biais du préfixe d’URI wasb:// ou adl://.

