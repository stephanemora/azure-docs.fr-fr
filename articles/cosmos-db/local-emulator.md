---
title: Développer localement avec l’émulateur Azure Cosmos
description: L’émulateur Azure Cosmos vous permet de développer et de tester votre application localement, sans créer d’abonnement Azure et sans frais.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 01/31/2020
ms.openlocfilehash: 287933de6403d680c5aa5b6c78df49abe5f2ac56
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77591368"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Utiliser l’émulateur Azure Cosmos pour le développement et le test en local

L’émulateur Azure Cosmos fournit un environnement local qui émule le service Azure Cosmos DB à des fins de développement. Cet émulateur vous permet de développer et de tester votre application localement, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur Azure Cosmos, vous pouvez commencer à utiliser un compte Azure Cosmos dans le cloud.

Le développement à l’aide de l’émulateur Azure Cosmos est possible avec des comptes des API [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api) et [Table](local-emulator.md#table-api). Toutefois, à l’heure actuelle, l’Explorateur de données dans l’émulateur ne prend totalement en charge que les clients pour l’API SQL. 

## <a name="how-the-emulator-works"></a>Fonctionnement de l’émulateur

L’émulateur Azure Cosmos fournit une émulation haute fidélité du service Azure Cosmos DB. Il prend en charge les mêmes fonctionnalités qu’Azure Cosmos DB, notamment la prise en charge de la création et de l’interrogation des données, la configuration et la mise à l’échelle des conteneurs, et l’exécution des procédures stockées et des déclencheurs. Vous pouvez développer et tester des applications à l’aide de l’émulateur Azure Cosmos et les déployer ensuite vers Azure à l’échelle mondiale en apportant une seule modification à la configuration du point de terminaison de connexion pour Azure Cosmos DB.

Si l’émulation du service Azure Cosmos DB est fidèle, l’implémentation de l’émulateur est différente de celle du service. Par exemple, l’émulateur utilise les composants du système d’exploitation standard, notamment le système de fichiers local pour la persistance, et la pile de protocole HTTPS pour la connectivité. Les fonctionnalités qui s’appuient sur l’infrastructure Azure, comme la réplication globale, la latence en quelques millisecondes pour les lectures/écritures et les niveaux de cohérence ajustables ne s’appliquent pas.

Vous pouvez migrer des données entre l’émulateur Azure Cosmos et le service Azure Cosmos DB à l’aide de l’[outil de migration de données Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Vous pouvez exécuter l’émulateur Azure Cosmos sur le conteneur Windows Docker. Consultez [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) pour en savoir plus sur la commande docker pull, et [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) pour obtenir `Dockerfile` et des informations complémentaires.

## <a name="differences-between-the-emulator-and-the-service"></a>Différences entre l’émulateur et le service

L’émulateur Azure Cosmos offrant un environnement émulé exécuté sur une station de travail de développement locale, il y a quelques différences entre les fonctionnalités de l’émulateur et celles d’un compte Azure Cosmos dans le cloud :

* À l’heure actuelle, l’Explorateur de données dans l’émulateur prend en charge les clients pour l’API SQL uniquement. La vue de l’Explorateur de données et les opérations pour les API MongoDB, Table, Graph et Cassandra d’Azure Cosmos DB ne sont pas entièrement prises en charge.
* L’émulateur Azure Cosmos prend uniquement en charge un seul compte fixe et une clé principale connue. La regénération de clé n’est pas possible dans l’émulateur Azure Cosmos, mais la clé par défaut peut être changée à l’aide de l’option de ligne de commande.
* L’émulateur Azure Cosmos n’est pas un service de stockage scalable et ne prend pas en charge un grand nombre de conteneurs.
* L’émulateur Azure Cosmos n’offre pas de [niveaux de cohérence Azure Cosmos DB](consistency-levels.md) différents.
* L’émulateur Azure Cosmos ne permet pas la [réplication entre plusieurs régions](distribute-data-globally.md).
* Votre copie de l’émulateur Azure Cosmos n’étant pas obligatoirement à jour par rapport aux dernières modifications apportées au service Azure Cosmos DB, nous vous conseillons de consulter la section [Planificateur de capacité Azure Cosmos DB](https://www.documentdb.com/capacityplanner) pour évaluer avec précision les besoins en débit de production (RU) de votre application.
* Quand vous utilisez l’émulateur Azure Cosmos, par défaut, vous pouvez créer jusqu’à 25 conteneurs de taille fixe (pris en charge uniquement avec les SDK Azure Cosmos DB), ou cinq conteneurs illimités avec l’émulateur Azure Cosmos. Pour plus d’informations sur la modification de cette valeur, voir la section relative à la [définition de la valeur PartitionCount](#set-partitioncount).

## <a name="system-requirements"></a>Configuration système requise

L’émulateur Azure Cosmos nécessite la configuration matérielle et logicielle suivante :

* Configuration logicielle requise
  * Windows Server 2012 R2, Windows Server 2016 ou Windows 10
  * Système d’exploitation 64 bits
* Conditions matérielles minimales requises
  * 2 Go de RAM
  * 10 Go d’espace disque disponible

## <a name="installation"></a>Installation

Vous pouvez télécharger et installer l’émulateur Azure Cosmos à partir du [Centre de téléchargement Microsoft](https://aka.ms/cosmosdb-emulator), ou exécuter l’émulateur sur Docker pour Windows. Pour obtenir des instructions sur l’utilisation de l’émulateur sur Docker pour Windows, consultez [Exécution sur Docker](#running-on-docker).

> [!NOTE]
> Pour installer, configurer et exécuter l’émulateur Azure Cosmos, vous devez avoir des privilèges d’administrateur sur l’ordinateur. L’émulateur va créer/ajouter un certificat, mais également définir les règles de pare-feu pour exécuter ses services. Il doit donc être en mesure d’exécuter ces opérations.

## <a name="running-on-windows"></a>Exécution sur Docker

Pour démarrer l’émulateur Azure Cosmos, sélectionnez le bouton Démarrer ou appuyez sur la touche Windows. Commencez à taper **Émulateur Azure Cosmos**, puis sélectionnez l’émulateur dans la liste des applications.

![Sélectionnez le bouton Démarrer ou appuyez sur la touche Windows, commencez à taper **Émulateur Azure Cosmos**, puis sélectionnez l’émulateur dans la liste des applications](./media/local-emulator/database-local-emulator-start.png)

Lorsque l’émulateur est en cours d’exécution, une icône est affichée dans la zone de notification de la barre des tâches Windows. ![Notification dans la barre des tâches de l'émulateur local Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

Par défaut, l’émulateur Azure Cosmos s’exécute sur la machine locale (« localhost ») et écoute sur le port 8081.

L’émulateur Azure Cosmos est installé dans `C:\Program Files\Azure Cosmos DB Emulator` par défaut. Vous pouvez également démarrer et arrêter l’émulateur à partir de la ligne de commande. Pour plus d’informations, consultez la section [Référence de l’outil en ligne de commande](#command-line).

## <a name="start-data-explorer"></a>Démarrer l’Explorateur de données

Au démarrage de l’émulateur Azure Cosmos, l’Explorateur de données Azure Cosmos s’ouvre automatiquement dans votre navigateur. L’adresse apparaît sous la forme `https://localhost:8081/_explorer/index.html`. Si vous fermez l’explorateur et que vous souhaitez le rouvrir ultérieurement, vous pouvez ouvrir l’URL dans votre navigateur ou la lancer à partir de l’émulateur Azure Cosmos dans l’icône de la barre d’état système Windows, comme illustré ci-dessous.

![Lanceur de l’Explorateur de données de l’émulateur local Azure Cosmos](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Recherche de mises à jour

L’Explorateur de données indique si une nouvelle mise à jour est disponible en téléchargement.

> [!NOTE]
> Certaines données créées dans une version de l’émulateur Azure Cosmos (voir %LOCALAPPDATA%\CosmosDBEmulator ou les paramètres facultatifs du chemin des données) peuvent ne pas être accessibles à partir d’une autre version. Si vous devez rendre vos données persistantes à long terme, nous vous recommandons de les stocker dans un compte Azure Cosmos plutôt que dans l’émulateur Azure Cosmos.

## <a name="authenticating-requests"></a>Authentification des demandes

Tout comme avec Azure Cosmos DB dans le cloud, chaque requête envoyée à l’émulateur Azure Cosmos doit être authentifiée. L’émulateur Azure Cosmos prend uniquement en charge un seul compte fixe et une clé d’authentification connue pour l’authentification de la clé principale. Ce compte et cette clé sont les seules informations d’identification autorisées avec l’émulateur Azure Cosmos. Il s'agit de :

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> La clé principale prise en charge par l’émulateur Azure Cosmos peut être utilisée uniquement avec l’émulateur. Vous ne pouvez pas utiliser votre compte et votre clé Azure Cosmos DB de production avec l’émulateur Azure Cosmos.

> [!NOTE]
> Si vous avez démarré l’émulateur avec l’option /Key, utilisez la clé générée à la place de `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Pour plus d’informations sur l’option /Key, consultez la section [Référence sur l’outil en ligne de commande](#command-line).

Comme Azure Cosmos DB, l’émulateur Azure Cosmos prend en charge uniquement la communication sécurisée via SSL.

## <a name="running-on-a-local-network"></a>Exécution sur un réseau local

Vous pouvez exécuter l’émulateur sur un réseau local. Pour activer l’accès réseau, spécifiez l’option `/AllowNetworkAccess` sur la [ligne de commande](#command-line-syntax), avec `/Key=key_string` ou `/KeyFile=file_name`. Vous pouvez initialement utiliser `/GenKeyFile=file_name` pour générer un fichier avec une clé aléatoire. Passez ensuite cette clé à `/KeyFile=file_name` ou `/Key=contents_of_file`.

Avant d’activer l’accès réseau pour la première fois, l’utilisateur doit arrêter l’émulateur et supprimer le répertoire de données de l’émulateur (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Développement avec l’émulateur

### <a name="sql-api"></a>API SQL

Une fois que l’émulateur Azure Cosmos est démarré sur votre bureau, vous pouvez utiliser n’importe quel [SDK Azure Cosmos DB](sql-api-sdk-dotnet.md) pris en charge ou l’[API REST Azure Cosmos DB](/rest/api/cosmos-db/) pour interagir avec lui. L’émulateur Azure Cosmos fournit également un Explorateur de données à partir duquel vous pouvez créer des conteneurs pour l’API SQL ou l’API de Cosmos DB pour Mongo DB, ou encore afficher et modifier des éléments sans avoir à écrire de code.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API d’Azure Cosmos DB pour MongoDB

Une fois que l’émulateur Azure Cosmos est démarré sur votre bureau, vous pouvez utiliser l’[API d’Azure Cosmos DB pour MongoDB](mongodb-introduction.md) pour interagir avec lui. Démarrez l’émulateur à partir d’une invite de commandes administrateur avec « /EnableMongoDbEndpoint ». Utilisez ensuite la chaîne de connexion suivante pour vous connecter au compte de l’API MongoDB :

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>API de table

Une fois que l’émulateur Azure Cosmos est démarré sur votre bureau, vous pouvez utiliser le [SDK API Table d’Azure Cosmos DB](table-storage-how-to-use-dotnet.md) pour interagir avec lui. Démarrez l’émulateur à partir d’une invite de commandes administrateur avec « /EnableTableEndpoint ». Ensuite, exécutez le code suivant pour vous connecter au compte d’API Table :

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>API Cassandra

Démarrez l’émulateur à partir d’une invite de commandes administrateur avec « /EnableCassandraEndpoint ». Vous pouvez également définir la variable d’environnement `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [Installez Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Installez Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* Exécutez les commandes ci-dessous à partir d’une fenêtre d’invite de commandes :

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* Dans l’interpréteur de commandes CQLSH, exécutez les commandes suivantes pour vous connecter au point de terminaison Cassandra :

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>API Gremlin

Démarrez l’émulateur à partir d’une invite de commandes administrateur avec « /EnableGremlinEndpoint ». Vous pouvez également définir la variable d’environnement `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Installez apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

* Dans l’Explorateur de données de l’émulateur, créez une base de données « db1 » et une collection « coll1 » ; pour la clé de partition, définissez le nom avec « /name »

* Exécutez les commandes ci-dessous à partir d’une fenêtre d’invite de commandes :

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* Dans l’interpréteur de commandes Gremlin, exécutez les commandes suivantes pour vous connecter au point de terminaison Gremlin :

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>Exporter le certificat SSL

Les langages .NET et le runtime utilisent le magasin de certificats Windows pour se connecter en toute sécurité à l’émulateur local Azure Cosmos DB. D’autres langages ont leur propre méthode de gestion et de l’utilisation des certificats. Java utilise son propre [magasin de certificats](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), tandis que Python utilise des [wrappers de socket](https://docs.python.org/2/library/ssl.html).

Pour obtenir un certificat à utiliser avec les langages et les runtimes qui ne s’intègrent pas au magasin de certificats Windows, vous devrez l’exporter à l’aide du Gestionnaire de certificats Windows. Vous pouvez le démarrer en exécutant le fichier certlm.msc ou suivre les instructions étape par étape fournies dans [Exporter les certificats de l’émulateur Azure Cosmos](./local-emulator-export-ssl-certificates.md). Une fois le certificat en cours d’exécution, ouvrez les certificats personnels comme indiqué ci-dessous et exportez le certificat avec le nom convivial "DocumentDBEmulatorCertificate" en tant que fichier X.509 (.cer) codé en Base 64.

![Certificat SSL de l’émulateur local Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Le certificat X.509 peut être importé dans le magasin de certificats Java en suivant les instructions de la publication [Ajout d’un certificat au magasin de certificats d’autorité de certification Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Une fois le certificat importé dans le magasin de certificats, les clients pour l’API SQL et l’API d’Azure Cosmos DB pour MongoDB peuvent se connecter à l’émulateur Azure Cosmos.

Lors de la connexion à l’émulateur à partir des Kits de développement logiciel (SDK) Python et Node.js, la vérification SSL est désactivée.

## <a id="command-line"></a>Référence sur l’outil en ligne de commande
À partir de l’emplacement d’installation, vous pouvez utiliser la ligne de commande pour démarrer et arrêter l’émulateur, configurer les options et effectuer d’autres opérations.

### <a name="command-line-syntax"></a>Syntaxe de ligne de commande

    Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Pour afficher la liste des options, tapez `Microsoft.Azure.Cosmos.Emulator.exe /?` dans l’invite de commandes.

|**Option** | **Description** | **Commande**| **Arguments**|
|---|---|---|---|
|[aucun argument] | Démarre l’émulateur Azure Cosmos avec les paramètres par défaut. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Aide] |Affiche la liste des arguments de ligne de commande pris en charge.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Obtient l’état de l’émulateur Azure Cosmos. L’état est indiqué par le code de sortie : 1 = démarrage, 2 = exécution, 3 = arrêté. Un code de sortie négatif indique qu’une erreur s’est produite. Aucune autre sortie n’est générée. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| Arrête l’émulateur Azure Cosmos.| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | Spécifie le chemin d’accès dans lequel stocker les fichiers de données. La valeur par défaut est %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\> : Un chemin accessible |
|Port | Spécifie le numéro de port à utiliser pour l'émulateur. La valeur par défaut est 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\> : Numéro de port unique |
| ComputePort | Spécifie le numéro de port à utiliser pour le service de passerelle Interop Compute. Le port de sonde du point de terminaison HTTP de la passerelle est calculé comme étant égal à ComputePort + 79. Par conséquent, les ports ComputePort et ComputePort + 79 doivent être ouverts et disponibles. La valeur par défaut est 8900. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\> : Numéro de port unique |
| EnableMongoDbEndpoint=3.2 | Active l’API MongoDB 3.2 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | Active l’API MongoDB 3.6 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Spécifie le numéro de port à utiliser pour l'API de compatibilité MongoDB. La valeur par défaut est 10255. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\> : Numéro de port unique|
| EnableCassandraEndpoint | Active l’API Cassandra | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Spécifie le numéro de port à utiliser pour le point de terminaison Cassandra. La valeur par défaut est 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\> : Numéro de port unique |
| EnableGremlinEndpoint | Active l’API Gremlin | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Numéro de port à utiliser pour le point de terminaison Gremlin. La valeur par défaut est 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\> : Numéro de port unique |
|EnableTableEndpoint | Active l’API Table Azure | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | Numéro de port à utiliser pour le point de terminaison Table Azure. La valeur par défaut est 8902. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\> : Numéro de port unique|
| KeyFile | Lit la clé d’autorisation dans le fichier spécifié. Utilisez l’option /GenKeyFile pour générer un fichier de clé | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\> : chemin du fichier |
| ResetDataPath | Supprime de manière récursive tous les fichiers du chemin spécifié. Si vous ne spécifiez pas de chemin, le chemin par défaut est %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<path\> : Chemins d'accès au fichier  |
| StartTraces  |  Démarre la collecte des journaux de trace du débogage à l’aide de LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Arrête la collecte des journaux de traces du débogage à l’aide de LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Démarre la collecte des journaux de traces du débogage à l’aide de l’outil d’enregistrement des performances Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Arrête la collecte des journaux de traces du débogage à l’aide de l’outil d’enregistrement des performances Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Par défaut, l’émulateur regénère son certificat SSL auto-signé, si le réseau SAN du certificat n’inclut pas les informations de l’hôte émulateur (nom de domaine, adresse IPv4 locale, « localhost » et « 127.0.0.1 »). Avec cette option, l’émulateur échoue au démarrage. Vous devez alors utiliser l’option /GenCert pour créer et installer un nouveau certificat SSL auto-signé. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Génère et installe un nouveau certificat SSL auto-signé, incluant de manière facultative une liste de noms DNS supplémentaires séparés par des virgules pour accéder à l’émulateur via le réseau. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<noms-dns\> |\<noms-dns\> : Liste de noms DNS supplémentaires séparés par des virgules (facultatif)  |
| DirectPorts |Spécifie les ports à utiliser pour une connectivité directe. Les valeurs par défaut sont 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\> : Liste de 4 ports séparée par des virgules |
| Clé |Clé d’autorisation pour l’émulateur. La clé doit être le codage en base 64 d’un vecteur de 64 octets. | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<clé\> | \<clé\> : La clé doit être le codage en base 64 d’un vecteur de 64 octets|
| EnableRateLimiting | Spécifie que le comportement de limitation de taux de demandes est activé. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Spécifie que le comportement de limitation de taux de demandes est désactivé. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Ne pas afficher l’interface utilisateur de l’émulateur. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Ne pas afficher l’Explorateur de données au démarrage. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Spécifie le nombre maximal de conteneurs partitionnés. Pour plus d’informations, consultez [Changer le nombre de conteneurs](#set-partitioncount). | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitionCount\> : nombre maximal de conteneurs à partition unique autorisés. La valeur par défaut est 25. Valeur maximale autorisée : 250.|
| DefaultPartitionCount| Spécifie le nombre par défaut de partitions pour un conteneur partitionné. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> : la valeur par défaut est 25.|
| AllowNetworkAccess | Permet d’accéder à l’émulateur sur un réseau. Vous devez également passer/Key =\<key_string\> ou/keyfile =\<nom_fichier\> pour activer l’accès réseau. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> ou  Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<nom_fichier\>| |
| NoFirewall | Ne pas ajuster les règles de pare-feu quand l’option /AllowNetworkAccess est utilisée. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Générer une nouvelle clé d’autorisation et Générer une nouvelle clé d’autorisation et l’enregistrer dans le fichier spécifié. La clé générée peut être utilisée avec les options /Key or /KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<chemin_fichier_clé\> | |
| Cohérence | Définir le niveau de cohérence par défaut pour le compte. | Microsoft.Azure.Cosmos.Emulator.exe /Consistency=\<consistency\> | \<cohérence\> : La valeur doit être l’un des [niveaux de cohérence](consistency-levels.md) suivants : Session, Strong, Eventual ou BoundedStaleness. La valeur par défaut est Session. |
| ? | Afficher le message d’aide.| | |

## <a id="set-partitioncount"></a>Changer le nombre de conteneurs

Par défaut, vous pouvez créer jusqu’à 25 conteneurs de taille fixe (pris en charge uniquement avec les SDK Azure Cosmos DB), ou cinq conteneurs illimités avec l’émulateur Azure Cosmos. En changeant la valeur **PartitionCount**, vous pouvez créer jusqu’à 250 conteneurs de taille fixe ou 50 conteneurs illimités, ou n’importe quelle combinaison des deux à hauteur de 250 conteneurs de taille fixe au total (sachant qu’un conteneur illimité est égal à cinq conteneurs de taille fixe). Toutefois, il est déconseillé de configurer l’émulateur pour s’exécuter avec plus de 200 conteneurs de taille fixe. En effet, cela ajoute une surcharge aux opérations d’E/S sur le disque, qui entraîne des délais d’attente imprévisibles lors de l’utilisation des API de point de terminaison.

Si vous tentez de créer un conteneur après le dépassement du nombre de partitions actuel, l’émulateur lève une exception ServiceUnavailable et affiche un message similaire à celui-ci.

« Nous sommes désolés, mais nous rencontrons actuellement une demande élevée dans cette région et nous ne pouvons pas répondre à votre demande pour l’instant. Nous nous efforçons d’offrir une capacité en ligne toujours plus grande, et nous vous encourageons à réessayer plus tard.
ActivityId : 12345678-1234-1234-1234-123456789abc »

Pour changer le nombre de conteneurs disponibles dans l’émulateur Azure Cosmos, effectuez les étapes suivantes :

1. Supprimez toutes les données locales de l’émulateur Azure Cosmos en cliquant avec le bouton droit sur l’icône **Émulateur Azure Cosmos DB** dans la zone d’état, puis en cliquant sur **Reset Data...** (Réinitialiser les données...).
2. Supprimez toutes les données de l’émulateur dans le dossier `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Quittez toutes les instances ouvertes en cliquant avec le bouton droit sur l’icône **Émulateur Azure Cosmos DB** dans la zone d’état, puis en cliquant sur **Quitter**. Quitter l’ensemble des instances peut prendre une minute.
4. Installez la dernière version de l’[émulateur Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Lancez l’émulateur avec l’indicateur PartitionCount en définissant une valeur <= 250. Par exemple : `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Contrôle de l’émulateur

L’émulateur est fourni avec un module PowerShell que vous pouvez utiliser pour démarrer, arrêter et désinstaller le service, et récupérer son état. Exécutez l’applet de commande suivante pour utiliser le module PowerShell :

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

Vous pouvez aussi placer le répertoire `PSModules` dans `PSModulesPath` et importer le module en utilisant la commande suivante :

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Voici un récapitulatif des commandes permettant de contrôler l’émulateur à partir de PowerShell :

### `Get-CosmosDbEmulatorStatus`

**Syntaxe**

`Get-CosmosDbEmulatorStatus`

**Remarques**

Retourne une des valeurs ServiceControllerStatus suivantes : ServiceControllerStatus.StartPending, ServiceControllerStatus.Running ou ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntaxe**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Remarques**

Démarre l’émulateur. Par défaut, la commande attend que l’émulateur soit prêt à accepter les requêtes. Utilisez l’option -NoWait si vous souhaitez que l’applet de commande soit retournée dès le démarrage de l’émulateur.

### `Stop-CosmosDbEmulator`

**Syntaxe**

 `Stop-CosmosDbEmulator [-NoWait]`

**Remarques**

Arrête l’émulateur. Par défaut, cette commande attend que l’émulateur soit complètement arrêté. Utilisez l’option -NoWait si vous souhaitez que l’applet de commande soit retournée dès le début du processus d’arrêt de l’émulateur.

### `Uninstall-CosmosDbEmulator`

**Syntaxe**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Remarques**

Désinstalle l’émulateur et supprime éventuellement l’intégralité du contenu de $env:LOCALAPPDATA\CosmosDbEmulator.
L’applet de commande garantit que l’émulateur est arrêté avant d’être désinstallé.

## <a name="running-on-docker"></a>Exécution sur Docker

L’émulateur Azure Cosmos peut être exécuté sur Docker pour Windows. L’émulateur ne fonctionne pas sur Docker pour Oracle Linux.

Une fois [Docker pour Windows](https://www.docker.com/docker-windows) installé, basculez vers les conteneurs Windows en double-cliquant sur l’icône Docker dans la barre d’outils et en sélectionnant **Switch to Windows containers (Basculer vers les conteneurs Windows)** .

Ensuite, extrayez l’image de l’émulateur à partir de Docker Hub en exécutant la commande suivante à partir de l’interpréteur de commandes de votre choix.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Pour démarrer l’image, exécutez les commandes suivantes :

À partir de la ligne de commande :
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> Si vous voyez une erreur de conflit de port (le port spécifié est déjà utilisé) quand vous exécutez la commande docker run, vous pouvez transmettre un port personnalisé en modifiant les numéros de port. Par exemple, vous pouvez remplacer « -p 8081:8081 » par « -p 443:8081 ».

À partir de PowerShell :
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

La réponse ressemble à ce qui suit :

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Utilisez maintenant le point de terminaison et la clé principale de la réponse sur votre client et importez le certificat SSL sur votre hôte. Pour importer le certificat SSL, procédez comme suit à partir d’une invite de commandes administrateur :

À partir de la ligne de commande :

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

À partir de PowerShell :
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Si vous fermez l’interpréteur de commandes interactif une fois que l’émulateur a démarré, le conteneur de l’émulateur s’arrête.

Pour ouvrir l’Explorateur de données, accédez à l’URL suivante dans votre navigateur. Le point de terminaison de l’émulateur est fourni dans le message de réponse ci-dessus.

    https://<emulator endpoint provided in response>/_explorer/index.html

Si vous avez une application cliente .NET en cours d’exécution sur un conteneur Docker Linux et si vous exécutez l’émulateur Azure Cosmos sur une machine hôte, suivez la section ci-dessous pour Linux afin d’importer le certificat dans le conteneur Docker Linux.

## Exécution sur Mac ou Linux<a id="mac"></a>

Actuellement, l’émulateur Cosmos peut uniquement être exécuté sur Windows. Les utilisateurs sous Mac ou Linux peuvent exécuter l’émulateur sur une machine virtuelle Windows hébergée sur un hyperviseur comme Parallels ou VirtualBox. Voici les étapes pour activer cela.

Dans la machine virtuelle Windows, exécutez la commande suivante et notez l’adresse IPv4.

```cmd
ipconfig.exe
```

Dans votre application, vous devez changer l’URI utilisé comme point de terminaison pour utiliser l’adresse IPv4 retournée par `ipconfig.exe` au lieu de `localhost`.

Ensuite, depuis la machine virtuelle Windows, lancez l’émulateur Cosmos à partir de la ligne de commande avec les options suivantes.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Enfin, nous devons importer le certificat de l’autorité de certification de l’émulateur dans l’environnement Linux ou Mac.

### <a name="linux"></a>Linux

Si vous travaillez sur Linux, .NET passe le relais à OpenSSL pour effectuer la validation :

1. [Exportez le certificat au format PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-ssl-certificate) (PFX est disponible quand vous choisissez d’exporter la clé privée). 

1. Copiez ce fichier PFX dans votre environnement Linux.

1. Convertir le fichier PFX en fichier CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Copiez le fichier CRT dans le dossier qui contient les certificats personnalisés dans votre distribution Linux. Sur les distributions Debian, il se trouve en général sur `/usr/local/share/ca-certificates/`.

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Mettez à jour les certificats d’autorité de certification, ce qui mettra à jour le dossier `/etc/ssl/certs/`.

   ```bash
   update-ca-certificates
   ```

### <a name="mac-os"></a>Mac OS

Suivez les étapes ci-dessous si vous travaillez sur Mac :

1. [Exportez le certificat au format PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-ssl-certificate) (PFX est disponible quand vous choisissez d’exporter la clé privée).

1. Copiez ce fichier PFX dans votre environnement Mac.

1. Ouvrez l’application *Keychain Access* et importez le fichier PFX.

1. Ouvrez la liste des certificats et identifiez celui qui a le nom `localhost`.

1. Ouvrez le menu contextuel pour cet élément particulier, sélectionnez *Obtenir l’élément* et, sous *Approuver* > *Lors de l’utilisation de ce certificat*, sélectionnez *Toujours approuver*. 

   ![Ouvrez le menu contextuel pour cet élément particulier, sélectionnez Obtenir l’élément et, sous Approuver - Lors de l’utilisation de ce certificat, sélectionnez Toujours approuver.](./media/local-emulator/mac-trust-certificate.png)

Une fois que vous avez effectué ces étapes, votre environnement approuve le certificat utilisé par l’émulateur lors de la connexion à l’adresse IP exposée par `/AllowNetworkAccess`.

## <a name="troubleshooting"></a>Dépannage

Aidez-vous des conseils suivants pour résoudre les problèmes rencontrés avec l’émulateur Azure Cosmos :

- Si vous avez installé une nouvelle version de l’émulateur et si vous rencontrez des erreurs, réinitialisez vos données. Pour réinitialiser vos données, cliquez avec le bouton droit sur l’icône de l’émulateur Azure Cosmos dans la zone d’état, puis cliquez sur Réinitialiser les données…. Si cela ne résout pas les erreurs, désinstallez l’émulateur et toute autre version ultérieure de l’émulateur détectée, supprimez le répertoire « C:\Program files\Azure Cosmos DB Emulator » et réinstallez l’émulateur. Pour obtenir des instructions, consultez [Désinstaller l’émulateur local](#uninstall).

- Si l’émulateur Azure Cosmos plante, collectez les fichiers de vidage du dossier « %LOCALAPPDATA%\CrashDumps », compressez-les et ouvrez un ticket de support à partir du [portail Azure](https://portal.azure.com).

- Si des problèmes de plantage se produisent dans `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, cela peut être le symptôme d’un état endommagé d’un compteur de performances. Généralement, l’exécution de la commande suivante à partir d’une invite de commandes administrateur résout le problème :

  ```cmd
  lodctr /R
   ```

- Si vous rencontrez un problème de connectivité, [collectez les fichiers de trace](#trace-files), compressez-les et ouvrez un ticket de support à partir du [portail Azure](https://portal.azure.com).

- Si vous recevez un message **Service indisponible**, il se peut que l’émulateur n’arrive pas à initialiser la pile réseau. Vérifiez si les clients Pulse Secure ou Juniper Networks sont installés, car leurs pilotes de filtre réseau peuvent être à l’origine du problème. La désinstallation des pilotes de filtre de réseau tiers permet généralement de résoudre le problème. Vous pouvez également démarrer l’émulateur avec l’option /DisableRIO, pour passer la communication réseau de l’émulateur à Winsock standard. 

- Lorsque l’émulateur est en cours d’exécution, si votre ordinateur se met en mode veille ou exécute une mise à jour du système d’exploitation, le message **Le service est actuellement indisponible** peut s’afficher. Réinitialisez les données de l’émulateur en cliquant avec le bouton droit sur l’icône qui apparaît dans la zone de notification Windows, puis en sélectionnant **Réinitialiser les données**.

### <a id="trace-files"></a>Collecter les fichiers de trace

Pour collecter des traces de débogage, exécutez les commandes suivantes à partir d’une invite de commande d’administration :

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. Regardez la barre d’état système pour vérifier que le programme s’est arrêté ; l’arrêt peut prendre une minute. Vous pouvez aussi simplement cliquer sur **Quitter** dans l’interface utilisateur de l’émulateur Azure Cosmos.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Reproduisez le problème. Si l’Explorateur de données ne fonctionne pas, attendez quelques secondes pour que le navigateur s’ouvre afin d’intercepter l’erreur.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Accédez à `%ProgramFiles%\Azure Cosmos DB Emulator` et recherchez le fichier docdbemulator_000001.etl.
8. Ouvrez un ticket de support dans le [portail Azure](https://portal.azure.com) et incluez le fichier. etl avec les étapes de reproduction.

### <a id="uninstall"></a>Désinstaller l’émulateur local

1. Quittez toutes les instances ouvertes de l’émulateur local en cliquant avec le bouton droit sur l’icône de l’émulateur Azure Cosmos dans la barre d’état système, puis en cliquant sur Quitter. Quitter l’ensemble des instances peut prendre une minute.
2. Dans la zone de recherche Windows, tapez **Applications et fonctionnalités**, puis cliquez sur le résultat **Applications et fonctionnalités (paramètres système)** .
3. Dans la liste des applications, faites défiler la page jusqu’à trouver **Émulateur Azure Cosmos DB**, sélectionnez-le, cliquez sur **Désinstaller**, puis confirmez en cliquant de nouveau sur **Désinstaller**.
4. Lorsque l’application est désinstallée, accédez à `%LOCALAPPDATA%\CosmosDBEmulator` et supprimez le dossier.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser l’émulateur local pour un développement local gratuit. Vous pouvez maintenant passer à l’étape suivante du didacticiel et découvrir comment exporter les certificats SSL de l’émulateur.

> [!div class="nextstepaction"]
> [Exporter les certificats de l’émulateur Azure Cosmos](local-emulator-export-ssl-certificates.md)
