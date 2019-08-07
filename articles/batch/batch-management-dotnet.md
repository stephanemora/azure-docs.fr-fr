---
title: Gérer des ressources de compte avec la bibliothèque client pour .NET - Azure Batch | Microsoft Docs
description: Créez, supprimez et modifiez des ressources de compte Azure Batch avec la bibliothèque Batch Management .NET.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 41f3eecb1b3f488c355b1bad65b90dae8c76126e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323452"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gérer les quotas et comptes Batch avec la bibliothèque cliente Batch Management pour .NET

> [!div class="op_single_selector"]
> * [Portail Azure](batch-account-create-portal.md)
> * [Gestion de lots .NET](batch-management-dotnet.md)
> 
> 

Vous pouvez réduire la surcharge de maintenance dans vos applications Azure Batch avec la bibliothèque [Batch Management .NET][api_mgmt_net] en automatisant la création et la suppression de comptes Batch, la gestion des clés et la détection des quotas.

* **Créez et supprimez des comptes Batch** dans n'importe quelle région. Si, par exemple, en tant qu'éditeur de logiciels indépendant (ISV) vous fournissez un service à vos clients dans lequel un compte Batch distinct est affecté à chacun à des fins de facturation, vous pouvez ajouter des fonctionnalités de création et de suppression de comptes à votre portail client.
* **Récupérez et régénérez des clés de compte** par programme pour tous vos comptes Batch. Cela est pratique pour maintenir la conformité aux stratégies de sécurité appliquant une substitution ou une expiration périodique des clés de compte. Lorsque vous avez plusieurs comptes Batch dans différentes régions Azure, l’automatisation de ce processus de substitution augmente l’efficacité de votre solution.
* **Vérifiez les quotas des comptes** et déterminez quels comptes Batch possèdent quelles limites sans avoir à tâtonner. En vérifiant les quotas de vos comptes avant de commencer les travaux, de créer des pools ou d’ajouter des nœuds de calcul, vous pouvez ajuster de façon proactive l’endroit et le moment où ces ressources de calcul sont créées. Vous pouvez déterminer quels comptes requièrent des augmentations de quota avant d’allouer des ressources supplémentaires à ces comptes.
* **Combinez les fonctionnalités d’autres services Azure** pour une expérience de gestion complète en utilisant conjointement Batch Management .NET et [Azure Active Directory][aad_about], and the [Azure Resource Manager][resman_overview] et dans la même application. En utilisant ces fonctionnalités et leurs API, vous pouvez fournir une expérience d’authentification transparente, une fonction de création et de suppression des groupes de ressources ainsi que les fonctionnalités décrites ci-dessus, pour une solution de gestion de bout en bout.

