---
title: Gérer des données de référence dans des environnements GA en C# – Azure Time Series Insights | Microsoft Docs
description: Découvrez comment gérer les données de référence de votre environnement GA en créant une application personnalisée écrite en C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: c8ce9a874ea81d3e48db1f46c13dece13dabbed6
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76544801"
---
# <a name="manage-ga-reference-data-for-an-azure-time-series-insights-environment-using-c"></a>Gérer des données de référence pour un environnement Azure Time Series Insights en C#

Cet article explique comment combiner C#, [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) et Azure Active Directory pour créer des demandes d’API programmatiques à l’[API Reference Data Management](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) d’Azure Time Series Insights en disponibilité générale.

## <a name="prerequisites"></a>Conditions préalables requises

Effectuez les étapes suivantes avant de compiler et d'exécuter l’exemple de code :

1. [Provisionner un environnement Azure Time Series Insights en disponibilité générale](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started
).

1. [Créer un jeu de données de référence](time-series-insights-add-reference-data-set.md) dans votre environnement. Utilisez le schéma de données de référence suivant :

   | Nom de clé | Type |
   | --- | --- |
   | uuid | String | 

1. Configurez votre environnement Azure Time Series Insights pour Azure Active Directory, comme décrit dans [Authentification et autorisation](time-series-insights-authentication-and-authorization.md). Utilisez `http://localhost:8080/` en tant qu’**URL de redirection**.

1. Installez les dépendances de projet nécessaires.

1. Modifiez l’exemple de code ci-dessous en remplaçant chaque espace réservé **#PLACEHOLDER#** par l’identificateur d’environnement qui convient.

1. Exécutez `dotnet run` dans le répertoire racine de votre projet. Lorsque vous y êtes invité, utilisez votre profil utilisateur pour vous connecter à Azure. 

> [!TIP]
> * Consultez les autres exemples de code C# en disponibilité générale sur [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Dépendances du projet

Il est recommandé d’utiliser la version la plus récente de Visual Studio et de **NETCore.app** :

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Version 16.4.2+
* [NETCore.app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) - Version 2.2.8

L’exemple de code comporte deux dépendances obligatoires :

* MSAL.NET [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) -  Package 4.7.1.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - Package 12.0.3

