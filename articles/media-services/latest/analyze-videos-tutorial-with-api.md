---
title: Analyser des vidéos avec Azure Media Services | Microsoft Docs
description: Suivez les étapes de ce didacticiel pour analyser des vidéos à l’aide d’Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: a228443a787db2cc71a7700b978d8f52edb59e14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Didacticiel : analyser des vidéos avec Azure Media Services 

Ce didacticiel vous montre comment analyser des vidéos avec Azure Media Services. Il existe plusieurs scénarios dans lesquels vous pouvez souhaiter obtenir des informations détaillées sur vos vidéos enregistrées ou vos contenus audio. Par exemple, pour obtenir une satisfaction plus élevée des clients, les organisations peuvent exécuter un traitement de reconnaissance vocale pour convertir les enregistrements du support client en un catalogue pouvant faire l’objet de recherches, avec des index et des tableaux de bord. Ensuite, elles peuvent obtenir des informations sur leur entreprise, comme la liste des réclamations courantes, leurs sources, etc.

Ce didacticiel vous explique les procédures suivantes :    

> [!div class="checklist"]
> * Lancement d’Azure Cloud Shell
> * Créer un compte Media Services
> * Accéder à l’API Media Services
> * Configurer l’exemple d’application
> * Examiner l’exemple de code en détail
> * Exécution de l'application
> * Analyser la sortie
> * Supprimer des ressources

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis


Si vous n’avez pas Visual Studio, vous pouvez obtenir [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Téléchargez l’exemple

Clonez un référentiel GitHub qui contient l’exemple .NET sur votre machine à l’aide de la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>Examiner l’exemple de code en détail

Cette section examine les fonctions définies dans le fichier [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) du projet *AnalyzeVideos*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Commencer à utiliser les API Media Services avec le Kit de développement logiciel (SDK) .NET

Pour commencer à utiliser les API Media Services avec .NET, vous devez créer un objet **AzureMediaServicesClient**. Pour créer l’objet, vous devez fournir les informations d’identification nécessaires pour que le client puisse se connecter à Azure à l’aide d’Azure AD. Vous devez d’abord récupérer un jeton, puis créer un objet **ClientCredential** à partir du jeton renvoyé. Dans le code que vous avez cloné au début de l’article, l’objet **ArmClientCredential** est utilisé pour récupérer le jeton.  

```csharp
private static IAzureMediaServicesClient CreateMediaServicesClient(ConfigWrapper config)
{
    ArmClientCredentials credentials = new ArmClientCredentials(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Créer une ressource de sortie pour stocker le résultat d’un travail 

La ressource de sortie stocke le résultat de votre travail. Le projet définit la fonction **DownloadResults** qui télécharge les résultats à partir de cette ressource de sortie dans le dossier « output », afin de voir ce que vous avez obtenu.

```csharp
private static Asset CreateOutputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName)
{
    // Check if an Asset already exists
    Asset outputAsset = client.Assets.Get(resourceGroupName, accountName, assetName);
    Asset asset = new Asset();
    string outputAssetName = assetName;

    if (outputAsset != null)
    {
        // Name collision! In order to get the sample to work, let's just go ahead and create a unique asset name
        // Note that the returned Asset can have a different name than the one specified as an input parameter.
        // You may want to update this part to throw an Exception instead, and handle name collisions differently.
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        outputAssetName += uniqueness;
    }

    return client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, asset);
}
```
### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Créer une transformation et un travail qui analyse les vidéos

Lors de l’encodage ou du traitement de contenus dans Media Services, il est courant de configurer les paramètres de codage en tant que formule. Vous envoyez ensuite un **travail** pour appliquer cette formule à une vidéo. En envoyant de nouveaux travaux pour chaque nouvelle vidéo, vous appliquez cette formule à toutes les vidéos de votre bibliothèque. Une formule dans Media Services est référencée comme une **transformation**. Pour plus d’informations, consultez [Transformations et travaux](transform-concept.md). L’exemple décrit dans ce didacticiel définit une recette qui analyse la vidéo spécifiée. 

#### <a name="transform"></a>Transformer

Lorsque vous créez une instance de **transformation**, vous devez spécifier ce qu’elle doit produire comme sortie. Le paramètre requis est un objet **TransformOutput**, comme indiqué dans le code ci-dessus. Chaque objet **TransformOutput** contient un **préréglage**. Le **préréglage** décrit les instructions détaillées concernant les opérations de traitement vidéo et/ou audio qui doivent être utilisées pour générer l’objet **TransformOutput** souhaité. Dans cet exemple, le préréglage **VideoAnalyzerPreset** est utilisé et la langue (« fr-fr ») est passée à son constructeur. Ce préréglage vous permet d’extraire plusieurs insights audio et vidéo à partir d’une vidéo. Vous pouvez utiliser le préréglage **AudioAnalyzerPreset** si vous avez besoin d’extraire plusieurs insights audio à partir d’une vidéo. 

Lorsque vous créez une **transformation**, vous devez tout d’abord vérifier s’il en existe déjà une à l’aide de la méthode **Get**, comme indiqué dans le code qui suit.  Dans Media Services v3, les méthodes **Get** appliquées sur les entités renvoient **null** si l’entité n’existe pas (une vérification du nom respectant la casse).

```csharp
private static Transform EnsureTransformExists(IAzureMediaServicesClient client, 
    string resourceGroupName, 
    string accountName, 
    string transformName, 
    Preset preset)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Start by defining the desired outputs.
        TransformOutput[] outputs = new TransformOutput[]
        {
            new TransformOutput(preset),
        };

        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs);
    }

    return transform;
}
```

#### <a name="job"></a>Travail

Comme indiqué ci-dessus, l’objet **Transformation** est la formule et un **travail** est la requête réelle envoyée à Media Services pour appliquer cette **transformation** à un contenu vidéo ou audio d’entrée donné. Le **travail** spécifie des informations telles que l’emplacement de la vidéo d’entrée et celui de la sortie. Vous pouvez spécifier l’emplacement de votre vidéo par : URL HTTPS, URL SAP ou des éléments qui se trouvent dans votre compte Media Services. 

Dans cet exemple, la vidéo d’entrée est téléchargée à partir d’une URL HTTPS spécifiée.  

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName, string jobName, string outputAssetName)
{
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });
            
    string uniqueJobName = jobName;
    Job job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

    if (job != null)
    {
        // Job already exists with the same name, so let's append a GUID
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        uniqueJobName += uniqueness;
    }

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAssetName),
    };

    job = client.Jobs.Create(
                resourceGroupName,
                accountName,
                transformName,
                uniqueJobName,
                new Job
                {
                    Input = jobInput,
                    Outputs = jobOutputs,
                });

    return job;
}
```