> [!NOTE]
> Si cet article se concentre sur la gestion par programme de vos comptes, clés et quotas Batch, vous pouvez effectuer la plupart de ces activités avec le [Portail Azure][azure_portal]. Pour plus d’informations, consultez [Création d’un compte Azure Batch à l’aide du portail Azure](batch-account-create-portal.md) et [Quotas et limites pour le service Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Créez et supprimez des comptes Batch
Comme mentionné ci-dessus, l’une des principales fonctionnalités de l’API Batch Management consiste à créer et à supprimer des comptes Batch dans une région Azure. Pour ce faire, utilisez [BatchManagementClient.Account.CreateAsync][net_create] and [DeleteAsync][net_delete], ou leurs équivalents synchrones.

L’extrait de code suivant crée un compte, récupère le compte créé à partir du service Batch, puis le supprime. Dans cet extrait de code et les autres extraits de cet article, `batchManagementClient` est une instance entièrement initialisée de [BatchManagementClient][net_mgmt_client].

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
> Les applications qui utilisent la bibliothèque Batch Management .NET et sa classe BatchManagementClient nécessitent un accès **administrateur de services fédérés** ou **coadministrateur** à l’abonnement qui possède le compte Batch à gérer. Pour plus d’informations, consultez la section Azure Active Directory et l’exemple de code [AccountManagement][acct_mgmt_sample].
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Récupérez et régénérez des clés de compte
Récupérez les clés primaires et secondaires de n’importe quel compte Batch de votre abonnement avec [ListKeysAsync][net_list_keys]. You can regenerate those keys by using [RegenerateKeyAsync][net_regenerate_keys]. Vous pouvez créer un flux de travail de connexion rationalisé pour vos applications de gestion.

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
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
> Tout d’abord, obtenez une clé de compte pour le compte batch que vous souhaitez gérer avec la classe [ListKeysAsync][net_list_keys]. Then, use this key when initializing the Batch .NET library's [BatchSharedKeyCredentials][net_sharedkeycred], qui est utilisée lors de l’initialisation de [BatchClient][net_batch_client]. Vérifier les quotas d'un abonnement Azure et d'un compte Batch Les abonnements Azure et les services Azure, comme Batch, ont tous des quotas par défaut qui limitent le nombre de certaines entités.
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Pour connaître les quotas par défaut des abonnements Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
Pour obtenir les quotas par défaut du service Batch, consultez [Quotas et limites pour le service Azure Batch](batch-quota-limit.md). La bibliothèque Batch Management .NET vous permet de vérifier ces quotas dans vos applications. Vous pouvez ainsi plus facilement décider des allocations avant d’ajouter des comptes ou des ressources de calcul comme les pools et nœuds de calcul. Vérifier les quotas d'un compte Batch dans un abonnement Azure Avant de créer un compte Batch dans une région, vous pouvez vérifier dans votre abonnement Azure que vous êtes en mesure d'ajouter un compte dans cette région.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Dans l’extrait de code ci-dessous, nous utilisons d’abord [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts] to get a collection of all Batch accounts that are within a subscription. Once we've obtained this collection, we determine how many accounts are in the target region. Then we use [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] pour obtenir le quota de compte batch et déterminer le nombre de comptes (le cas échéant) pouvant être créés dans cette région.
Dans l’extrait ci-dessus, `creds` est une instance de l’exemple de code [TokenCloudCredentials][azure_tokencreds]. To see an example of creating this object, see the [AccountManagement][acct_mgmt_sample] sur GitHub.

Vérifier les quotas de ressources de calcul dans un compte Batch Avant d’augmenter les ressources de calcul dans votre solution Batch, vous pouvez vous assurer que les ressources que vous souhaitez allouer ne dépasseront pas les quotas de comptes. Dans l’extrait de code ci-dessous, nous imprimons les informations de quota pour le compte Batch nommé `mybatchaccount`.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Dans votre application, vous pouvez utiliser ces informations pour déterminer si le compte peut ou non gérer les ressources supplémentaires à créer. Bien qu’il y ait des quotas par défaut pour les abonnements et services Azure, la plupart de ces limites peuvent être relevées en émettant une requête dans le [Portail Azure][azure_portal].

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Par exemple, consultez [Quotas et limites du service Azure Batch](batch-quota-limit.md) pour obtenir des instructions sur l’augmentation des quotas de vos comptes Batch.
Utiliser Azure AD avec Batch Management .NET La bibliothèque Batch Management .NET est un client de fournisseur de ressources Azure. Elle est utilisée avec [Azure Resource Manager][resman_overview] pour gérer les ressources de compte par programme. Azure AD est requis pour authentifier les demandes effectuées par le biais des clients de fournisseur de ressources Azure, y compris la bibliothèque Batch Management .NET et d’[Azure Resource Manager][resman_overview].

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
> Pour plus d’informations sur l’utilisation d’Azure AD avec la bibliothèque Batch Management .NET, consultez [Utiliser Azure Active Directory pour authentifier des solutions Batch](batch-aad-auth.md). Exemple de projet sur GitHub
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Pour voir la bibliothèque Batch Management .NET en pratique, découvrez l’exemple de projet [AccountManagment][acct_mgmt_sample] sur GitHub.

L’exemple d’application AccountManagement illustre les opérations suivantes : Acquérir un jeton de sécurité d’Azure AD avec [ADAL][aad_adal]. Si l’utilisateur n’est pas encore connecté, il est invité à entrer ses informations d’identification Azure. 

## <a name="sample-project-on-github"></a>Avec le jeton de sécurité obtenu à partir d’Azure AD, créez une classe [SubscriptionClient][resman_subclient] pour demander à Azure la liste des abonnements associés au compte.

L’utilisateur peut sélectionner un abonnement à partir de la liste si celle-ci en contient plusieurs. Obtenez les informations d’identification associées à l’abonnement sélectionné.

1. Créez un objet [ResourceManagementClient][resman_client] avec les informations d’identification. Utilisez un objet [ResourceManagementClient][resman_client] pour créer un groupe de ressources.
2. Utilisez un objet [BatchManagementClient][net_mgmt_client] pour effectuer un certain nombre d’opérations de compte Batch : Créez un compte Batch dans le nouveau groupe de ressources.
3. Récupérer le compte créé dans le service Batch
4. Imprimer les clés du nouveau compte
5. Régénérer une nouvelle clé primaire pour le compte
6. Imprimer les informations de quota du compte
   * Imprimer les informations de quota de l’abonnement
   * Imprimer tous les comptes de l’abonnement
   * Supprimer le compte créé
   * Supprimez le groupe de ressources.
   * Avant de supprimer le compte Batch et le groupe de ressources qui viennent d’être créés, vous pouvez les afficher tous les deux dans le [Portail Azure][azure_portal] :
   * Pour exécuter l’exemple d’application, vous devez tout d’abord l’inscrire auprès de votre locataire Azure AD sur le portail Azure et accorder des autorisations à l’API Azure Resource Manager.
   * Suivez les étapes indiquées dans [Authentifier des solutions de gestion Batch avec Active Directory](batch-aad-auth-management.md).
   * [aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Qu’est-ce qu’Azure Active Directory ?"
7. [aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Scénarios d’authentification pour Azure AD"

[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Intégration d’applications dans Azure Active Directory"

To run the sample application successfully, you must first register it with your Azure AD tenant in the Azure portal and grant permissions to the Azure Resource Manager API. Follow the steps provided in <bpt id="p1">[</bpt>Authenticate Batch Management solutions with Active Directory<ept id="p1">](batch-aad-auth-management.md)</ept>.


<bpt id="p1">[</bpt><ept id="p1">aad_about]:../active-directory/fundamentals/active-directory-whatis.md</ept><bpt id="p2"> "</bpt>What is Azure Active Directory?<ept id="p2">"</ept>
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
<bpt id="p1">[</bpt><ept id="p1">aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md</ept><bpt id="p2"> "</bpt>Authentication Scenarios for Azure AD<ept id="p2">"</ept>
<bpt id="p1">[</bpt><ept id="p1">aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md</ept><bpt id="p2"> "</bpt>Integrating Applications with Azure Active Directory<ept id="p2">"</ept>
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
