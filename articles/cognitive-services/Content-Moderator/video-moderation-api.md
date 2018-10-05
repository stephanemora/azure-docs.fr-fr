---
title: Modération de vidéo - Content Moderator
titlesuffix: Azure Cognitive Services
description: Recourez à une modération des vidéos afin de détecter la présence éventuelle de contenu pour adultes ou inconvenant.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: 9b6240ef0883e1a523c50c9b641065deb2669d53
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226533"
---
# <a name="video-moderation"></a>Modération de vidéo

Aujourd’hui, les visionneuses en ligne génèrent des milliards de vues de vidéos sur des sites web de réseaux sociaux populaires et régionaux, et le phénomène ne cesse de croître. En recourant à des services basés sur l’apprentissage automatique pour prédire la présence potentielle le contenu pour adultes ou inconvenant, vous réduisez le coût de vos efforts de modération.

## <a name="sign-up-for-the-content-moderator-media-processor-preview"></a>S’inscrire pour accéder au processeur multimédia Content Moderator (préversion)

### <a name="create-a-free-azure-account"></a>Créer un compte Azure gratuit

[Commencez ici](https://azure.microsoft.com/free/) pour créer un compte Azure gratuit si vous n’en avez pas.

### <a name="create-an-azure-media-services-account"></a>Créer un compte Azure Media Services

Les fonctionnalités vidéo de Content Moderator sont disponibles gratuitement en tant que **processeur multimédia** en préversion publique dans Azure Media Services (AMS).

[Créez un compte Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) dans votre abonnement Azure.

### <a name="get-azure-active-directory-credentials"></a>Obtenir les informations d’identification Azure Active Directory

   1. Pour apprendre à utiliser le portail Azure afin d’obtenir vos informations d’identification pour l’authentification auprès d’Azure AD, lisez l’[article du portail Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).
   1. Pour apprendre à utiliser vos informations d’identification Azure Active Directory avec le Kit de développement logiciel (SDK) .NET, lisez l’[article sur Azure Media Services avec .NET](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad).

   > [!NOTE]
   > L’exemple de code dans ce démarrage rapide utilise la méthode d’**authentification du principal du service** décrite dans les deux articles.

Une fois que vous disposez de vos informations d’identification AMS, vous pouvez essayer le processeur multimédia Content Moderator de deux façons.

## <a name="use-azure-media-services-explorer"></a>Utiliser Azure Media Services Explorer

Utilisez l’[explorateur Azure Media Services (AMS)](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) interactif pour rechercher votre compte AMS, charger des vidéos et analyser celles-ci avec le processeur multimédia Content Moderator. [Téléchargez-le et installez-le](https://github.com/Azure/Azure-Media-Services-Explorer/releases) à partir de GitHub, puis [recherchez le code source](http://github.com/Azure/Azure-Media-Services-Explorer) à explorer à l’aide du Kit de développement logiciel (SDK) AMS.

![Explorateur Azure Media Services avec Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>Démarrage rapide de .NET avec Visual Studio et C#

1. Ajoutez un nouveau projet **Console app (.NET Framework)** à votre solution.

   Dans l’exemple de code, nommez le projet **VideoModeration**.

1. Sélectionnez ce projet comme unique projet de démarrage de la solution.

### <a name="install-required-packages"></a>Installer les packages nécessaires

Installez les packages NuGet suivants disponibles sur [NuGet](https://www.nuget.org/).

- windowsazure.mediaservices
- windowsazure.mediaservices.extensions

### <a name="update-the-programs-using-statements"></a>Mettre à jour les instructions d’utilisation du programme

Mettez à jour les instructions using du programme.

    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.IO;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using System.Collections.Generic;


### <a name="initialize-application-specific-settings"></a>Initialiser les paramètres spécifiques de l’application

Ajoutez les champs statiques suivants à la classe **Program** dans Program.cs.

    // declare constants and globals
    private static CloudMediaContext _context = null;
    private static CloudStorageAccount _StorageAccount = null;

    // Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
    // a list of Blobs to be processed.
    static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
    static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
    static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

    private static StorageCredentials _StorageCredentials = null;

    // Azure Media Services authentication. See the quickstart for how to get these.
    private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
    private const string CLIENT_ID = "YOUR CLIENT ID"
    private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

    // REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
    private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

    // Content Moderator Media Processor Nam
    private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

    // Input and Output files in the current directory of the executable
    private const string INPUT_FILE = "VIDEO FILE NAME";
    private const string OUTPUT_FOLDER = "";

### <a name="create-a-preset-file-json"></a>Créer un fichier de présélections (JSON)

Créez dans le répertoire actif un fichier JSON contenant le numéro de version.

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>Ajoutez le code suivant à la méthode main.

La méthode main crée un contexte Azure Media, puis un contexte Stockage Azure au cas où vos vidéos se trouveraient dans un stockage d’objets blob.
Le code restant analyse une vidéo à partir d’un dossier local, ou d’un ou plusieurs objets blob dans un conteneur de stockage Azure.
Vous pouvez essayer toutes les options en commentant les autres lignes de code.

    // Create Azure Media Context
    CreateMediaContext();

    // Create Storage Context
    CreateStorageContext();

    // Use a file as the input.
    // IAsset asset = CreateAssetfromFile();
    
    // -- OR ---
    
    // Or a blob as the input
    // IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

    // Then submit the asset to Content Moderator
    // RunContentModeratorJob(asset);

    //-- OR ----

    // Just run the content moderator on all blobs in a list (from a Blob Container)
    RunContentModeratorJobOnBlobs();

### <a name="add-the-code-to-create-an-azure-media-context"></a>Ajouter le code pour créer un contexte Azure Media

    /// <summary>
    /// Creates a media context from azure credentials
    /// </summary>
    static void CreateMediaContext()
    {
        // Get Azure AD credentials
        var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
            new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
            AzureEnvironments.AzureCloudEnvironment);

        // Initialize an Azure AD token
        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        // Create a media context
        _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
    }

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Ajouter le code pour créer un contexte Stockage Azure
Le contexte Stockage créé à partir de vos informations d’identification de Stockage vous permet d’accéder à votre stockage d’objets blob.

    /// <summary>
    /// Creates a storage context from the AMS associated storage name and key
    /// </summary>
    static void CreateStorageContext()
    {
        // Get a reference to the storage account associated with a Media Services account. 
        if (_StorageCredentials == null)
        {
            _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
        }
        _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
    }

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Ajouter le code pour créer des actifs multimédias Azure à partir d’un fichier local et d’un objet blob
Le processeur multimédia Content Moderator exécute des travaux sur des **Actifs** au sein de la plateforme Azure Media Services.
Ces méthodes créent les Actifs à partir d’un fichier local ou d’un objet blob associé.

    /// <summary>
    /// Creates an Azure Media Services Asset from the video file
    /// </summary>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromFile()
    {
        return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
    }

    /// <summary>
    /// Creates an Azure Media Services asset from your blog storage
    /// </summary>
    /// <param name="Blob"></param>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
    {
        // Create asset from the FIRST blob in the list and return it
        return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
    }

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Ajouter le code pour analyser une collection de vidéos (en tant qu’objets blob) au sein d’un conteneur

    /// <summary>
    /// Runs the Content Moderator Job on all Blobs in a given container name
    /// </summary>
    static void RunContentModeratorJobOnBlobs()
    {
        // Get the reference to the list of Blobs. See the following method.
        var blobList = GetBlobsList();

        // Iterate over the Blob list items or work on specific ones as needed
        foreach (var sourceBlob in blobList)
        {
            // Create an Asset
            IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                               _StorageCredentials, AssetCreationOptions.None);
            asset.Update();

            // Submit to Content Moderator
            RunContentModeratorJob(asset);
        }
    }

    /// <summary>
    /// Get all blobs in your container
    /// </summary>
    /// <returns></returns>
    static IEnumerable<IListBlobItem> GetBlobsList()
    {
        // Get a reference to the Container within the Storage Account
        // that has the files (blobs) for moderation
        CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
        CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

        // Get the reference to the list of Blobs 
        var blobList = MediaBlobContainer.ListBlobs();
        return blobList;
    }

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Ajouter la méthode pour exécuter le travail de Content Moderator

    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

### <a name="add-a-couple-of-helper-functions"></a>Ajoutez quelques fonctions d’assistance

Ces méthodes téléchargent le fichier de sortie (JSON) de Content Moderator à partir de l’actif Azure Media Services, et facilitent le suivi de l’état du travail de modération afin que le programme puisse journaliser un état d’exécution sur la console.

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    // event handler for Job State
    static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job finished.");
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
                Console.WriteLine("Job is canceled.\n");
                break;
            case JobState.Error:
                Console.WriteLine("Job failed.\n");
                break;
            default:
                break;
        }
    }

