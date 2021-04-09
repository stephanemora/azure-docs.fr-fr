---
title: Création programmatique d’entités Azure Service Bus | Microsoft Docs
description: Cet article explique comment utiliser dynamiquement ou provisionner programmatiquement des espaces de noms et des entités Service Bus.
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 57192ab2ee1624cb18de832ac91c95290da727df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539873"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Provisionnement dynamique d’espaces de noms et d’entités Service Bus 
Les bibliothèques de gestion Azure Service Bus peuvent approvisionner dynamiquement des entités et des espaces de noms Service Bus. Cela permet des déploiements et des scénarios de messagerie complexes, et rend possible la définition des entités à approvisionner par programmation. Ces bibliothèques sont actuellement disponibles pour .NET.

## <a name="overview"></a>Vue d'ensemble
Trois bibliothèques de gestion sont disponibles pour créer et gérer des entités Service Bus. Il s'agit des éléments suivants :

- [Azure.Messaging.ServiceBus.Administration](#azuremessagingservicebusadministration)
- [Microsoft.Azure.ServiceBus.Management](#microsoftazureservicebusmanagement)
- [Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus)

Tous ces packages prennent en charge les opérations de création, d’extraction, d’affichage, de suppression et de mise à jour sur les **files d’attente, rubriques et abonnements**. Cependant, seul [Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus) gère ces mêmes opérations sur les **espaces de noms**, l’affichage et la régénération des clés SAS, etc. 

La bibliothèque Microsoft.Azure.Management.ServiceBus fonctionne uniquement avec l’authentification Azure Active Directory (Azure AD). Elle ne prend pas en charge les chaînes de connexion pour l’authentification auprès du service, contrairement aux deux autres bibliothèques (Azure.Messaging.ServiceBus et Microsoft.Azure.ServiceBus). Ces dernières sont aussi plus faciles à utiliser. De ces bibliothèques, Azure.Messaging.ServiceBus est la plus récente et celle que nous recommandons d’utiliser.

Les sections suivantes décrivent plus en détail ces bibliothèques. 

## <a name="azuremessagingservicebusadministration"></a>Azure.Messaging.ServiceBus.Administration
Vous pouvez utiliser la classe [ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) de l’espace de noms [Azure.Messaging.ServiceBus.Administration](/dotnet/api/azure.messaging.servicebus.administration) pour gérer les espaces de noms, les files d’attente, les rubriques et les abonnements. Voici l’exemple de code. Pour un exemple complet, consultez [Exemple CRUD](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs).

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Microsoft.Azure.ServiceBus.Management 
Vous pouvez utiliser la classe [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) de l’espace de noms [Microsoft.Azure.ServiceBus.Management](/dotnet/api/microsoft.azure.servicebus.management) pour gérer les espaces de noms, les files d’attente, les rubriques et les abonnements. Voici l’exemple de code : 

> [!NOTE]
> Nous vous recommandons d’utiliser la classe `ServiceBusAdministrationClient` de la bibliothèque `Azure.Messaging.ServiceBus.Administration`, qui correspond au dernier kit de développement logiciel (SDK) en date. Pour plus d’informations, consultez la [première section](#azuremessagingservicebusadministration). 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
Cette bibliothèque fait partie du kit SDK de plan de contrôle basé sur Azure Resource Manager. 

### <a name="prerequisites"></a>Prérequis

Pour pouvoir utiliser cette bibliothèque, vous devez vous authentifier auprès du service Azure Active Directory (Azure AD). Azure AD vous oblige à vous authentifier en tant que principal du service pour pouvoir accéder à vos ressources Azure. Pour plus d’informations sur la création d’un principal du service, consultez ces articles :  

* [Utiliser le portail Azure pour créer une application et un principal du service Active Directory pouvant accéder aux ressources](../active-directory/develop/howto-create-service-principal-portal.md)
* [Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Créer un principal du service pour accéder aux ressources à l’aide de l’interface de ligne de commande (CLI) Azure](/cli/azure/create-an-azure-service-principal-azure-cli)

Ces didacticiels vous fournissent un `AppId` (ID de client), un `TenantId` et un `ClientSecret` (clé d’authentification), tous étant utilisés pour l’authentification par les bibliothèques de gestion. Au minimum, vous devez des autorisations [**Propriétaire de données Azure Service Bus**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) ou [**Contributeur**](../role-based-access-control/built-in-roles.md#contributor) pour le groupe de ressources sur lequel vous souhaitez effectuer l’exécution.

### <a name="programming-pattern"></a>Modèle de programmation

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

### <a name="complete-code-to-create-a-queue"></a>Code complet pour créer une file d’attente
Voici l’exemple de code permettant de créer une file d’attente Service Bus. Pour obtenir un exemple complet, consultez l’[exemple de gestion .NET sur GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

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

## <a name="fluent-library"></a>Bibliothèque Fluent
Pour un exemple de gestion d’entités Service Bus avec la bibliothèque Fluent, consultez [cet exemple](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus). 

## <a name="next-steps"></a>Étapes suivantes
Consultez les rubriques de référence suivantes : 

- [Azure.Messaging.ServiceBus.Administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Microsoft.Azure.ServiceBus.Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)