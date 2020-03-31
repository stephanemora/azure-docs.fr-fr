---
title: Traiter les journaux de flux de modification dans Stockage Blob Azure (préversion) | Microsoft Docs
description: Découvrez comment traiter les journaux de flux de modification dans une application cliente .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111853"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Flux de modification dans le Stockage Blob Azure (version préliminaire)

Le flux de modification fournit des journaux des transactions de toutes les modifications apportées aux objets blob et aux métadonnées d’objets blob dans votre compte de stockage. Cet article explique comment lire les enregistrements de flux de modification à l’aide de la bibliothèque du processeur de flux de modification d’objet blob.

Pour en savoir plus sur le flux de modification, consultez [Flux de modification dans Stockage Blob Azure (préversion)](storage-blob-change-feed.md).

> [!NOTE]
> Le flux de modification est en préversion publique et est disponible dans les régions **westcentralus** et **westus2**. Pour en savoir plus sur cette fonctionnalité, ainsi que sur les problèmes connus et les limitations, consultez [Prise en charge du flux de modification dans Stockage Blob Azure](storage-blob-change-feed.md). La bibliothèque du processeur de flux de modification est sujette à modification entre maintenant et le moment où cette bibliothèque est généralement disponible.

## <a name="get-the-blob-change-feed-processor-library"></a>Obtenir la bibliothèque du processeur de flux de modification d’objet blob

1. Dans Visual Studio, ajoutez l’URL `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` à vos sources de package NuGet. 

   Pour en savoir plus, consultez [sources de package](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. Dans le gestionnaire de package NuGet, recherchez le package **Microsoft.Azure.Storage.Changefeed**, puis installez-le dans votre projet. 

   Pour savoir comment procéder, consultez [Rechercher et installer un package](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Se connecter au compte de stockage

Analysez la chaîne de connexion en appelant la méthode [CloudStorageAccount.TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse). 

Ensuite, créez un objet qui représente le stockage d’objets blob dans votre compte de stockage en appelant la méthode [CloudStorageAccount.CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient).

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>Initiliser le flux de modification

Ajoutez les instructions using suivantes au début de votre fichier de code. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Ensuite, créez une instance de la classe **ChangeFeed** en appelant la méthode **GetContainerReference**. Passez le nom du conteneur de flux de modification.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Lecture des enregistrements

> [!NOTE]
> Le flux de modification est une entité immuable et en lecture seule dans votre compte de stockage. Un nombre quelconque d’applications peut lire et traiter le flux de modification simultanément et de manière indépendante. Les enregistrements ne sont pas supprimés du flux de modification quand une application les lit. L’état de lecture ou d’itération de chaque lecteur de consommation est indépendant et géré uniquement par votre application.

La façon la plus simple de lire des enregistrements consiste à créer une instance de la classe **ChangeFeedReader**. 

Cet exemple itère au sein de tous les enregistrements du flux de modification, puis imprime sur la console quelques valeurs de chaque enregistrement. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Reprise de la lecture des enregistrements à partir d’une position enregistrée

Vous pouvez choisir d’enregistrer votre position de lecture dans votre flux de modification et de reprendre l’itération des enregistrements à un moment ultérieur. Vous pouvez enregistrer l’état de votre itération du flux de modification à tout moment à l’aide de la méthode **ChangeFeedReader.SerializeState()** . L’état est une **chaîne** et votre application peut enregistrer cet état en fonction de la conception de votre application (par exemple, une base de données ou un fichier).

```csharp
    string currentReadState = processor.SerializeState();
```

Vous pouvez poursuivre l’itération des enregistrements à partir du dernier état en créant le **ChangeFeedReader** à l’aide de la méthode **CreateChangeFeedReaderFromPointerAsync**.

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Traitement des flux de données d’enregistrement

Vous pouvez choisir de traiter les enregistrements de flux de modification à mesure qu’ils arrivent. Voir [Spécifications](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Lecture d’enregistrements dans un intervalle de temps

Le flux de modification est organisé en segments horaires en fonction de l’heure de modification de l’événement. Voir [Spécifications](storage-blob-change-feed.md#specifications). Vous pouvez lire des enregistrements à partir de segments de flux de modification qui se trouvent dans un intervalle de temps spécifique.

Cet exemple obtient les heures de début de tous les segments. Ensuite, il itère au sein de cette liste jusqu’à ce que l’heure de début soit postérieure à l’heure du dernier segment consommé ou au-delà de l’heure de fin de la plage souhaitée. 

### <a name="selecting-segments-for-a-time-range"></a>Sélection de segments pour un intervalle de temps

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Lecture d’enregistrements dans un segment

Vous pouvez lire des enregistrements à partir de segments individuels ou de plages de segments.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Lecture d’enregistrements à partir d’une heure

Vous pouvez lire les enregistrements du flux de modification à partir d’un segment de départ et jusqu’à la fin. Comme pour lire des enregistrements dans une plage de temps, vous pouvez répertorier les segments et choisir un segment à partir duquel commencer l’itération.

Cet exemple obtient le [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) du premier segment à traiter.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

Cet exemple traite les enregistrements de flux de modification à partir du [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) d’un segment de départ.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Un segment peut avoir des journaux de flux de modification dans un ou plusieurs *chunkFilePath*. En cas de plusieurs *chunkFilePath*, le système a partitionné en interne les enregistrements en plusieurs partitions pour gérer le débit de publication. Il est garanti que chaque partition du segment contiendra des modifications pour les objets blob mutuellement exclusifs et peut être traitée indépendamment sans enfreindre le classement. Vous pouvez utiliser la classe **ChangeFeedSegmentShardReader** pour itérer au sein des enregistrements au niveau partition si cette solution est la plus efficace pour votre scénario.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les journaux de flux de modification. Voir [Change feed in Azure Blob Storage (Preview)](storage-blob-change-feed.md) (Flux de modification dans le Stockage Blob Azure [version préliminaire])