### <a name="wait-for-the-job-to-complete"></a>Attendre la fin du travail

Le travail prend du temps à se terminer et vous voulez être prévenu lorsque c’est le cas. Il existe différentes options pour être prévenu de l’achèvement du travail. L’option la plus simple (indiquée ici) est d’utiliser l’interrogation. 

L’interrogation n’est pas une meilleure pratique recommandée pour les applications de production en raison de la latence potentielle. L’interrogation peut être limitée si elle est utilisée de façon excessive sur un compte. À la place, les développeurs doivent utiliser Event Grid.

Event Grid est conçu pour une haute disponibilité, des performances cohérentes et une mise à l’échelle dynamique. Avec Event Grid, vos applications peuvent écouter les événements de presque tous les services Azure ou de toute source personnalisée, et y réagir. La gestion simple et réactive des événements basée sur HTTP vous aide à générer des solutions efficaces grâce au filtrage et au routage intelligents des événements. Consultez [Acheminer des événements Azure Media Services vers un point de terminaison personnalisé à l’aide de CLI](job-state-events-cli-how-to.md).

Le **travail** passe généralement par les états suivants : **Planifié**, **En attente**,  **Traitement en cours**, **Terminé** (l’état final). Si le travail a rencontré une erreur, vous obtenez l’état **Erreur**. Si le travail est en cours d’annulation, vous obtenez **Annulation en cours** et **Annulé** une fois l’opération terminée.

