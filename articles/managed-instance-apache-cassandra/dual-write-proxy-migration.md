---
title: Migration dynamique vers Azure Managed Instance pour Apache Cassandra en utilisant Apache Spark et un proxy de double écriture.
description: Découvrez comment effectuer une migration vers Azure Managed Instance pour Apache Cassandra en utilisant Apache Spark et un proxy de double écriture.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 06/02/2021
ms.openlocfilehash: c2529f355e7d39c22bf5f3d703cb33c59101a5f8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749458"
---
# <a name="live-migration-to-azure-managed-instance-for-apache-cassandra-by-using-a-dual-write-proxy"></a>Migration dynamique vers Azure Managed Instance pour Apache Cassandra en utilisant un proxy de double écriture

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quand c’est possible, nous vous recommandons d’utiliser la capacité native d’Apache Cassandra à migrer des données de votre cluster existant vers Azure Managed Instance pour Apache Cassandra en configurant un [cluster hybride](configure-hybrid-cluster.md). Cette capacité utilise le protocole gossip d’Apache Cassandra pour répliquer des données de votre centre de données source sur votre nouveau centre de données d’instance managée de façon directe. Cependant, il peut y avoir des scénarios où la version de la base de données source n’est pas compatible, ou dans le cas où une configuration de cluster hybride n’est pas possible. 

