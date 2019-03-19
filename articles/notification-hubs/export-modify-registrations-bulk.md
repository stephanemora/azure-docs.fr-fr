---
title: Exporter et importer en bloc, les inscriptions Azure Notification Hubs | Microsoft Docs
description: Découvrez comment utiliser la prise en charge de Notification Hubs en bloc pour effectuer un grand nombre d’opérations sur un concentrateur de notification, ou pour exporter toutes les inscriptions.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: jowargo
ms.openlocfilehash: a86c3bd85f9d611787a41754f49ee2475ba33a9a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58175776"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Exporter et importer des inscriptions Azure Notification Hubs en bloc
Il existe des scénarios dans lesquels il est nécessaire pour créer ou modifier un grand nombre d’inscriptions dans un concentrateur de notification. Certains de ces scénarios sont mises à jour de balise calculs par lots, ou la migration d’une implémentation push existante pour utiliser Notification Hubs.

Cet article explique comment exécuter un grand nombre d’opérations sur un concentrateur de notification, ou pour exporter toutes les inscriptions en bloc.

## <a name="high-level-flow"></a>Flux de haut niveau
Prise en charge de traitement par lots est conçu pour prendre en charge des travaux longs impliquant des millions d’inscriptions. Pour obtenir cette mise à l’échelle, prise en charge batch utilise le stockage Azure pour stocker la sortie et les détails de la tâche. Pour les opérations de mise à jour en bloc, l’utilisateur est requis pour créer un fichier dans un conteneur d’objets blob, dont le contenu est la liste des opérations de mise à jour de l’inscription. Lors du démarrage de la tâche, l’utilisateur fournit une URL vers l’objet blob d’entrée, ainsi qu’une URL dans un répertoire de sortie (également dans un conteneur d’objets blob). Une fois que le travail a démarré, l’utilisateur peut vérifier l’état en interrogeant un emplacement de l’URL fourni au démarrage de la tâche. Un travail spécifique peut uniquement effectuer des opérations d’un type spécifique (crée, met à jour ou suppressions). Les opérations d’exportation sont exécutées de façon analogue.

## <a name="import"></a>Importer

### <a name="set-up"></a>Installation
Cette section suppose que les entités suivantes :

- Un hub de notifications configuré.
- Un conteneur d’objets blob stockage Azure.
- Références aux packages NuGet Azure Service Bus et de stockage Azure.

### <a name="create-input-file-and-store-it-in-a-blob"></a>Créer le fichier d’entrée et le stocker dans un objet blob
Un fichier d’entrée contient une liste des inscriptions sérialisées en XML, une par ligne. À l’aide du Kit de développement, l’exemple de code suivant montre comment sérialiser les inscriptions et les télécharger vers le conteneur d’objets blob.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Le code précédent sérialise les inscriptions dans la mémoire, puis charge la totalité du flux dans un objet blob. Si vous avez chargé un fichier de plus de quelques mégaoctets, consultez l’aide d’objets blob Azure sur la façon d’effectuer ces étapes. par exemple, [objets BLOB de blocs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Créer des jetons d’URL
Une fois votre fichier d’entrée est chargé, générer l’URL à fournir à votre hub de notification pour le fichier d’entrée et le répertoire de sortie. Vous pouvez utiliser deux conteneurs d’objets blob différents pour l’entrée et de sortie.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Envoi du travail
Avec les deux URL d’entrée et de sortie, vous pouvez maintenant démarrer le traitement par lots.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

En plus de l’URL d’entrée et de sortie, cet exemple crée un `NotificationHubJob` objet qui contient un `JobType` objet, ce qui peut être un des types suivants :

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Une fois l’appel terminé, le travail est poursuivi par le concentrateur de notification, et vous pouvez vérifier son statut avec l’appel à [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

À l’issue de la tâche, vous pouvez inspecter les résultats en examinant les fichiers suivants dans votre répertoire de sortie :

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Ces fichiers contiennent la liste des opérations réussies et échouées à partir de votre lot. Le format de fichier est `.cvs`, dans lequel chaque ligne a le numéro de ligne du fichier d’entrée d’origine et la sortie de l’opération (généralement la description de l’enregistrement créé ou mis à jour).

### <a name="full-sample-code"></a>Exemple de code complet
L’exemple de code suivant importe les inscriptions vers un hub de notification.

```csharp
using Microsoft.ServiceBus.Notifications;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Exportation
L’exportation des inscriptions est similaire à l’importation, avec les différences suivantes :

- Vous devez uniquement l’URL de sortie.
- Vous créez un NotificationHubJob de type ExportRegistrations.

### <a name="sample-code-snippet"></a>Exemple d’extrait de code
Voici un exemple d’extrait de code pour l’exportation des inscriptions en Java :

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les inscriptions, consultez les articles suivants :

- [Gestion des inscriptions](notification-hubs-push-notification-registration-management.md)
- [Balises pour les inscriptions](notification-hubs-tags-segment-push-message.md)
- [Inscriptions de modèle](notification-hubs-templates-cross-platform-push-messages.md)
