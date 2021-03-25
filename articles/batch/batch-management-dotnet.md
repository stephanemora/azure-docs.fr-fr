---
title: Utiliser la bibliothèque Batch Management .NET. pour gérer des ressources de compte
description: Créez, supprimez et modifiez des ressources de compte Azure Batch avec la bibliothèque Batch Management .NET.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896729"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gérer les quotas et comptes Batch avec la bibliothèque cliente Batch Management pour .NET

Vous pouvez réduire la surcharge de maintenance dans vos applications Azure Batch avec la bibliothèque [Batch Management .NET](/dotnet/api/overview/azure/batch) en automatisant la création et la suppression de comptes Batch, la gestion des clés et la détection des quotas.

- **Créez et supprimez des comptes Batch** dans n'importe quelle région. Si, par exemple, en tant qu'éditeur de logiciels indépendant (ISV) vous fournissez un service à vos clients dans lequel un compte Batch distinct est affecté à chacun à des fins de facturation, vous pouvez ajouter des fonctionnalités de création et de suppression de comptes à votre portail client.
- **Récupérez et régénérez des clés de compte** par programme pour tous vos comptes Batch. Cela est pratique pour maintenir la conformité aux stratégies de sécurité appliquant une substitution ou une expiration périodique des clés de compte. Lorsque vous avez plusieurs comptes Batch dans différentes régions Azure, l’automatisation de ce processus de substitution augmente l’efficacité de votre solution.
- **Vérifiez les quotas des comptes** et déterminez quels comptes Batch possèdent quelles limites sans avoir à tâtonner. En vérifiant les quotas de vos comptes avant de commencer les travaux, de créer des pools ou d’ajouter des nœuds de calcul, vous pouvez ajuster de façon proactive l’endroit et le moment où ces ressources de calcul sont créées. Vous pouvez déterminer quels comptes requièrent des augmentations de quota avant d’allouer des ressources supplémentaires à ces comptes.
- **Combinez les fonctionnalités d’autres services Azure** pour une expérience de management complète en utilisant conjointement Batch Management .NET, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) et [Azure Resource Manager](../azure-resource-manager/management/overview.md) dans la même application. En utilisant ces fonctionnalités et leurs API, vous pouvez fournir une expérience d’authentification transparente, une fonction de création et de suppression des groupes de ressources ainsi que les fonctionnalités décrites ci-dessus, pour une solution de gestion de bout en bout.

> [!NOTE]
> Cet article se concentre sur la gestion programmatique de vos comptes, clés et quotas Batch, mais vous pouvez également effectuer la plupart de ces activités dans le [portail Azure](batch-account-create-portal.md).

## <a name="create-and-delete-batch-accounts"></a>Créez et supprimez des comptes Batch

L’une des principales fonctionnalités de l’API Batch Management consiste à créer et à supprimer des [comptes Batch](accounts.md) dans une région Azure. Pour ce faire, utilisez [BatchManagementClient.Account.CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) et [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync), ou leurs équivalents synchrones.

L’extrait de code suivant crée un compte, récupère le compte créé à partir du service Batch, puis le supprime. Dans cet extrait de code et les autres extraits de cet article, `batchManagementClient` est une instance entièrement initialisée de [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient).

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Les applications qui utilisent la bibliothèque Batch Management .NET et sa classe BatchManagementClient nécessitent un accès administrateur de services fédérés ou coadministrateur à l’abonnement qui possède le compte Batch à gérer. Pour plus d’informations, consultez la section Azure Active Directory et l’exemple de code [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement).

## <a name="retrieve-and-regenerate-account-keys"></a>Récupérez et régénérez des clés de compte