### <a name="run-the-program-and-review-the-output"></a>Exécuter le programme et examiner la sortie

Une fois le travail de modération du contenu accompli, analysez la réponse JSON. Elle se compose des éléments suivants :

- Résumé des informations sur la vidéo
- **Captures** en tant que « **fragments** »
- **Images clés** en tant qu’« **événements** » avec un indicateur **reviewRecommended" (= true or false)"** basé sur les scores **Adult** et **Racy**
- **start**, **duration**, **totalDuration** et **timestamp** sont exprimés en « tics ». Divisez par la valeur **timestamp** pour obtenir le nombre de secondes.
 
> [!NOTE]

> - `adultScore` représente la présence potentielle et le score de prédiction du contenu pouvant être considéré comme sexuellement explicite ou pour adultes dans certaines situations.
> - `racyScore` représente la présence potentielle et le score de prédiction du contenu pouvant être considéré comme sexuellement suggestif ou pour adultes dans certaines situations.
> - `adultScore` et `racyScore` ont des valeurs de 0 à 1. Plus le score est élevé, plus le modèle prédit que la catégorie peut être applicable. Cette préversion s’appuie sur un modèle statistique plutôt que sur des résultats codés manuellement. Nous vous recommandons d’effectuer le test avec votre propre contenu afin de déterminer comment chaque catégorie correspond à vos besoins.
> - La valeur de `reviewRecommended` est true ou false, selon les seuils de score internes. Les clients doivent évaluer l’opportunité d’utiliser cette valeur ou choisir des seuils personnalisés en fonction de leurs stratégies de contenu.
>

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment générer des [analyses vidéo](video-reviews-quickstart-dotnet.md) à partir du résultat de votre modération.

Ajoutez une [modération des transcriptions](video-transcript-moderation-review-tutorial-dotnet.md) à vos analyses vidéo.

Consultez le didacticiel détaillé sur la création d’une [solution de modération complète des vidéos et transcriptions](video-transcript-moderation-review-tutorial-dotnet.md).

[Téléchargez la solution Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pour ce démarrage rapide de Content Moderator et d’autres démarrages rapides pour .NET.