Ajoutez les packages à l’aide de [NuGet 2.12+](https://www.nuget.org/) :

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

Ou :

1. Déclarez un fichier `csharp-tsi-msal-ga-sample.csproj` :

    ```XML
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <LangVersion>latest</LangVersion>
        <RootNamespace>csharp-tsi-msal-ga-sample</RootNamespace>
        <RunWorkingDirectory>$(MSBuildThisFileDirectory)</RunWorkingDirectory>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Identity.Client" Version="4.7.1.0" Culture="neutral" PublicKeyToken="0a613f4dd989e8ae" />
        <PackageReference Include="Newtonsoft.Json" Version="12.0.3" />
      </ItemGroup>
    </Project>
    ```
1. Exécutez ensuite `dotnet restore`.

## <a name="c-sample-code"></a>Exemple de code C#

```csharp
// Copyright (c) Microsoft Corporation.  All rights reserved.

namespace CsharpTsiMsalGaSample
{
    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    public static class Program
    {
        /**
         * Review the product documentation for detailed configuration steps or skip ahead and configure your environment settings.
         * 
         * https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization
         */

        // Azure Time Series Insights environment configuration
        internal static string EnvironmentFqdn = "#PLACEHOLDER#.env.timeseries.azure.com";
        internal static string EnvironmentReferenceDataSetName = "#PLACEHOLDER#";

        // Azure Active Directory application configuration
        internal static string AadClientApplicationId = "#PLACEHOLDER#";
        internal static string[] AadScopes = new string[] { "https://api.timeseries.azure.com//user_impersonation" };
        internal static string AadRedirectUri = "http://localhost:8080/";
        internal static string AadAuthenticationAuthority = "https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/";

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (AadClientApplicationId == "#PLACEHOLDER#" || AadScopes.Length == 0 || AadRedirectUri == "#PLACEHOLDER#" || AadAuthenticationAuthority.StartsWith("#PLACEHOLDER#"))
            {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started"} to update the values of 'AadClientApplicationId', 'AadScopes', 'AadRedirectUri', and 'AadAuthenticationAuthority'.");
            }

            /**
             * MSAL.NET configuration. Review the product documentation for more information about MSAL.NET authentication options.
             * 
             * https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/
             */

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(AadClientApplicationId)
                .WithRedirectUri(AadRedirectUri)
                .WithAuthority(AadAuthenticationAuthority)
                .Build();

            AuthenticationResult result = await app
                .AcquireTokenInteractive(AadScopes)
                .ExecuteAsync();

            Console.WriteLine("MSAL Authentication Token Acquired: {0}", result.AccessToken);
            Console.WriteLine("");
            return result.AccessToken;
        }

        // System.Net.HttpClient helper to wrap HTTP POST made to the GA Reference Data API
        private static async Task<HttpResponseMessage> AsyncHttpPostRequestHelper(HttpClient httpClient, string input)
        {
             if (EnvironmentFqdn == "#PLACEHOLDER#" || EnvironmentReferenceDataSetName == "#PLACEHOLDER#")
             {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'EnvironmentFqdn' and 'EnvironmentReferenceDataSetName'.");
             }

             Console.WriteLine("HTTP JSON Request Body: {0}", input);
             Console.WriteLine("");
             HttpContent requestBody = new StringContent(input, Encoding.UTF8, "application/json");

             Uri uri = new UriBuilder("https", EnvironmentFqdn)
             {
                Path = $"referencedatasets/{EnvironmentReferenceDataSetName}/$batch",
                Query = "api-version=2016-12-12"
             }.Uri;
                
             Console.WriteLine("Making HTTP POST to URI: {0}", uri);
             Console.WriteLine("");

             HttpResponseMessage response = await httpClient.PostAsync(uri, requestBody);
             if (response.IsSuccessStatusCode)
             {
                var jsonString = await response.Content.ReadAsStringAsync();
                var jsonStringTransferObject = JsonConvert.DeserializeObject<object>(jsonString);
                Console.WriteLine("HTTP JSON Response Body: {0}", jsonStringTransferObject);
                Console.WriteLine("");
                return response;
             }

             return null;
        }

        private static async Task TsiMsalGaSample()
        {
            Console.WriteLine("Beginning demo...");
            Console.WriteLine("");
            Console.WriteLine("The following samples assume a single Key Name (uuid) with String type...");
            Console.WriteLine("");

            string accessToken = await AcquireAccessTokenAsync();
            var httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + accessToken);

            {   
                // CREATE reference data
                Console.WriteLine("CREATE reference data example...");
                Console.WriteLine("");
                string createInput = @"
                    {
                        ""put"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""floor"": 2
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";


                var createResponse = await AsyncHttpPostRequestHelper(httpClient, createInput);

                // READ reference data
                Console.WriteLine("READ reference data example...");
                Console.WriteLine("");
                string readInput = @"
                    {
                        ""get"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""desc"": ""example""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";

                var readResponse = await AsyncHttpPostRequestHelper(httpClient, readInput);

                // UPDATE reference data
                Console.WriteLine("UPDATE reference data example...");
                Console.WriteLine("");
                string updateInput = @"
                    {
                        ""patch"": [
                            {
                                ""uuid"": ""Fan1"",
                                ""color"": ""Red""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""desc"": ""Example""
                            }
                        ]
                    }";

                var inputResponse = await AsyncHttpPostRequestHelper(httpClient, updateInput);

                // DELETE reference data
                Console.WriteLine("DELETE reference data example...");
                Console.WriteLine("");
                string deleteInput = @"
                    {
                        ""delete"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""desc"": ""Blue""
                            }
                        ]
                    }";

                var deleteResponse = await AsyncHttpPostRequestHelper(httpClient, deleteInput);
            }
        }

        internal static void Main(string[] args)
        {
            Task.Run(async () => await TsiMsalGaSample()).Wait();
        }
    }
}
```

## <a name="summary"></a>Résumé

L’exemple de code ci-dessus illustre les fonctionnalités suivantes :

* Acquisition d’un jeton d’accès à l’aide de [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) **PublicClientApplication**
* Opérations séquentielles CREATE, READ, UPDATE et DELETE exécutées sur l’[API Reference Data Management](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) en disponibilité générale
* Codes de réponse courants, y compris les [codes d’erreur courants](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api#validation-and-error-handling)
    
    L’API Reference Data Management traite chaque élément individuellement. De fait, si une erreur se produit au niveau d’un élément, cela n’empêche pas les autres de se terminer correctement. Par exemple, si votre requête contient 100 éléments et que l’un d’eux contient une erreur, 99 éléments seront écrits et 1 élément sera rejeté.

## <a name="next-steps"></a>Étapes suivantes

- Consultez la documentation de référence sur l’[API Reference Data Management](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) en disponibilité générale.