Cet article explique comment migrer des données vers Azure Managed Instance pour Apache Cassandra de façon dynamique en utilisant un [proxy de double écriture](https://github.com/Azure-Samples/cassandra-proxy) et Apache Spark. Les avantages de cette approche sont les suivants :

- **Modification minimale de l’application**. Le proxy peut accepter des connexions depuis votre code d’application avec peu ou pas de modifications de configuration. Il va router toutes les demandes vers votre base de données source et router de façon asynchrone les écritures vers une cible secondaire. 
- **Dépendance du protocole filaire du client**. Cette approche ne dépendant pas de ressources back-end ou de protocoles internes, elle peut être utilisée avec n’importe quel système Cassandra source ou cible implémentant le protocole filaire d’Apache Cassandra.

L’approche proposée est illustrée dans l’image suivante.


:::image type="content" source="./media/migration/live-migration.gif" alt-text="Animation montrant la migration dynamique de données vers Azure Managed Instance pour Apache Cassandra." border="false":::

## <a name="prerequisites"></a>Prérequis

* Provisionnez une instance Azure Managed Instance pour Apache Cassandra en utilisant le [portail Azure](create-cluster-portal.md) ou [Azure CLI](create-cluster-cli.md). Vérifiez que vous pouvez vous [connecter à votre cluster avec CQLSH](./create-cluster-portal.md#connecting-to-your-cluster).

* [Provisionnez un compte Azure Databricks à l’intérieur de votre réseau virtuel Cassandra géré](deploy-cluster-databricks.md). Veillez à ce que le compte dispose d’un accès réseau à votre cluster Cassandra source. Nous allons créer un cluster Spark dans ce compte pour le chargement des données d’historique.

* Veillez à avoir déjà migré l’espace de clés/le schéma de table de votre base de données Cassandra source vers votre base de données Managed Instance Cassandra cible.


## <a name="provision-a-spark-cluster"></a>Approvisionner un cluster Spark

Nous vous recommandons de sélectionner le runtime Azure Databricks version 7.5, qui prend en charge Spark 3.0.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="Capture d’écran montrant où trouver la version du runtime Azure Databricks.":::

## <a name="add-spark-dependencies"></a>Ajouter des dépendances Spark

Vous devez ajouter la bibliothèque du connecteur Apache Spark Cassandra à votre cluster pour vous connecter aux points de terminaison Cassandra natifs et Azure Cosmos DB. Dans votre cluster, sélectionnez **Bibliothèques** > **Installer nouveau** > **Maven**, puis ajoutez `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` dans les coordonnées Maven.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="Capture d’écran montrant la recherche de packages Maven dans Azure Databricks.":::

Sélectionnez **Installer**, puis redémarrez le cluster une fois l’installation terminée.

> [!NOTE]
> Veillez à redémarrer le cluster Azure Databricks après l’installation de la bibliothèque du connecteur Cassandra.

## <a name="install-the-dual-write-proxy"></a>Installer le proxy de double écriture

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

## <a name="start-the-dual-write-proxy"></a>Démarrer le proxy de double écriture

Nous vous recommandons d’installer le proxy sur tous les nœuds dans votre cluster Cassandra source. Au minimum, exécutez la commande suivante pour démarrer le proxy sur chaque nœud. Remplacez `<target-server>` par l’adresse IP ou de serveur de l’un des nœuds dans le cluster cible. Remplacez `<path to JKS file>` par le chemin vers un fichier .jks local, et remplacez `<keystore password>` par le mot de passe correspondant.  

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password>
```

Démarrer le proxy de cette façon suppose que les conditions suivantes sont réunies :

- Les points de terminaison source et cible ont le même nom d’utilisateur et le même mot de passe.
- Les points de terminaison source et cible implémentent SSL (Secure Sockets Layer).

Si vos points de terminaison source et cible ne peuvent pas répondre à ces critères, consultez les informations ci-dessous pour d’autres options de configuration. 

### <a name="configure-ssl"></a>Configuration du chiffrement SSL

Pour SSL, vous pouvez implémenter un magasin de clés existant (par exemple celui qu’utilise votre cluster source) ou créer un certificat auto-signé en utilisant `keytool` :

```bash
keytool -genkey -keyalg RSA -alias selfsigned -keystore keystore.jks -storepass password -validity 360 -keysize 2048
```
Vous pouvez aussi désactiver SSL pour les points de terminaison source ou cible s’ils n’implémentent pas SSL. Utilisez les indicateurs `--disable-source-tls` ou `--disable-target-tls` :

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password> --disable-source-tls true  --disable-target-tls true 
```

> [!NOTE]
> Vérifiez que votre application cliente utilise le même magasin de clés et le même mot de passe que ceux utilisés pour le proxy de double écriture lors de la création de connexions SSL à la base de données via le proxy.


### <a name="configure-the-credentials-and-port"></a>Configurer les informations d’identification et le port

Par défaut, les informations d’identification sources sont passées depuis votre application cliente. Le proxy va utiliser les informations d’identification pour établir des connexions avec les clusters source et cible. Comme mentionné plus haut, ce processus suppose que les informations d’identification de la source et de la cible sont identiques. Si nécessaire, vous pouvez spécifier un nom d’utilisateur et un mot de passe différents pour le point de terminaison Cassandra cible séparément lors du démarrage du proxy :

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

Si les ports source et cible ne sont pas spécifiés, le port par défaut est 9042. Si le point de terminaison Cassandra cible ou source s’exécute sur un autre port, vous pouvez utiliser `--source-port` ou `--target-port` pour spécifier un numéro de port différent : 

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

### <a name="deploy-the-proxy-remotely"></a>Déployer le proxy à distance

Dans certaines circonstances, vous ne voulez pas installer le proxy sur les nœuds de cluster proprement dits et vous préférez l’installer sur une machine distincte. Dans ce scénario, vous devez spécifier l’adresse IP de `<source-server>` :

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar <source-server> <destination-server>
```

> [!NOTE]
> Si vous n’installez pas et que vous n’exécutez pas le proxy sur tous les nœuds dans un cluster Apache Cassandra natif, ceci va affecter les performances de votre application. Le pilote du client ne pourra pas ouvrir des connexions avec tous les nœuds au sein du cluster. 

### <a name="allow-zero-application-code-changes"></a>Autoriser les modifications sans aucun code d’application

Par défaut, le proxy écoute sur le port 29042. Le code d’application doit être modifié de façon à pointer vers ce port. Cependant, vous pouvez changer le port sur lequel le proxy est à l’écoute. Vous pouvez faire ceci si vous voulez ne pas modifier le code au niveau de l’application :

- Faire en sorte que le serveur Cassandra source s’exécute sur un port différent.
- Faire en sorte que le proxy s’exécute sur le port Cassandra standard 9042.

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042
```

> [!NOTE]
> L’installation du proxy sur des nœuds de cluster ne nécessite pas de redémarrage des nœuds. Cependant, si vous avez de nombreux clients d’application et que vous préférez que le proxy s’exécute sur le port Cassandra standard 9042 afin d’éviter des modifications du code au niveau de l’application, vous devez changer le [port par défaut d’Apache Cassandra](https://cassandra.apache.org/doc/latest/cassandra/faq/#what-ports-does-cassandra-use). Vous devez ensuite redémarrer les nœuds de votre cluster et configurer le port source pour qu’il corresponde au nouveau port que vous avez défini pour votre cluster Cassandra source. 
>
> Dans l’exemple suivant, nous modifions le cluster Cassandra source pour qu’il s’exécute sur le port 3074 et nous démarrons le cluster sur le port 9042 :
>
>```bash
>java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042 --source-port 3074
>``` 

### <a name="force-protocols"></a>Forcer les protocoles

Le proxy dispose de fonctionnalités pour forcer les protocoles, ce qui peut être nécessaires si le point de terminaison source est plus avancé que la cible ou s’il n’est pas pris en charge. Dans ce cas, vous pouvez spécifier `--protocol-version` et `--cql-version` pour forcer le protocole à se conformer à la cible :

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --protocol-version 4 --cql-version 3.11
```

Une fois que le proxy à double écriture est en cours d’exécution, vous devez changer le port sur votre client d’application et redémarrer. (Ou bien changer le port Cassandra et redémarrer le cluster si vous avez choisi cette approche.) Le proxy va alors démarrer le transfert des écritures vers le point de terminaison cible. Des informations sur [la surveillance et les métriques](https://github.com/Azure-Samples/cassandra-proxy#monitoring) dans l’outil proxy sont disponibles. 


## <a name="run-the-historical-data-load"></a>Effectuer le chargement des données d’historique

Pour charger les données, créez un notebook Scala dans votre compte Azure Databricks. Remplacez vos configurations Cassandra source et cible par les informations d’identification correspondantes, et remplacez les espaces de clés et les tables sources et cibles. Ajoutez des variables à l’exemple ci-dessous pour chaque table en fonction des besoins, puis lancez l’exécution. Une fois que votre application a commencé à envoyer des demandes au proxy de double écriture, vous êtes prêt à migrer les données d’historique. 

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
> Dans l’exemple Scala précédent, notez que `timestamp` est défini sur l’heure actuelle avant de lire toutes les données de la table source. Ensuite `writetime` est défini sur cet horodatage antidaté. Ceci garantit que les enregistrements écrits depuis le chargement des données d’historique sur le point de terminaison cible ne peuvent pas remplacer les mises à jour qui arrivent avec un horodatage ultérieur en provenance du proxy de double écriture pendant la lecture des données d’historique.
>
> Si pour une raison quelconque, vous devez conserver les horodatages *exacts*, il vous faut adopter une approche de la migration des données d’historique qui conserve les horodatages, comme dans [cet exemple](https://github.com/Azure-Samples/cassandra-migrator). 

## <a name="validate-the-source-and-target"></a>Vérifier la source et la cible

Une fois le chargement des données d’historique terminé, vos bases de données doivent être synchronisées et prêtes pour le basculement. Cependant, nous vous recommandons d’examiner la source et la cible pour vérifier que les résultats de la demande sont bien en correspondance avant d’effectuer le basculement final.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)
