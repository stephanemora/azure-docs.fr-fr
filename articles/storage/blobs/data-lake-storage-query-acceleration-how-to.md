---
title: Filtrer des données à l’aide de l’accélération des requêtes Azure Data Lake Storage (préversion) | Microsoft Docs
description: Utilisez l’accélération des requêtes (préversion) pour récupérer un sous-ensemble de données à partir de votre compte de stockage.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: d7213bb44503fbe191a69683188bdea6976827ee
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930078"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrer des données à l’aide de l’accélération des requêtes Azure Data Lake Storage (préversion)

Cet article explique comment utiliser l’accélération des requêtes (préversion) pour récupérer un sous-ensemble de données à partir de votre compte de stockage. 

L’accélération des requêtes (préversion) est une nouvelle capacité d’Azure Data Lake Storage qui permet aux applications et aux infrastructures d’analytique d’optimiser considérablement le traitement des données en extrayant uniquement les données requises pour effectuer une opération donnée. Pour en savoir plus, consultez [Accélération des requêtes Azure Data Lake Storage (préversion)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> La fonctionnalité d’accélération des requêtes est disponible en préversion publique dans les régions Canada Centre et France Centre. Pour passer en revue les limitations, consultez l’article [Problèmes connus](data-lake-storage-known-issues.md). Pour vous inscrire à la préversion, voir [ce formulaire](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Prérequis

### <a name="net"></a>[.NET](#tab/dotnet)

- Pour accéder à Stockage Azure, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Un compte de stockage **universel v2**. Voir [Créer un compte de stockage](../common/storage-quickstart-create-account.md).

- [Kit de développement logiciel (SDK) .NET](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Pour accéder à Stockage Azure, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Un compte de stockage **universel v2**. Voir [Créer un compte de stockage](../common/storage-quickstart-create-account.md).

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) version 8 ou ultérieure.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > Cet article suppose que vous avez créé un projet Java à l’aide d’Apache Maven. Pour obtenir un exemple de création de projet à l’aide d’Apache Maven, consultez [Configuration](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Installer des packages 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Téléchargez les packages d’accélération des requêtes. Vous pouvez obtenir un fichier. zip compressé qui contient ces packages à l’aide de ce lien : [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net). 

2. Extrayez le contenu de ce fichier dans le répertoire de votre projet.

3. Ouvrez votre Fichier projet ( *.csproj*) dans un éditeur de texte et ajoutez ces références de package à l’intérieur de l’élément \<Project\>.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Restaurez les packages du Kit de développement logiciel (SDK) de préversion. Cet exemple de commande restaure les packages du Kit de développement logiciel (SDK) de préversion à l’aide de la commande `dotnet restore`. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Restaurez toutes les autres dépendances à partir du référentiel NuGet public.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Créez le répertoire à la racine de votre projet. Le dossier racine est le répertoire qui contient le fichier **pom.xml**.

   > [!NOTE]
   > Les exemples de cet article supposent que le nom du répertoire est **lib**.

2. Téléchargez les packages d’accélération des requêtes. Vous pouvez obtenir un fichier. zip compressé qui contient ces packages à l’aide de ce lien : [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java). 

3. Extrayez les fichiers de ce fichier. zip dans le répertoire que vous avez créé. Dans notre exemple, ce répertoire s’appelle **lib**. 

4. Ouvrez le fichier *pom.xml* dans votre éditeur de texte. Ajoutez les éléments de dépendance suivants au groupe de dépendances. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>Ajouter des instructions


### <a name="net"></a>[.NET](#tab/dotnet)

Ajoutez les instructions `using` suivantes au début de votre fichier de code.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

L’accélération des requêtes récupère les données au format CSV et JSON. Par conséquent, veillez à ajouter des instructions using pour les bibliothèques d’analyse CSV ou JSON que vous choisissez d’utiliser. Les exemples qui apparaissent dans cet article analysent un fichier CSV à l’aide de la bibliothèque [CsvHelper](https://www.nuget.org/packages/CsvHelper/) qui est disponible sur le site de NuGet. Par conséquent, nous ajouterons ces instructions `using` au début du fichier de code.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Pour compiler des exemples présentés dans cet article, vous devrez également ajouter ces instructions `using`.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Ajoutez les instructions `import` suivantes au début de votre fichier de code.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Récupérer des données à l’aide d’un filtre

Vous pouvez utiliser SQL pour spécifier les prédicats de filtrage de lignes et les projections de colonne dans une demande d’accélération des requêtes. Le code suivant interroge un fichier CSV dans le stockage et retourne toutes les lignes de données où la troisième colonne correspond à la valeur `Hemingway, Ernest`. 

- Dans la requête SQL, le mot clé `BlobStorage` est utilisé pour indiquer le fichier qui est interrogé.

- Les références de colonnes sont spécifiées par `_N` où la première colonne est `_1`. Si le fichier source contient une ligne d’en-tête, vous pouvez faire référence aux colonnes par le nom spécifié dans la ligne d’en-tête. 

### <a name="net"></a>[.NET](#tab/dotnet)

La méthode asynchrone `BlobQuickQueryClient.QueryAsync` envoie la requête à l’API d’accélération des requêtes, puis transmet en continu les résultats à l’application sous la forme d’un objet [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8).

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

La méthode `BlobQuickQueryClient.openInputStream()` envoie la requête à l’API d’accélération des requêtes, puis transmet en continu les résultats à l’application sous la forme d’un objet `InputStream` qui peut être lu comme tout autre objet InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>Récupérer des colonnes spécifiques

Vous pouvez étendre vos résultats à un sous-ensemble de colonnes. De cette façon, vous récupérez uniquement les colonnes nécessaires pour effectuer un calcul donné. Cela permet d’améliorer les performances de l’application et de réduire les coûts, car moins de données sont transférées via le réseau. 

Ce code récupère uniquement la colonne `PublicationYear` pour tous les livres du jeu de données. Il utilise également les informations de la ligne d’en-tête dans le fichier source pour référencer des colonnes dans la requête.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

Le code suivant combine le filtrage de lignes et les projections de colonnes dans la même requête. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Formulaire d’inscription à l’accélération des requêtes](https://aka.ms/adls/qa-preview-signup)    
- [Accélération des requêtes Azure Data Lake Storage (préversion)](data-lake-storage-query-acceleration.md)
- [Informations de référence sur l’accélération des requêtes en langage SQL (préversion)](query-acceleration-sql-reference.md)
