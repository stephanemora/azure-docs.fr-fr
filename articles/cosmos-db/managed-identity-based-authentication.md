---
title: Comment utiliser une identité managée de machine virtuelle Linux attribuée par le système pour accéder aux données Azure Cosmos DB
description: Découvrez comment configurer une identité managée attribuée par le système Azure AD pour accéder à des clés à partir d’Azure Cosmos DB. msi, managed service identity, aad, azure active directory, identité
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 102efba5d028eef621f392ef1739ea9ebeca0b44
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80416974"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>Comment utiliser une identité managée de machine virtuelle Linux attribuée par le système pour accéder aux données Azure Cosmos DB

Dans cet article, vous allez configurer une **solution robuste et indépendante de rotation des clés** pour accéder aux clés d’Azure Cosmos DB en tirant parti des [identités managées](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). L’exemple de cet article utilise une fonction Azure. Toutefois, vous pouvez obtenir cette solution à l’aide de n’importe quel service prenant en charge les identités managées. 

Vous allez apprendre à créer une fonction Azure qui peut accéder à Azure Cosmos DB sans avoir à copier les clés d’Azure Cosmos DB. La fonction s’active chaque minute et enregistre la température actuelle d’un réservoir de poisson d’aquarium. Pour savoir comment configurer une fonction Azure déclenchée par un minuteur, consultez l’article [Créer une fonction dans Azure déclenchée par un minuteur](../azure-functions/functions-create-scheduled-function.md).

Pour simplifier le scénario, le nettoyage des documents de température plus anciens est géré par un paramètre [Durée de vie](./time-to-live.md) déjà configuré. 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Attribuer une identité managée attribuée par le système à une fonction Azure

Dans cette étape, vous allez attribuer une identité managée attribuée par le système à votre fonction Azure.

1. Dans le [Portail Azure](https://portal.azure.com/), ouvrez le volet **Fonction Azure** et accédez à votre application de fonction. 

1. Ouvrez l’onglet **Identité** >  **des fonctionnalités de plateforme** : 

   ![Onglet Identité](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Sous l’onglet **Identité**, **activez** l’état **Identité système**. Veillez à sélectionner **Enregistrer** et confirmez que vous souhaitez activer l’identité système. À la fin, le volet **Identité du système** doit se présenter comme suit :  

   ![Identité système activée](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>Autoriser l’identité managée à accéder à votre compte Azure Cosmos

Dans cette étape, vous allez attribuer un rôle à l’identité managée attribuée par le système de votre fonction Azure. Azure Cosmos DB a plusieurs rôles intégrés que vous pouvez attribuer à l’identité managée. Pour cette solution, vous allez utiliser les deux rôles suivants :

|Rôle intégré  |Description  |
|---------|---------|
|[Contributeur de compte DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Gérer des comptes Azure Cosmos DB. Autorise la récupération des clés en lecture/écriture. |
|[Lecteur de compte Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Lire les données de comptes Azure Cosmos DB. Autorise la récupération des clés de lecture. |

> [!IMPORTANT]
> La prise en charge du RBAC dans Azure Cosmos DB s’applique uniquement aux opérations de plan de contrôle. Les opérations du plan de données sont sécurisées à l’aide de clés principales ou de jetons de ressources. Pour en savoir plus, consultez l’article [Sécuriser l’accès aux données](secure-access-to-data.md).

> [!TIP] 
> Lorsque vous attribuez des rôles, attribuez uniquement l’accès nécessaire. Si votre service nécessite uniquement la lecture des données, affectez l’identité managée au rôle **Lecteur de compte Cosmos DB**. Pour plus d’informations sur l’importance de l’accès au moindre privilège, consultez l’article [Exposition réduite des comptes privilégiés](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts).

Pour votre scénario, vous allez lire la température, puis écrire ces données dans un conteneur dans Azure Cosmos DB. Étant donné que vous devez écrire les données, vous allez utiliser le rôle **Contributeur de comptes DocumentDB**. 

1. Connectez-vous au Portail Azure et accédez à votre compte Azure Cosmos DB. Ouvrez le **Volet de gestion des accès (IAM)** , puis l’onglet **Attributions de rôle** :

   ![Volet IAM](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Sélectionnez le bouton **+ Ajouter**, puis **ajouter une attribution de rôle**.

1. Le volet **Ajouter une attribution de rôle** s’ouvre à droite :

   ![Ajouter un rôle](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Rôle** - Sélectionnez **Contributeur de compte DocumentDB**
   * **Attribuer l’accès à** - Sous **Identité managée affectée par le système**, sélectionnez **Application de fonction**.
   * **Sélection** - Le volet sera rempli avec toutes les applications de fonction, dans votre abonnement, qui ont une **Identité de système managé**. Dans le cas présent, je sélectionne l’application de fonction **SummaryService** : 

      ![Sélectionner une attribution](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Une fois l’identité de l’application de fonction sélectionnée, cliquez sur **Enregistrer**.

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Accéder par programme aux clés d’Azure Cosmos DB à partir de la fonction Azure

Nous avons maintenant une application de fonction qui a une identité managée affectée par le système. Cette identité se voit attribuer le rôle **Contributeur de comptes DocumentDB** dans les autorisations d’Azure Cosmos DB. Le code d’application de fonction suivant obtient les clés d’Azure Cosmos DB, crée un objet CosmosClient, obtient la température, puis enregistre cela dans Cosmos DB.

Cet exemple utilise l’[API Répertorier les clés](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) pour accéder à vos clés de compte Azure Cosmos DB.

> [!IMPORTANT] 
> Si vous voulez [attribuer le rôle **Lecteur de compte Cosmos DB**](#grant-the-managed-identity-access-to-your-azure-cosmos-account), vous allez devoir utilizer l’[API Répertorier les clés](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys) en lecture seule. Seules les clés en lecture seule seront remplies.

L’API Répertorier les clés retourne l’objet `DatabaseAccountListKeysResult`. Ce type n’est pas défini dans les bibliothèques C#. Le code suivant montre l'implémentation de cette classe :  

```csharp 
namespace SummarizationService 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

L’exemple utilise également un document simple appelé « TemperatureRecord », qui est défini comme suit :

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Vous allez utiliser la bibliothèque [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) pour obtenir le jeton d’identité managée attribué par le système. Pour en savoir plus sur les autres façons d’obtenir le jeton et d’autres informations sur la bibliothèque `Microsoft.Azure.Service.AppAuthentication`, consultez l’article [Authentification de service à service](../key-vault/service-to-service-authentication.md).

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class TemperatureMonitor
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Vous êtes maintenant prêt à [déployer votre fonction Azure](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Étapes suivantes

* [Authentification basée sur les certificats avec Azure Cosmos DB et Azure Active Directory](certificate-based-authentication.md)
* [Sécuriser les clés Azure Cosmos à l’aide d’Azure Key Vault](access-secrets-from-keyvault.md)
* [Base de référence de sécurité pour Azure Cosmos DB](security-baseline.md)
