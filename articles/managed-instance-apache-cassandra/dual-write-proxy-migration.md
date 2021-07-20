---
title: Effectuer une migration dynamique vers Azure Managed Instance pour Apache Cassandra à l’aide d’Apache Spark et d’un proxy de double écriture.
description: Découvrez comment effectuer une migration vers Azure Managed Instance pour Apache Cassandra à l’aide d’Apache Spark et d’un proxy de double écriture.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 06/02/2021
ms.openlocfilehash: 104ee2d6238b32be32df0fb19d06f0bc5c9d4865
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970028"
---
# <a name="live-migration-to-azure-managed-instance-for-apache-cassandra-using-dual-write-proxy"></a>Migration dynamique vers Azure Managed Instance pour Apache Cassandra à l’aide d’un proxy de double écriture

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans la mesure du possible, nous vous recommandons d’utiliser la capacité native d’Apache Cassandra à migrer des données de votre cluster actuel vers Azure Managed Instance pour Apache Cassandra en configurant un [cluster hybride](configure-hybrid-cluster.md). Cette approche utilise le protocole gossip d’Apache Cassandra pour répliquer des données de votre centre de données source vers votre nouveau centre de données d’instance gérée de façon homogène. Toutefois, il peut y avoir des scénarios dans lesquels la version de la base de données source n’est pas compatible, ou une configuration de cluster hybride n’est pas possible. Cet article explique comment migrer des données vers Azure Managed Instance pour Apache Cassandra de façon dynamique à l’aide d’un [proxy de double écriture](https://github.com/Azure-Samples/cassandra-proxy) et d’Apache Spark. Les avantages de cette approche sont les suivants :

- **modifications d’application minimes** : le proxy peut accepter des connexions à partir de votre code d’application moyennant peu ou pas de changements de configuration. Il achemine toutes les demandes vers votre base de données source, et achemine les écritures de manière asynchrone vers une cible secondaire. 
- **dépendante du protocole wire client** : cette approche ne dépendant pas de ressources principales ou de protocoles internes, elle peut être utilisée avec tout système Cassandra source ou cible implémentant le protocole wire Apache Cassandra.

L’image ci-dessous illustre l’approche.


:::image type="content" source="./media/migration/live-migration.gif" alt-text="Azure Managed Instance pour Apache Cassandra est un service managé pour Apache Cassandra." border="false":::

## <a name="prerequisites"></a>Prérequis

* Approvisionnez un cluster Azure Managed Instance pour Apache Cassandra à l’aide du [portail Azure](create-cluster-portal.md) ou d’[Azure CLI](create-cluster-cli.md), et assurez-vous que vous pouvez [vous connecter à votre cluster avec CQLSH](./create-cluster-portal.md#connecting-to-your-cluster).

* [Approvisionnez un compte Azure Databricks à l’intérieur de votre réseau virtuel Cassandra géré](deploy-cluster-databricks.md). Veillez à ce qu’il dispose également d’un accès réseau à votre cluster Cassandra source. Nous allons créer un cluster Spark dans ce compte pour le chargement des données historiques.

* Assurez-vous que vous avez déjà migré l’espace de clés/le schéma de table de votre base de données Cassandra source vers votre base de données Managed Instance pour Cassandra cible.


## <a name="provision-a-spark-cluster"></a>Approvisionner un cluster Spark

Nous vous recommandons de sélectionner le runtime Azure Databricks version 7.5, qui prend en charge Spark 3.0.

:::image type="content" source="../cosmos-db/media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Capture d’écran montrant la détection de la version du runtime Databricks.":::

## <a name="add-spark-dependencies"></a>Ajouter des dépendances Spark

Vous devez ajouter la bibliothèque du connecteur Apache Spark Cassandra à votre cluster pour vous connecter aux points de terminaison Cassandra natifs et Azure Cosmos DB. Dans votre cluster, sélectionnez **Bibliothèques** > **Installer nouveau** > **Maven**, puis ajoutez `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` dans les coordonnées Maven.

:::image type="content" source="../cosmos-db/media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Capture d’écran montrant la recherche de packages Maven dans Databricks.":::

Sélectionnez **Installer**, puis redémarrez le cluster une fois l’installation terminée.

> [!NOTE]
> Veillez à redémarrer le cluster Databricks après l’installation de la bibliothèque du connecteur Cassandra.

## <a name="install-dual-write-proxy"></a>Installer un proxy de double écriture

Pour des performances optimales lors des doubles écritures, nous vous recommandons d’installer le proxy sur tous les nœuds dans votre cluster Cassandra source.

```bash
#assuming you do not have git already installed
sudo apt-get install git 

#assuming you do not have maven already installed
sudo apt install maven

#clone repo for dual-write proxy
git clone https://github.com/Azure-Samples/cassandra-proxy.git

#change directory
cd cassandra-proxy

#compile the proxy
mvn package
```

## <a name="start-dual-write-proxy"></a>Démarrer un proxy de double écriture

Nous vous recommandons d’installer le proxy sur tous les nœuds dans votre cluster Cassandra source. Au minimum, vous devez exécuter la commande suivante pour démarrer le proxy sur chaque nœud. Remplacez `<target-server>` par l’adresse IP ou de serveur de l’un des nœuds dans le cluster cible. Remplacez `<path to JKS file>` par le chemin d’accès à un fichier JKS local, et `<keystore password>` par le mot de passe correspondant :  

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password>
```

Le démarrage du proxy de cette façon suppose que les conditions suivantes sont réunies :

- Les points de terminaison source et cible ont les mêmes nom d’utilisateur et mot de passe.
- Les points de terminaison source et cible implémentent le protocole SSL.

Si vos points de terminaison source et cible ne peuvent pas répondre à ces critères, consultez les informations ci-dessous afin de découvrir d’autres options de configuration. 

### <a name="configure-ssl"></a>Configuration du chiffrement SSL

Pour SSL, vous pouvez soit implémenter un magasin de clés existant (par exemple, celui qu’utilise votre cluster source), soit créer un certificat auto-signé à l’aide de keytool :

```bash
keytool -genkey -keyalg RSA -alias selfsigned -keystore keystore.jks -storepass password -validity 360 -keysize 2048
```
Vous pouvez également désactiver le protocole SSL pour les points de terminaison source ou cible s’ils n’implémentent pas le protocole SSL. Utilisez les indicateurs `--disable-source-tls` ou `--disable-target-tls` :

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password> --disable-source-tls true  --disable-target-tls true 
```

> [!NOTE]
> Assurez-vous que votre application cliente utilise les mêmes magasin de clés et mot de passe que ceux utilisés pour le proxy de double écriture lors de la création de connexions SSL à la base de données via le proxy.


### <a name="configure-credentials-and-port"></a>Configurer les informations d’identification et le port

Par défaut, les informations d’identification sources sont transmises à partir de votre application cliente, et utilisées par le proxy pour établir des connexions aux clusters source et cible. Comme indiqué ci-dessus, cela suppose que les informations d’identification de la source et de la cible soient identiques. Si nécessaire, vous pouvez spécifier un nom d’utilisateur et un mot de passe différents pour le point de terminaison Cassandra cible séparément lors du démarrage du proxy :

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

Si les ports source et cible ne sont pas spécifiés, le port par défaut est `9042`. Si les points de terminaison Cassandra cible ou source s’exécutent sur un autre port, vous pouvez utiliser `--source-port` ou `--target-port` pour spécifier un numéro de port différent. 

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

### <a name="deploy-proxy-remotely"></a>Déployer le proxy à distance

Il peut arriver que vous ne souhaitiez pas installer le proxy sur les nœuds de cluster proprement dits, et préfériez l’installer sur un ordinateur distinct. Dans ce scénario, vous devez spécifier l’adresse IP du `<source-server>` :

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar <source-server> <destination-server>
```

> [!NOTE]
> Si vous n’installez et n’exécutez pas le proxy sur tous les nœuds dans un cluster Apache Cassandra natif, cela aura une incidence sur les performances de votre application, car le pilote du client ne sera pas en mesure d’ouvrir des connexions à tous les nœuds au sein du cluster. 

### <a name="allow-zero-application-code-changes"></a>Autoriser les modifications sans aucun code d’application

Par défaut, le proxy écoute sur le port `29042`. Pour ce faire, le code d’application doit être modifié de manière à pointer vers ce port. Toutefois, vous pouvez modifier le port sur lequel le proxy écoute. Vous pouvez être amené à faire cela si vous souhaitez éliminer les modifications du code au niveau de l’application en faisant en sorte que le serveur Cassandra source s’exécute sur un autre port et que le proxy s’exécute sur le port Cassandra standard `9042` :

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042
```

> [!NOTE]
> L’installation du proxy sur des nœuds de cluster ne nécessite pas de redémarrage des nœuds. En revanche, si vous avez de nombreux clients d’application et préférez que le proxy s’exécute sur le port Cassandra standard `9042` afin d’éliminer toute modification du code au niveau de l’application, vous devez modifier le [port par défaut Apache Cassandra](https://cassandra.apache.org/doc/latest/faq/#what-ports-does-cassandra-use). Vous devez ensuite redémarrer les nœuds de votre cluster et configurer le port source pour qu’il corresponde au nouveau port que vous avez défini pour votre cluster Cassandra source. Dans l’exemple ci-dessous, nous allons modifier le cluster Cassandra source pour qu’il s’exécute sur le port 3074 et démarrer le cluster sur le port 9042.
>```bash
>java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042 --source-port 3074
>``` 

### <a name="force-protocols"></a>Forcer les protocoles

Le proxy dispose de fonctionnalités pour forcer les protocoles, qui peuvent être nécessaires si le point de terminaison source est plus avancé, que la cible, voire non pris en charge. Dans ce cas, vous pouvez spécifier `--protocol-version` et `--cql-version` pour forcer le protocole à se conformer à la cible :

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --protocol-version 4 --cql-version 3.11
```

Une fois le proxy de double écriture opérationnel, vous devez modifier le port sur votre client d’application et redémarrer (ou modifier le port Cassandra et redémarrer le cluster si vous avez adopté cette approche). Le proxy démarre ensuite le transfert des écritures vers le point de terminaison cible. Des informations sur [la surveillance et les métriques](https://github.com/Azure-Samples/cassandra-proxy#monitoring) dans l’outil proxy sont disponibles. 


## <a name="run-the-historic-data-load"></a>Exécutez le chargement des données d’historique.

Pour charger les données, créez un notebook Scala dans votre compte Databricks. Remplacez vos configurations Cassandra source et cible par les informations d’identification correspondantes, ainsi que les espaces de clés et les tables sources et cibles. Ajoutez des variables à l’exemple ci-dessous pour chaque table en fonction des besoins, puis exécutez. Une fois que votre application a commencé à envoyer des demandes au proxy de double écriture, vous êtes prêt à migrer les données d’historique. 

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val sourceCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val targetCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.connection.remoteConnectionsPerExecutor" -> "1",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//set timestamp to ensure it is before read job starts
val timestamp: Long = System.currentTimeMillis / 1000

//Read from source Cassandra
val DFfromSourceCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(sourceCassandra)
  .load
  
//Write to target Cassandra
DFfromSourceCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(targetCassandra)
  .option("writetime", timestamp)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> Dans l’échantillon Scala ci-dessus, vous remarquerez que `timestamp` est défini sur l’heure actuelle avant la lecture de toutes les données dans la table source, puis que `writetime` est défini sur cet horodatage antidaté. Cela permet de s’assurer que les enregistrements écrits à partir du chargement des données d’historique vers le point de terminaison cible ne peuvent pas remplacer les mises à jour qui arrivent avec un horodatage ultérieur en provenance du proxy de double écriture pendant la lecture des données d’historique. Si, pour une raison quelconque, vous devez conserver les timestamps *exacts*, il vous faut adopter une approche de la migration des données d’historique qui conserve les timestamps, comme [cet](https://github.com/scylladb/scylla-migrator) exemple. 

## <a name="validation"></a>Validation

Une fois le chargement des données d’historique terminé, vos bases de données doivent être synchronisées et prêtes pour le basculement. Toutefois, il est recommandé d’effectuer une validation de la source et de la cible pour vous assurer que les résultats de la demande correspondent avant le basculement.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)