Récupérez les clés primaires et secondaires à partir de n’importe quel compte Batch de votre abonnement avec [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Vous pouvez régénérer ces clés à l’aide de [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync).

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Vous pouvez créer un flux de travail de connexion rationalisé pour vos applications de gestion. Commencez par récupérer une clé du compte Batch que vous souhaitez gérer avec [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Utilisez ensuite cette clé lors de l’initialisation de la classe [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) de la bibliothèque Batch .NET, qui est utilisée lors de l’initialisation de [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Vérifier les quotas d'un abonnement Azure et d'un compte Batch

Les abonnements Azure et les services Azure, comme Batch, ont tous des quotas par défaut qui limitent le nombre de certaines entités. Pour connaître les quotas par défaut des abonnements Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-resource-manager/management/azure-subscription-service-limits.md). Pour obtenir les quotas par défaut du service Batch, consultez [Quotas et limites pour le service Azure Batch](batch-quota-limit.md). La bibliothèque Batch Management .NET vous permet de vérifier ces quotas dans vos applications. Vous pouvez ainsi plus facilement décider des allocations avant d’ajouter des comptes ou des ressources de calcul comme les pools et nœuds de calcul.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Vérifier les quotas d'un compte Batch dans un abonnement Azure

Avant de créer un compte Batch dans une région, vous pouvez vérifier dans votre abonnement Azure que vous êtes en mesure d'ajouter un compte dans cette région.

Dans l’extrait de code ci-dessous, nous utilisons tout d’abord **ListAsync** pour obtenir la liste de tous les comptes Batch d’un abonnement. Une fois que nous avons obtenu cette collection, nous pouvons déterminer le nombre de comptes qui se trouvent dans la région cible. Nous utilisons ensuite **GetQuotasAsync** pour obtenir le quota de comptes Batch et déterminer le nombre de comptes (le cas échéant) pouvant être créés dans cette région.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Dans l’extrait de code ci-dessus, `creds` est une instance de **TokenCredentials**. Pour voir un exemple de création de cet objet, consultez l’exemple de code [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) sur GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Vérifier les quotas de ressources de calcul dans un compte Batch

Avant d’augmenter les ressources de calcul dans votre solution Batch, vous pouvez vous assurer que les ressources que vous souhaitez allouer ne dépasseront pas les quotas de comptes. Dans l’extrait de code ci-dessous, nous imprimons les informations de quota pour le compte Batch nommé `mybatchaccount`. Dans votre application, vous pouvez utiliser ces informations pour déterminer si le compte peut ou non gérer les ressources supplémentaires à créer.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Bien qu’il existe des quotas par défaut pour les abonnements et services Azure, la plupart de ces limites peuvent être relevées en [faisant une demande d’augmentation de quota dans le portail Azure](batch-quota-limit.md#increase-a-quota).

## <a name="use-azure-ad-with-batch-management-net"></a>Utiliser Azure AD avec Batch Management .NET

La bibliothèque Batch Management .NET est un client de fournisseur de ressources Azure. Elle est utilisée avec [Azure Resource Manager](../azure-resource-manager/management/overview.md) pour gérer les ressources de compte par programme. Azure AD est nécessaire pour authentifier les requêtes effectuées via les clients de fournisseur de ressources Azure, dont la bibliothèque Batch Management .NET, et via Azure Resource Manager. Pour plus d’informations sur l’utilisation d’Azure AD avec la bibliothèque Batch Management .NET, consultez [Utiliser Azure Active Directory pour authentifier des solutions Batch](batch-aad-auth.md).

## <a name="sample-project-on-github"></a>Exemple de projet sur GitHub

Pour voir la bibliothèque Batch Management .NET en pratique, découvrez l’exemple de projet [AccountManagment](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) sur GitHub. L’exemple d’application AccountManagement illustre les opérations suivantes :

1. Acquérir un jeton de sécurité d’Azure AD avec [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md). Si l’utilisateur n’est pas encore connecté, il est invité à entrer ses informations d’identification Azure.
2. Avec le jeton de sécurité obtenu à partir d’Azure AD, créez une classe [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) pour demander à Azure la liste des abonnements associés au compte. L’utilisateur peut sélectionner un abonnement à partir de la liste si celle-ci en contient plusieurs.
3. Obtenez les informations d’identification associées à l’abonnement sélectionné.
4. Créez un objet [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) avec les informations d’identification.
5. Utilisez un objet [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) pour créer un groupe de ressources.
6. Utilisez un objet [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) pour effectuer un certain nombre d’opérations de compte Batch :
   - Créez un compte Batch dans le nouveau groupe de ressources.
   - Récupérer le compte créé dans le service Batch
   - Imprimer les clés du nouveau compte
   - Régénérer une nouvelle clé primaire pour le compte
   - Imprimer les informations de quota du compte
   - Imprimer les informations de quota de l’abonnement
   - Imprimer tous les comptes de l’abonnement
   - Supprimer le nouveau compte créé.
7. Supprimez le groupe de ressources.

Pour exécuter l’exemple d’application, vous devez tout d’abord l’inscrire auprès de votre locataire Azure AD sur le portail Azure et accorder des autorisations à l’API Azure Resource Manager. Suivez les étapes indiquées dans [Authentifier des solutions de gestion Batch avec Active Directory](batch-aad-auth-management.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [workflow et les ressources principales du service Batch](batch-service-workflow-features.md) telles que les pools, les nœuds, les travaux et les tâches.
- Découvrez les bases du développement d’une application Batch à l’aide de la [bibliothèque Azure Batch pour .NET](quick-run-dotnet.md) ou [Python](quick-run-python.md). Ces guides de démarrage rapide décrivent un exemple d’application qui utilise le service Batch pour exécuter une charge de travail sur plusieurs nœuds de calcul, en utilisant Stockage Azure pour la mise en attente et la récupération de fichiers de la charge de travail.
