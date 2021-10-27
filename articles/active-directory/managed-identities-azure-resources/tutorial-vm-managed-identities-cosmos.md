---
title: Utiliser les identités managées d’une machine virtuelle pour accéder à Cosmos DB | Microsoft Docs
description: Découvrir comment utiliser des identités managées avec des machines virtuelles Windows à l’aide du portail Azure, de l’interface CLI, de PowerShell ou d’un modèle Azure Resource Manager
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: msi
ms.workload: integration
ms.topic: tutorial
ms.date: 10/14/2021
ms.author: barclayn
ms.custom: ep-miar
ms.openlocfilehash: 7ff0ae1578a8b1d637f7baee50ed6d71a63e3996
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070106"
---
# <a name="how-to-use-managed-identities-to-connect-to-cosmos-db-from-an-azure-virtual-machine"></a>Comment utiliser des identités managées pour se connecter à Cosmos DB à partir d’une machine virtuelle Azure

Dans cet article, nous allons configurer une machine virtuelle afin d’utiliser des identités managées pour se connecter à Cosmos. [Azure Cosmos DB](../../cosmos-db/introduction.md) est une base de données NoSQL complètement managée pour développer des applications modernes. Les [identités managées pour les ressources Azure](overview.md) permettent à vos applications de s’authentifier lorsqu’elles accèdent à des services qui prennent en charge l’authentification Azure AD à l’aide d’une identité managée par Azure.

## <a name="prerequisites"></a>Prérequis

- Connaissance de base des identités managées. Si vous souhaitez en savoir plus sur les identités managées pour les ressources Azure avant de continuer, consultez la [vue d’ensemble](overview.md) des identités managées.
- Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/).
- Vous aurez peut-être besoin de [PowerShell](/powershell/azure/new-azureps-module-az) ou de l’[interface CLI](/cli/azure/install-azure-cli).
- [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) ou un autre environnement de développement de votre choix. 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources appelé **mi-test**. Nous allons utiliser ce groupe de ressources pour toutes les ressources utilisées dans ce tutoriel.

