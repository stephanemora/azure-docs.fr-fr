---
title: 'Démarrage rapide : Détecter des anomalies dans les données de séries chronologiques avec la bibliothèque de client Détecteur d’anomalies pour .NET'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment utiliser l’API Détecteur d’anomalies pour détecter les anomalies dans vos séries de données en lot ou en streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: fdb35edc35e07ed4ee718281942565a8f1d061d4
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402694"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Démarrage rapide : Bibliothèque de client Détecteur d’anomalies pour .NET

Commencez à utiliser la bibliothèque de client Détecteur d’anomalies pour .NET. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Le service Détecteur d’anomalies vous permet de rechercher des anomalies dans vos données de séries chronologiques en utilisant automatiquement les modèles les mieux adaptés sur celles-ci, quel que soit le secteur d’activité, le scénario ou le volume de données.

Utilisez la bibliothèque de client Détecteur d’anomalies pour .NET pour :

* Détecter les anomalies dans votre jeu de données de série chronologique, comme une requête de lots
* Détecter l’état d’anomalie du dernier point de données dans vos séries chronologiques

[Documentation de référence de la bibliothèque](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [Trouver le code sur GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Une clé et un point de terminaison de détecteur d’anomalie

## <a name="setting-up"></a>Configuration

### <a name="create-an-anomaly-detector-resource"></a>Créer une ressource Détecteur d’anomalies

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Créez une application .NET Core

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `anomaly-detector-quickstart`. Cette commande crée un projet « Hello World » simple avec un seul fichier source C# : *Program.cs*. 

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Vous pouvez générer l’application avec :

```dotnetcli
dotnet build
```

La sortie de génération ne doit contenir aucun avertissement ni erreur. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Dans le répertoire de l’application, installez la bibliothèque de client Détecteur d’anomalies pour .NET avec la commande suivante :

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

À partir du répertoire du projet, ouvrez le fichier *program.cs* et ajoutez ce qui suit en utilisant des `directives` :

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

Dans la méthode `main()` de l’application, créez des variables pour l’emplacement Azure de votre ressource, et votre clé en tant que variable d’environnement. Si vous avez créé la variable d’environnement une fois que l’application est lancée, l’éditeur, l’IDE ou le shell l’exécutant doit être fermé et rechargé pour accéder à la variable.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Modèle objet

Le client Détecteur d’anomalies est un objet [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) qui s’authentifie auprès d’Azure à l’aide de [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), qui contient votre clé. Le client propose deux méthodes de détection des anomalies : Sur un jeu de données entier à l’aide de [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync) et sur les derniers points de données à l’aide de [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Les données de séries chronologiques sont envoyées en tant que série de [points](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) dans un objet [Requête](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request). L’objet `Request` contient des propriétés pour décrire les données ([Granularité](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) par exemple) et des paramètres pour la détection d’anomalies. 

La réponse de Détecteur d’anomalies est un objet [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) ou [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse), selon la méthode utilisée. 

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les opérations suivantes avec la bibliothèque de client Détecteur d’anomalies pour .NET :

* [Authentifier le client](#authenticate-the-client)
* [Charger un jeu de données de séries chronologiques à partir d’un fichier](#load-time-series-data-from-a-file)
* [Détecter des anomalies dans un jeu de données entier](#detect-anomalies-in-the-entire-data-set) 
* [Détecter l’état d’anomalie du dernier point de données](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Authentifier le client

Dans une nouvelle méthode, instanciez un client avec votre point de terminaison et la clé. Créez un objet [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview). 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Charger des données de séries chronologiques à partir d’un fichier

Téléchargez l’exemple de données pour ce démarrage rapide à partir de [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) :
1. Dans votre navigateur, cliquez avec le bouton droit sur **Brutes**.
2. Cliquez sur **Enregistrer le lien sous**.
3. Enregistrez le fichier dans le répertoire de votre application en tant que fichier .csv.

Ces données de séries chronologiques sont mises en forme dans un fichier .csv et envoyées à l’API Détecteur d’anomalies.

Créez une méthode pour lire les données de séries chronologiques et ajoutez-la à un objet [Demande](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview). Appelez `File.ReadAllLines()` avec le chemin d’accès au fichier et créez une liste d’objets [Point](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) et enlevez les caractères de nouvelle ligne. Extrayez les valeurs et séparez la date de sa valeur numérique et ajoutez-les à un nouvel objet `Point`. 

Créez un objet `Request` avec la série de points, et `Granularity.Daily` pour la [granularité](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (ou la périodicité) des points de données.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Détecter des anomalies dans un jeu de données entier 

Créez une méthode pour appeler la méthode [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) du client avec l’objet `Request` et attendez la réponse en tant qu’objet [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview). Si la série chronologique contient des anomalies, itérez au sein des valeurs [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) de la réponse et imprimez celles qui sont définies sur `true`. Ces valeurs correspondent à l’index des points de données anormaux, le cas échéant.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter l’état d’anomalie du dernier point de données

Créez une méthode pour appeler la méthode [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) du client avec l’objet `Request` et attendez la réponse en tant qu’objet [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview). Vérifiez l’attribut [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) de la réponse pour déterminer si le dernier point de données envoyé est une anomalie ou non. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `dotnet run` à partir du répertoire de votre application.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
