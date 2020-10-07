---
title: Sécurisation des informations d’identification d’accès avec les services liés dans Apache Spark pour Azure Synapse Analytics
description: Cet article explique comment intégrer de manière sécurisée Apache Spark pour Synapse Analytics à d’autres services en utilisant les services liés et la bibliothèque de jetons
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 90e7297236994650e0820e883c94a98b29c49fb7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249414"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>Sécurisation de vos informations d’identification via les services liés avec la bibliothèque de jetons (TokenLibrary)
L’accès aux données de sources externes est un modèle courant. À moins que la source de données externe autorise l’accès anonyme, il est probable que vous aurez besoin de sécuriser votre connexion avec des informations d’identification, un secret ou une chaîne de connexion.  

Azure Synapse Analytics propose des services liés pour simplifier le processus d’intégration en stockant les détails de connexion dans un service lié ou Azure Key Vault. Une fois que vous avez créé un service lié, Apache Spark peut faire référence au service lié pour appliquer les informations de connexion dans votre code. 

Pour plus d’informations, consultez [services liés](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
> [!NOTE]
> Sachant que l’accès aux fichiers d’Azure Data Lake Storage à partir votre espace de travail utilise l’authentification directe AAD, vous n’avez pas besoin d’utiliser TokenLibrary. 


## <a name="prerequisite"></a>Prérequis
* Service lié : vous devez créer un service lié à la source de données externe et référencer le service lié à partir de la bibliothèque de jetons. Apprenez-en davantage sur les [services liés](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Se connecter à ADLS Gen2 en dehors de l’espace de travail Synapse

Synapse propose une expérience de services liés intégrée pour Azure Data Lake Storage Gen2.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>Utiliser la bibliothèque de jetons

Pour vous connecter à d’autres services liés, vous pouvez effectuer un appel direct à TokenLibrary.

### <a name="getconnectionstring"></a>GetConnectionString
 Pour récupérer la chaîne de connexion, utilisez la fonction <b>getConnectionString</b> et transmettez le <b>nom du service lié</b>.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
Pour analyser les valeurs spécifiques d’une paire <i>clé=valeur</i> contenue dans la chaîne de connexion, telle que 

<i>DefaultEndpointsProtocol=https;AccountName=\<AccountName>;AccountKey=\<AccountKey></i>

utilisez la fonction <b>getConnectionStringAsMap</b> et transmettez la clé pour retourner la valeur.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>Étapes suivantes

- [Écrire dans le pool SQL](./synapse-spark-sql-pool-import-export.md)

