---
title: Bibliothèques de gestion Azure Service Bus | Microsoft Docs
description: Cet article explique comment utiliser les bibliothèques de gestion Azure Service Bus pour approvisionner dynamiquement des entités et des espaces de noms Service Bus.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 042edcd1851f86dd2a660673bc87884b68410bfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341707"
---
# <a name="service-bus-management-libraries"></a>Bibliothèques de gestion Service Bus

Les bibliothèques de gestion Azure Service Bus peuvent approvisionner dynamiquement des entités et des espaces de noms Service Bus. Cela permet des déploiements et des scénarios de messagerie complexes, et rend possible la définition des entités à approvisionner par programmation. Ces bibliothèques sont actuellement disponibles pour .NET.

## <a name="supported-functionality"></a>Fonctionnalités prises en charge

* Création, mise à jour et suppression d’espaces de noms
* Création, mise à jour et suppression de files d’attente
* Création, mise à jour et suppression de rubriques
* Création, mise à jour et suppression d’abonnements

## <a name="prerequisites"></a>Prérequis

Pour commencer à utiliser les bibliothèques de gestion Service Bus, vous devez vous authentifier auprès du service Azure Active Directory (Azure AD). Azure AD vous oblige à vous authentifier en tant que principal du service pour pouvoir accéder à vos ressources Azure. Pour plus d’informations sur la création d’un principal du service, consultez ces articles :  

* [Utiliser le portail Azure pour créer une application et un principal du service Active Directory pouvant accéder aux ressources](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Créer un principal du service pour accéder aux ressources à l’aide de l’interface de ligne de commande (CLI) Azure](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Ces didacticiels vous fournissent un `AppId` (ID de client), un `TenantId` et un `ClientSecret` (clé d’authentification), tous étant utilisés pour l’authentification par les bibliothèques de gestion. Au minimum, vous devez des autorisations [**Propriétaire de données Azure Service Bus**](/azure/role-based-access-control/built-in-roles#azure-service-bus-data-owner) ou [**Contributeur**](/azure/role-based-access-control/built-in-roles#contributor) pour le groupe de ressources sur lequel vous souhaitez effectuer l’exécution.

## <a name="programming-pattern"></a>Modèle de programmation

Le modèle pour manipuler une ressource Service Bus quelconque suit un protocole commun :

1. Obtenez un jeton d’Azure AD à l’aide de la bibliothèque **Microsoft.IdentityModel.Clients.ActiveDirectory** :
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Créez l’objet `ServiceBusManagementClient`:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Définissez les paramètres `CreateOrUpdate` sur vos valeurs spécifiées :

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Exécutez l’appel :

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Code complet pour créer une file d’attente
Voici le code complet pour créer une file d’attente Service Bus : 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Pour obtenir un exemple complet, consultez l’[exemple de gestion .NET sur GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Étapes suivantes
[Informations de référence sur l’API Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