```csharp
private static Job WaitForJobToFinish(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName)
{
    int SleepInterval = 60 * 1000;

    Job job = null;

    while (true)
    {
        job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

        if (job.State == JobState.Finished || job.State == JobState.Error || job.State == JobState.Canceled)
        {
            break;
        }

        Console.WriteLine($"Job is {job.State}.");
        for (int i = 0; i < job.Outputs.Count; i++)
        {
            JobOutput output = job.Outputs[i];
            Console.Write($"\tJobOutput[{i}] is {output.State}.");
            if (output.State == JobState.Processing)
            {
                Console.Write($"  Progress: {output.Progress}");
            }
            Console.WriteLine();
        }
        System.Threading.Thread.Sleep(SleepInterval);
    }

    return job;
}
```

### <a name="download-the-result-of-the-job"></a>Télécharger le résultat du travail

 La fonction suivante télécharge les résultats à partir de l’élément multimédia de sortie dans le dossier « sortie », afin de pouvoir examiner les résultats du travail. 

```csharp
private static void DownloadResults(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string assetName,
    string resultsFolder)
{
    AssetContainerSas assetContainerSas = client.Assets.ListContainerSas(
            resourceGroup,
            accountName,
            assetName,
            permissions: AssetContainerPermission.Read,
            expiryTime: DateTime.UtcNow.AddHours(1).ToUniversalTime()
            );

    Uri containerSasUrl = new Uri(assetContainerSas.AssetContainerSasUrls.FirstOrDefault());
    CloudBlobContainer container = new CloudBlobContainer(containerSasUrl);

    string directory = Path.Combine(resultsFolder, assetName);
    Directory.CreateDirectory(directory);

    Console.WriteLine("Downloading results to {0}.", directory);

    foreach (IListBlobItem blobItem in container.ListBlobs(null, true, BlobListingDetails.None))
    {
        if (blobItem is CloudBlockBlob)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            string filename = Path.Combine(directory, blob.Name);

            blob.DownloadToFile(filename, FileMode.Create);
        }
    }

    Console.WriteLine("Download complete.");
}
```

### <a name="clean-up-resource-in-your-media-services-account"></a>Supprimer les ressources de votre compte Media Services

En règle générale, vous devez supprimer tous les éléments à l’exception des objets que vous envisagez de réutiliser (habituellement, vous allez réutiliser les transformations et conserver les éléments StreamingLocators, etc.). Si vous souhaitez que votre compte soit propre après avoir effectué vos expériences, vous devez supprimer les ressources que vous n’envisagez pas de réutiliser. Par exemple, le code suivant supprime les travaux.

```csharp
static void CleanUp(IAzureMediaServicesClient client,
        string resourceGroupName,
        string accountName, 
        string transformName)
{
    foreach (var job in client.Jobs.List(resourceGroupName, accountName, transformName))
    {
        client.Jobs.Delete(resourceGroupName, accountName, transformName, job.Name);
    }

    foreach (var asset in client.Assets.List(resourceGroupName, accountName))
    {
        client.Assets.Delete(resourceGroupName, accountName, asset.Name);
    }
}
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Appuyez sur Ctrl+F5 pour exécuter l’application *AnalyzeVideos*.

Lorsque nous exécutons le programme, le travail génère des miniatures pour chaque visage trouvé dans la vidéo. Il génère également le fichier insights.json.

## <a name="examine-the-output"></a>Analyser la sortie

Le fichier de sortie de l’analyse de vidéos est nommé insights.json. Ce fichier contient des insights sur votre vidéo. Vous trouverez une description des éléments trouvés au sein du fichier json dans l’article [Intelligence multimédia](intelligence-concept.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes de stockage et Media Services que vous avez créés pour ce didacticiel, supprimez le groupe de ressources créé précédemment. Vous pouvez utiliser l’outil **CloudShell**.

Dans **CloudShell**, exécutez la commande suivante :

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Les Kits de développement logiciel (SDK) Azure Media Services v3 ne sont pas thread-safe. Lorsque vous travaillez avec une application multithread, vous devez générer un nouvel objet AzureMediaServicesClient par thread.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel : charger, encoder et diffuser en continu des fichiers](stream-files-tutorial-with-api.md)
