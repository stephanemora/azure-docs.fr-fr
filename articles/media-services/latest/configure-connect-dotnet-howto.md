---
title: Se connecter à Azure Media Services v3 API - .NET
description: Découvrez comment vous connecter à Media Services v3 API avec .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 9fad5874a0e595ee2c275f06504665ce143266f6
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759580"
---
# <a name="connect-to-media-services-v3-api---net"></a>Se connecter à l’API de Media Services v3 - .NET

Cet article vous montre comment se connecter au SDK .NET Azure Media Services v3 à l’aide de la méthode de connexion du principal de service.

## <a name="prerequisites"></a>Conditions préalables

- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom de groupe de ressources et le nom du compte Media Services
- Installer un outil que vous souhaitez utiliser pour le développement .NET. Les étapes décrites dans cet article montrent comment utiliser [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Vous pouvez utiliser Visual Studio Code, consultez [utilisation C# ](https://code.visualstudio.com/docs/languages/csharp). Ou bien, vous pouvez utiliser un autre éditeur de code.

## <a name="create-a-console-application"></a>Création d’une application console

1. Démarrez Visual Studio. 
1. À partir de la **fichier** menu, cliquez sur **New** > **projet**. 
1. Créer un **.NET Core** application console.

Cible de l’exemple d’application dans cette rubrique, `netcoreapp2.0`. Le code utilise « Async principal », qui est disponible à partir de C# 7.1. Consultez ce [blog](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) pour plus d’informations.

## <a name="add-required-nuget-packages"></a>Ajouter des packages NuGet requis

1. Dans Visual Studio, sélectionnez **outils** > **Gestionnaire de Package NuGet** > **Console du gestionnaire NuGet**.
2. Dans le **Console du Gestionnaire de Package** fenêtre, utilisez `Install-Package` commande pour ajouter les packages NuGet suivants. Par exemple : `Install-Package Microsoft.Azure.Management.Media`.

|Package|Description|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Pour vous assurer que vous utilisez le dernier package Azure Media Services, vérifiez [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Bibliothèque d’authentification ADAL pour Azure SDK pour NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Lire les valeurs de configuration à partir de variables d’environnement et des fichiers JSON locaux|
|`Microsoft.Extensions.Configuration.Json`|Lire les valeurs de configuration à partir de variables d’environnement et des fichiers JSON locaux
|`WindowsAzure.Storage`|Kit de développement logiciel de stockage|

## <a name="create-and-configure-the-app-settings-file"></a>Créer et configurer le fichier de paramètres d’application

### <a name="create-appsettingsjson"></a>Créer appsettings.json

1. Accédez go **général** > **fichier texte**.
1. Nommez-le « appsettings.json ».
1. Définissez la propriété « Répertoire de sortie » du fichier .json à « Copier si plus récent » (pour que l’application puisse y accéder lors de la publication).

### <a name="set-values-in-appsettingsjson"></a>Valeurs définies dans le fichier appsettings.json

Exécutez le `az ams account sp create` commande comme décrit dans [accéder aux API](access-api-cli-how-to.md). La commande retourne json que vous devez copier dans votre « appsettings.json ».
 
## <a name="add-configuration-file"></a>Ajouter un fichier de configuration

Pour plus de commodité, ajoutez un fichier de configuration qui est responsable de lire les valeurs à partir de « appsettings.json ».

1. Ajouter une nouvelle classe .cs à votre projet. Nommez-le `ConfigWrapper`. 
1. Collez le code suivant dans ce fichier (cet exemple suppose que l’espace de noms est `ConsoleApp1`).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Se connecter au client .NET

Pour commencer à utiliser les API Media Services avec .NET, vous devez créer un objet **AzureMediaServicesClient**. Pour créer l’objet, vous devez fournir les informations d’identification nécessaires pour que le client puisse se connecter à Azure à l’aide d’Azure AD. Dans le code ci-dessous, la fonction GetCredentialsAsync crée l’objet ServiceClientCredentials selon les informations d’identification fournies dans le fichier de configuration local.

1. Ouvrez `Program.cs`.
1. Collez le code suivant :

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use UserTokenProvider.LoginWithPromptAsync or UserTokenProvider.LoginSilentAsync to get a token using user authentication
            //// ActiveDirectoryClientSettings.UsePromptOnly
            //// UserTokenProvider.LoginWithPromptAsync

            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="see-also"></a>Voir aussi

- [Tutoriel : Charger, encoder et diffuser des vidéos en continu - .NET](stream-files-tutorial-with-api.md) 
- [Tutoriel : Diffuser en direct avec Media Services v3 - .NET](stream-live-tutorial-with-api.md)
- [Tutoriel : Analyser des vidéos avec Media Services v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Créer une entrée de travail à partir d’un fichier local - .NET](job-input-from-local-file-how-to.md)
- [Créer une entrée de travail à partir d’une URL HTTPS - .NET](job-input-from-http-how-to.md)
- [Encoder avec une transformation personnalisée - .NET](customize-encoder-presets-how-to.md)
- [Utiliser le chiffrement dynamique AES-128 et le service de distribution de clés - .NET](protect-with-aes128.md)
- [Utilisation du chiffrement dynamique DRM et du service de remise des licences - .NET](protect-with-drm.md)
- [Obtenir une clé de signature à partir de la stratégie existante - .NET](get-content-key-policy-dotnet-howto.md)
- [Créer des filtres avec Media Services - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemples avancés de vidéo à la demande d’Azure Functions v2 avec Media Services v3](https://aka.ms/ams3functions)

## <a name="next-steps"></a>Étapes suivantes

[Information de référence sur .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