- [Créer un groupe de ressources avec le portail Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)
- [Créer un groupe de ressources avec l’interface CLI](../../azure-resource-manager/management/manage-resource-groups-cli.md#create-resource-groups)
- [Créer un groupe de ressources avec PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md#create-resource-groups)

## <a name="create-an-azure-vm-with-a-managed-identity"></a>Créer une machine virtuelle Azure avec une identité managée

Pour ce tutoriel, vous avez besoin d’une machine virtuelle Azure. Créez une machine virtuelle avec une identité managée affectée par le système activée et appelez-la **mi-vm-01**.  Vous pouvez aussi [créer une identité managée affectée par l’utilisateur](how-manage-user-assigned-managed-identities.md) appelée **mi-ua-01** dans le groupe de ressources que nous avons créé plus tôt (**mi-test**). Si vous utilisez une identité managée affectée par l’utilisateur, vous pouvez l’affecter à une machine virtuelle lors de la création.

### <a name="create-a-vm-with-a-system-assigned-managed-identity"></a>Créer une machine virtuelle avec une identité managée affectée par le système

Pour créer une machine virtuelle Azure avec l’identité managée affectée par le système sur une machine virtuelle, votre compte a besoin de l’affectation de rôle [Contributeur de machine virtuelle](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Aucune autre attribution de rôle Azure AD n’est requise.

# <a name="portal"></a>[Portail](#tab/azure-portal)

- Dans le **portail Azure**, recherchez **machines virtuelles**.
- Choisissez **Créer**.
- Sous l’onglet Informations de base, fournissez les informations requises.
- Choisissez **Suivant : Disques >**.
- Continuez à renseigner les informations selon les besoins, puis sous l’onglet **Gestion**, recherchez la section **Identité** et cochez la case à côté de **Identité managée affectée par le système**.

:::image type="content" source="media/how-to-manage-identities-vm-cosmos/create-vm-system-assigned-managed-identities.png" alt-text="Image illustrant comment activer les identités managées affectées par le système lors de la création d’une machine virtuelle.":::

Pour plus d’informations, consultez la documentation sur les machines virtuelles Azure :

- [Linux](../../virtual-machines/linux/quick-create-portal.md)
- [Windows](../../virtual-machines/windows/quick-create-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[New-AZVM](/powershell/module/az.compute/new-azvm) crée les ressources que vous référencez si elles n’existent pas. Pour créer une machine virtuelle avec une identité managée affectée par le système activée, passez le paramètre **-SystemAssignedIdentity** comme indiqué ci-dessous. 


```powershell

New-AzVm `
    -ResourceGroupName "My VM" `
    -Name "My resource group" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -SystemAssignedIdentity
    -OpenPorts 80,3389
```

- [Démarrage rapide : Créer une machine virtuelle Windows dans Azure avec PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
- [Démarrage rapide : Créer une machine virtuelle Linux dans Azure avec PowerShell](../../virtual-machines/linux/quick-create-powershell.md)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm/#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVM* avec une identité managée affectée par le système, comme le demande le paramètre `--assign-identity`. Les paramètres `--admin-username` et `--admin-password` spécifient le nom d’utilisateur et le mot de passe d’administration du compte pour la connexion à la machine virtuelle. Mettez à jour ces valeurs en fonction de votre environnement : 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

- [Créer une machine virtuelle Linux avec une identité managée affectée par le système](../../virtual-machines/linux/quick-create-cli.md)
- [Créer une machine virtuelle Windows avec une identité managée affectée par le système](../../virtual-machines/windows/quick-create-cli.md)

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

Pour activer l’identité managée affectée par le système, chargez le modèle dans un éditeur, recherchez la ressource `Microsoft.Compute/virtualMachines` qui vous intéresse dans la section `resources`, puis ajoutez la propriété `"identity"` au même niveau que la propriété `"type": "Microsoft.Compute/virtualMachines"`. Utilisez la syntaxe suivante :

   ```json
   "identity": {
       "type": "SystemAssigned"
   },
   ```

Lorsque vous avez terminé, les sections ci-après doivent être ajoutées à la section `resource` de votre modèle. Votre modèle doit ressembler à l’exemple illustré ci-dessous :

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('myVM')]",
            "location": "[myResourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

---


### <a name="create-a-vm-with-a-user-assigned-managed-identity"></a>Créer une machine virtuelle avec une identité managée affectée par l’utilisateur

Les étapes ci-dessous vous montrent comment créer une machine virtuelle avec une identité managée affectée par l’utilisateur configurée.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Aujourd’hui, le portail Azure ne prend pas en charge l’attribution d’une identité managée affectée par l’utilisateur lors de la création d’une machine virtuelle. Vous devez créer une machine virtuelle, puis lui attribuer une identité managée affectée par l’utilisateur.

[Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant le portail Azure](qs-configure-portal-windows-vm.md#user-assigned-managed-identity)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Créez une machine virtuelle Windows avec une identité managée affectée par l’utilisateur spécifiée.

```powershell
New-AzVm `
    -ResourceGroupName "<Your resource group>" `
    -Name "<Your VM name>" `
    -Location "East US" `
    -VirtualNetworkName "<myVnet>" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -UserAssignedIdentity "/subscriptions/<Your subscription>/resourceGroups/<Your resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Your user assigned managed identity>" `
    -OpenPorts 80,3389

```

Créez une machine virtuelle Linux avec une identité managée affectée par l’utilisateur spécifiée.

```powershell
New-AzVm `
    -Name "<Linux VM name>" `
    -image CentOS
    -ResourceGroupName "<Your resource group>" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -Linux `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -UserAssignedIdentity "/subscriptions/<Your subscription>/resourceGroups/<Your resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Your user assigned managed identity>" `
    -OpenPorts 22


```

L’identité managée affectée par l’utilisateur doit être spécifiée avec son [resourceID](how-manage-user-assigned-managed-identities.md
). 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az vm create --resource-group <MyResourceGroup> --name <myVM> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
```

[Configurer des identités managées pour les ressources Azure sur une machine virtuelle en utilisant l’interface Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

Selon la version de votre API, vous devez effectuer [différentes étapes](qs-configure-template-windows-vm.md#user-assigned-managed-identity). Si votre apiVersion est 2018-06-01, vos identités managées affectées par l’utilisateur sont stockées au format dictionnaire userAssignedIdentities et la valeur ```<identityName>``` est le nom d’une variable que vous définissez dans la section variables de votre modèle. Dans la variable, vous pointez sur l’identité managée affectée par l’utilisateur que vous souhaitez attribuer.

```json
    "variables": {
     "identityName": "my-user-assigned" 
        
    },
```

Sous l’élément ressources, ajoutez l’entrée suivante pour attribuer une identité managée affectée par l’utilisateur à votre machine virtuelle. Veillez à remplacer ```<identityName>``` par le nom de l’identité managée affectée par l’utilisateur que vous avez créée.

```json

"resources": [
     {
         //other resource provider properties...
         "apiVersion": "2018-06-01",
         "type": "Microsoft.Compute/virtualMachines",
         "name": "[variables('vmName')]",
         "location": "[resourceGroup().location]",
         "identity": {
             "type": "userAssigned",
             "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<identityName>'))]": {}
             }
         }
     }
 ]
```

---

## <a name="create-a-cosmos-db-account"></a>Créer un compte Cosmos DB

Maintenant que nous avons une machine virtuelle avec une identité managée affectée par l’utilisateur ou une identité managée affectée par le système, nous avons besoin d’un compte Cosmos DB disponible avec des droits d’administration. Si vous avez besoin de créer un compte Cosmos DB pour ce tutoriel, le [Démarrage rapide Cosmos DB](../..//cosmos-db/sql/create-cosmosdb-resources-portal.md) fournit le détail des étapes à suivre.

>[!NOTE]
> Les identités managées peuvent être utilisées pour accéder à toute ressource Azure prenant en charge l’authentification Azure Active Directory. Ce tutoriel part du principe que votre compte Cosmos DB sera configuré comme indiqué ci-dessous.

 |Paramètre|Valeur|Description |
   |---|---|---|
   |Abonnement|Nom d’abonnement|Sélectionnez l’abonnement Azure à utiliser pour ce compte Azure Cosmos. |
   |Groupe de ressources|Nom de groupe ressources|Sélectionnez **mi-test** ou **Créer**, puis entrez un nom unique pour le nouveau groupe de ressources. |
   |Nom du compte|Un nom unique|Entrez un nom pour identifier votre compte Azure Cosmos. Étant donné que *documents.azure.com* est ajouté au nom que vous fournissez pour créer votre URI, utilisez un nom unique.<br><br>Le nom peut uniquement contenir des lettres minuscules, des chiffres et le caractère de trait d’union (-). Sa longueur doit être comprise entre 3 et 44 caractères.|
   |API|Type de compte à créer|Sélectionnez **Core (SQL)** pour créer une base de données orientée document et effectuez des requêtes à l’aide de la syntaxe SQL. <br><br>[En savoir plus sur l’API SQL](../../cosmos-db/introduction.md).|
   |Emplacement|La région la plus proche de vos utilisateurs|Sélectionnez la zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Utilisez l’emplacement le plus proche de vos utilisateurs pour leur donner l’accès le plus rapide possible aux données.|

   > [!NOTE]
   > Si vous faites des tests, vous souhaiterez peut-être appliquer une remise de niveau gratuit Azure Cosmos DB. Avec le niveau gratuit d’Azure Cosmos DB, vous recevez gratuitement 1 000 RU/s et 25 Go de stockage dans un compte. Découvrez-en plus sur le [niveau gratuit](https://azure.microsoft.com/pricing/details/cosmos-db/). Sachez que, dans le cadre de ce tutoriel, ce choix ne fait aucune différence.

## <a name="grant-access"></a>Accorder l'accès

À ce stade, nous devrions avoir à la fois une machine virtuelle configurée avec une identité managée et un compte Cosmos DB. Avant de continuer, nous devons accorder à l’identité managée deux ou trois rôles différents.

- Accordez tout d’abord l’accès au plan de gestion Cosmos en utilisant [Azure RBAC](../../cosmos-db/role-based-access-control.md). L’identité managée doit avoir le rôle Contributeur de comptes DocumentDB attribué pour créer des bases de données et des conteneurs.

- Vous devez également accorder un rôle de contributeur à l’identité managée en utilisant [Cosmos RBAC](../../cosmos-db/how-to-setup-rbac.md). Vous pouvez voir les étapes spécifiques ci-dessous. 

> [!NOTE] 
> Nous allons utiliser le rôle **Contributeur aux données intégrées Cosmos DB**. Pour accorder l’accès, vous devez associer la définition de rôle à l’identité. Dans notre cas, il s’agit de l’identité managée associée à notre machine virtuelle.

# <a name="portal"></a>[Portail](#tab/azure-portal)

**À ce stade, aucune option d’attribution de rôle n’est disponible dans le portail Azure.**


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>" 
$contributorRoleDefinitionId = "00000000-0000-0000-0000-000000000002" # This is the ID of the Cosmos DB Built-in Data contributor role definition
$principalId = "1111111-1111-11111-1111-11111111" # This is the object ID of the managed identity.
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $contributorRoleDefinitionId `
    -Scope "/" `
    -PrincipalId $principalId
```

Une fois que l’étape d’attribution de rôle est terminée, vous devez voir des résultats similaires à ceux indiqués ci-dessous.

:::image type="content" source="media/how-to-manage-identities-vm-cosmos/results-role-assignment.png" alt-text="Voici les résultats de l’attribution de rôle.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '00000000-0000-0000-0000-000000000002' # This is the ID of the Cosmos DB Built-in Data contributor role definition
principalId = "1111111-1111-11111-1111-11111111" # This is the object ID of the managed identity.
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId

```

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

```JSON
{
  "id": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/sqlRoleAssignments/00000000-0000-0000-0000-000000000002",
  "name": "myRoleAssignmentId",
  "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments",
  "properties": {
    "roleDefinitionId": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/sqlRoleDefinitions/00000000-0000-0000-0000-000000000002",
    "scope": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/dbs/purchases/colls/redmond-purchases",
    "principalId": "myPrincipalId"
  }
}

```

---

## <a name="access-data"></a>Accéder aux données

Pour obtenir l’accès à Cosmos avec des identités managées, la bibliothèque Azure.identity doit être utilisée pour activer l’authentification dans votre application. Vous pouvez appeler [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) directement ou utiliser [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential).

La classe ManagedIdentityCredential tente une authentification à l’aide d’une identité managée affectée à l’environnement de déploiement. La classe [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme) passe par différentes options d’authentification dans l’ordre. La deuxième option d’authentification que tente DefaultAzureCredential est les identités managées. 

Dans l’exemple ci-dessous, vous allez créer une base de données, un conteneur et un élément dans le conteneur, puis lire l’élément qui vient d’être créé avec l’identité managée affectée par le système de la machine virtuelle. Si vous souhaitez utiliser une identité managée affectée par l’utilisateur, vous devez en spécifier une en indiquant l’ID client de l’identité managée. 

```csharp
string userAssignedClientId = "<your managed identity client Id>";
var tokenCredential = new DefaultAzureCredential(new DefaultAzureCredentialOptions { ManagedIdentityClientId = userAssignedClientId });
```

Pour utiliser l’exemple ci-dessous, vous devez disposer des packages NuGet suivants :

- Azure.Identity
- Microsoft.Azure.Cosmos
- Microsoft.Azure.Management.CosmosDB

En plus des packages NuGet ci-dessus, vous devez également activer **Inclure la préversion**, puis ajouter **Azure.ResourceManager.CosmosDB**. 

```csharp
using Azure.Identity;
using Azure.ResourceManager.CosmosDB;
using Azure.ResourceManager.CosmosDB.Models;
using Microsoft.Azure.Cosmos;
using System;
using System.Threading.Tasks;

namespace MITest
{
    class Program
    {
        static async Task Main(string[] args)
        {
            var subscriptionId = "Your subscription ID";
            var resourceGroupName = "You resource group";
            var accountName = "Cosmos DB Account name";
            var databaseName = "mi-test";
            var containerName = "container01";

            var tokenCredential = new DefaultAzureCredential();

            // create the management clientSS
            var managementClient = new CosmosDBManagementClient(subscriptionId, tokenCredential);

            // create the data client
            var dataClient = new CosmosClient("https://[Account].documents.azure.com:443/", tokenCredential);

            // create a new database 
            var createDatabaseOperation = await managementClient.SqlResources.StartCreateUpdateSqlDatabaseAsync(resourceGroupName, accountName, databaseName,
                new SqlDatabaseCreateUpdateParameters(new SqlDatabaseResource(databaseName), new CreateUpdateOptions()));
            await createDatabaseOperation.WaitForCompletionAsync();

            // create a new container
            var createContainerOperation = await managementClient.SqlResources.StartCreateUpdateSqlContainerAsync(resourceGroupName, accountName, databaseName, containerName,
                new SqlContainerCreateUpdateParameters(new SqlContainerResource(containerName), new CreateUpdateOptions()));
            await createContainerOperation.WaitForCompletionAsync();


            // create a new item 
            var partitionKey = "pkey";
            var id = Guid.NewGuid().ToString();
            await dataClient.GetContainer(databaseName, containerName)
                .CreateItemAsync(new { id = id, _partitionKey = partitionKey }, new PartitionKey(partitionKey));


            // read back the item
            var pointReadResult = await dataClient.GetContainer(databaseName, containerName)
                .ReadItemAsync<dynamic>(id, new PartitionKey(partitionKey));


            // run a query
            await dataClient.GetContainer(databaseName, containerName)
                .GetItemQueryIterator<dynamic>("SELECT * FROM c")
                .ReadNextAsync();
        }
    }
}

```

Exemples par langage qui utilisent ManagedIdentityCredential :

### <a name="net"></a>.NET

Initialisez votre client Cosmos DB :

```csharp
CosmosClient client = new CosmosClient("<account-endpoint>", new ManagedIdentityCredential());
```

Puis, [lisez et écrivez des données](../../cosmos-db/sql/sql-api-dotnet-v3sdk-samples.md).

### <a name="java"></a>Java

Initialisez votre client Cosmos DB :

```java
CosmosAsyncClient Client = new CosmosClientBuilder().endpoint("<account-endpoint>") .credential(new ManagedIdentityCredential()) .build();
```

Puis, lisez et écrivez les données comme décrit dans [ces exemples](../../cosmos-db/sql/sql-api-java-sdk-samples.md).

### <a name="javascript"></a>JavaScript

Initialisez votre client Cosmos DB :

```javascript
const client = new CosmosClient({ "<account-endpoint>", aadCredentials: new ManagedIdentityCredential() });
```

Puis, lisez et écrivez les données comme décrit dans [ces exemples](../../cosmos-db/sql/sql-api-nodejs-samples.md).

## <a name="clean-up-steps"></a>Étapes de nettoyage

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail](https://portal.azure.com), sélectionnez la ressource à supprimer.

1. Sélectionnez **Supprimer**.

1. Quand vous y êtes invité, confirmez la suppression.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

Section intentionnellement vide

---

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les identités managées pour les ressources Azure :

- [Que sont les identités managées pour les ressources Azure ?](overview.md)
- [Modèles Microsoft Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)

En savoir plus sur Azure Cosmos

- [Modèle de ressource Azure Cosmos DB](../../cosmos-db/account-databases-containers-items.md)
- [Tutoriel : Créer une application console .NET pour gérer des données dans le compte de l’API SQL Azure Cosmos DB](../../cosmos-db/sql/sql-api-get-started.md)
