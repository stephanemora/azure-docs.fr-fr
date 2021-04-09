---
title: Configurer le contrôle d’accès en fonction du rôle pour votre compte Azure Cosmos DB avec Azure AD
description: Découvrez comment configurer le contrôle d’accès en fonction du rôle avec Azure Active Directory pour votre compte Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/17/2021
ms.author: thweiss
ms.openlocfilehash: efde86eac3e0830b36eabfc9e80df09daeed9f6f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586044"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Configurer le contrôle d’accès en fonction du rôle avec Azure Active Directory pour votre compte Azure Cosmos DB (préversion)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Le contrôle d’accès en fonction du rôle d’Azure Cosmos DB est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Cet article concerne le contrôle d’accès en fonction du rôle pour les opérations de plan de données dans Azure Cosmos DB. Si vous utilisez des opérations de plan de gestion, consultez l’article [Contrôle d’accès en fonction du rôle](role-based-access-control.md) appliqué à vos opérations de plan de gestion.

Azure Cosmos DB expose un système de contrôle d’accès en fonction du rôle (RBAC) intégré, qui vous permet :

- D’authentifier vos demandes de données avec une identité Azure Active Directory (Azure AD).
- D’autoriser vos demandes de données avec un modèle d’autorisation basé sur les rôles et précis.

## <a name="concepts"></a>Concepts

Le contrôle d’accès en fonction du rôle (RBAC) de plan de données Azure Cosmos DB s’appuie sur des concepts qui se trouvent généralement dans d’autres systèmes RBAC, comme [Azure RBAC](../role-based-access-control/overview.md) :

- Le [modèle d’autorisation](#permission-model) se compose d’un ensemble d’**actions** ; chacune de ces actions est mappée à une ou plusieurs opérations de base de données. Voici quelques exemples d’actions : la lecture d’un élément, l’écriture d’un élément ou l’exécution d’une requête.
- Les utilisateurs Azure Cosmos DB créent des **[définitions de rôle](#role-definitions)** contenant une liste d’actions autorisées.
- Les définitions de rôle sont affectées à des identités Azure AD spécifiques via des **[attributions de rôle](#role-assignments)** . Une attribution de rôle définit également l’étendue à laquelle s’applique la définition de rôle ; il existe actuellement trois étendues :
    - Un compte Azure Cosmos DB
    - Une base de données Azure Cosmos DB
    - Un conteneur Azure Cosmos DB.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="Concepts du contrôle d’accès en fonction du rôle (RBAC)":::

> [!NOTE]
> Le contrôle d’accès en fonction du rôle (RBAC) d’Azure Cosmos DB n’expose actuellement aucune définition de rôle intégrée.

## <a name="permission-model"></a><a id="permission-model"></a> Modèle d’autorisation

Le tableau ci-dessous liste toutes les actions exposées par le modèle d’autorisation.

| Nom | Opération(s) de base de données correspondante(s) |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Lire les métadonnées de compte. Pour plus d’informations, consultez [Demandes de métadonnées](#metadata-requests). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Créez un élément. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Lire un élément individuel en utilisant son ID et sa clé de partition (lecture par pointage). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Remplacer un élément existant. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | Faire un « upsert » d’un élément, c’est-à-dire le créer s’il n’existe pas ou le remplacer s’il existe. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Supprimer un élément. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Exécuter une [requête SQL](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Lire dans le [flux de modification](read-change-feed.md) du conteneur. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Exécuter une [procédure stockée](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Gérer les [conflits](conflict-resolution-policies.md) pour les comptes de régions à plusieurs écritures (c’est-à-dire lister et supprimer des éléments dans le flux en conflit). |

Les caractères génériques sont pris en charge au niveau des *conteneurs* et des *éléments* :

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> Ce modèle d’autorisation couvre seulement les opérations de base de données qui vous permettent de lire et d’écrire des données. Il **ne couvre aucun** type d’opération de gestion, comme la création de conteneurs ou la modification de leur débit. Pour authentifier les opérations de gestion avec une identité AAD, utilisez à la place [Azure RBAC](role-based-access-control.md).

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Demandes de métadonnées

Lors de l’utilisation des SDK Azure Cosmos DB, ces SDK émettent des demandes de métadonnées en lecture seule lors l’initialisation et pour traiter des demandes de données spécifiques. Ces demandes de métadonnées extraient différents détails de configuration, comme : 

- La configuration globale de votre compte, qui comprend les régions Azure où le compte est disponible.
- La clé de partition de vos conteneurs ou leur stratégie d’indexation.
- La liste des partitions physiques qui constituent un conteneur et leurs adresses.

Elles ne récupèrent *pas* les données que vous avez stockées dans votre compte.

Pour garantir la meilleure transparence de notre modèle d’autorisation, ces demandes de métadonnées sont explicitement couvertes par l’action `Microsoft.DocumentDB/databaseAccounts/readMetadata`. Cette action doit être autorisée dans toutes les situations où l’accès à votre compte Azure Cosmos DB se fait via un des SDK Azure Cosmos DB. Elle peut être affectée (via une attribution de rôle) à n’importe quel niveau de la hiérarchie Azure Cosmos DB (c’est-à-dire le compte, la base de données ou le conteneur).

Les demandes de métadonnées réelles autorisées par l’action `Microsoft.DocumentDB/databaseAccounts/readMetadata` dépendent de l’étendue à laquelle l’action est affectée :

| Étendue | Demandes autorisées par l’action |
|---|---|
| Compte | - Lister les bases de données sous le compte<br>- Pour chaque base de données sous le compte, les actions autorisées au niveau de l’étendue de la base de données |
| Base de données | - Lire les métadonnées de la base de données<br>- Lister les conteneurs sous la base de données<br>- Pour chaque conteneur sous la base de données, les actions autorisées au niveau de l’étendue du conteneur |
| Conteneur | - Lire les métadonnées du conteneur<br>- Lister les partitions physiques sous le conteneur<br>- Résoudre l’adresse de chaque partition physique |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Créer des définitions de rôle

Quand vous créez une définition de rôle, vous devez fournir les éléments suivants :

- Le nom de votre compte Azure Cosmos DB.
- Le groupe de ressources contenant votre compte.
- Le type de la définition de rôle ; seul `CustomRole` est actuellement pris en charge.
- Le nom de la définition de rôle.
- Une liste des [actions](#permission-model) que vous voulez autoriser pour le rôle.
- Une ou plusieurs étendues auxquelles la définition de rôle peut être affectée ; les étendues prises en charge sont :
    - `/` (niveau compte),
    - `/dbs/<database-name>` (niveau base de données),
    - `/dbs/<database-name>/colls/<container-name>` (niveau conteneur).

> [!NOTE]
> Les opérations décrites ci-dessous sont actuellement disponibles dans :
> - Azure PowerShell : [Az.CosmosDB version 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI : [extension « cosmosdb-preview » version 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell

Créez un rôle nommé *MyReadOnlyRole* qui contient seulement des actions de lecture :

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Créez un rôle nommé *MyReadWriteRole* qui contient toutes les actions :

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Listez les définitions de rôle que vous avez créées pour récupérer leur ID :

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

Créez un rôle nommé *MyReadOnlyRole* qui contient seulement des actions de lecture :

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Créez un rôle nommé *MyReadWriteRole* qui contient toutes les actions :

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Listez les définitions de rôle que vous avez créées pour récupérer leur ID :

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Créer des attributions de rôle

Une fois que vous avez créé vos définitions de rôle, vous pouvez les associer à vos identités AAD. Lors de la création d’une attribution de rôle, vous devez fournir :

- Le nom de votre compte Azure Cosmos DB.
- Le groupe de ressources contenant votre compte.
- L’ID de la définition de rôle à attribuer.
- L’ID principal de l’identité à laquelle la définition de rôle doit être affectée.
- L’étendue de l’attribution de rôle ; les étendues prises en charge sont :
    - `/` (niveau compte)
    - `/dbs/<database-name>` (niveau base de données)
    - `/dbs/<database-name>/colls/<container-name>` (niveau conteneur)

  L’étendue doit correspondre à ou être une sous-étendue d’une des étendues affectables de la définition de rôle.

> [!NOTE]
> Si vous voulez créer une attribution de rôle pour un principal de service, veillez à utiliser son **ID d’objet** tel qu’il figure dans la section **Applications d’entreprise** du panneau du portail **Azure Active Directory**.

> [!NOTE]
> Les opérations décrites ci-dessous sont actuellement disponibles dans :
> - Azure PowerShell : [Az.CosmosDB version 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI : [extension « cosmosdb-preview » version 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell

Affecter un rôle à une identité :

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

Affecter un rôle à une identité :

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Initialiser le SDK avec Azure AD

Pour utiliser le contrôle RBAC d’Azure Cosmos DB dans votre application, vous devez mettre à jour la façon dont vous initialisez le SDK Azure Cosmos DB. Au lieu de passer la clé principale de votre compte, vous devez passer une instance d’une classe `TokenCredential`. Cette instance fournit le SDK Azure Cosmos DB avec le contexte nécessaire pour extraire un jeton AAD pour le compte de l’identité que vous souhaitez utiliser.

La façon dont vous créez une instance de `TokenCredential` dépasse le cadre de cet article. Il existe de nombreuses façons de créer une telle instance selon le type d’identité AAD que vous voulez utiliser (principal d’utilisateur, principal de service, groupe, etc.). Plus important encore, votre instance de `TokenCredential` doit se résoudre en l’identité (ID de principal) à laquelle vous avez affecté vos rôles. Vous trouverez des exemples de création d’une classe `TokenCredential` :

- [dans .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme#credential-classes)
- [en Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme#credential-classes)
- [en JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme#credential-classes)

Les exemples ci-dessous utilisent un principal de service avec une instance de `ClientSecretCredential`.

### <a name="in-net"></a>Dans .NET

Le RBAC Azure Cosmos DB est actuellement pris en charge dans la version `preview` du [Kit de développement logiciel (SDK) .NET V3](sql-api-sdk-dotnet-standard.md).

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>En Java

Le RBAC Azure Cosmos DB est actuellement pris en charge dans le [Kit de développement logiciel (SDK) Java V4](sql-api-sdk-java-v4.md).

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>En JavaScript

Le RBAC Azure Cosmos DB est actuellement pris en charge dans le [Kit de développement logiciel (SDK) JavaScript V3](sql-api-sdk-node.md).

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="auditing-data-requests"></a>Audit des demandes de données

Lors de l’utilisation du contrôle RBAC d’Azure Cosmos DB, les [journaux de diagnostic](cosmosdb-monitor-resource-logs.md) contiennent en plus les informations d’identité et d’autorisation pour chaque opération de données. Ceci vous permet d’effectuer un audit détaillé et de récupérer l’identité AAD utilisée pour chaque demande de données envoyée à votre compte Azure Cosmos DB.

Ces informations supplémentaires appartiennent à la catégorie de journalisation **DataPlaneRequests** et se composent de deux colonnes supplémentaires :

- `aadPrincipalId_g` montre l’ID du principal de l’identité AAD qui a utilisée pour authentifier la demande.
- `aadAppliedRoleAssignmentId_g` montre l’[attribution de rôle](#role-assignments) qui a été respectée lors de l’autorisation de la demande.

## <a name="limits"></a>limites

- Vous pouvez créer jusqu’à 100 définitions de rôles et 2 000 attributions de rôles par compte Azure Cosmos DB.
- La résolution de groupe Azure AD n’est actuellement pas prise en charge pour les identités qui appartiennent à plus de 200 groupes.
- Le jeton Azure AD est actuellement transmis en tant qu’en-tête avec chaque requête individuelle envoyée au service Azure Cosmos DB, ce qui augmente la taille globale de la charge utile.
- L'accès à vos données à l'aide d'Azure AD via l'[Explorateur Azure Cosmos DB](data-explorer.md) n'est pas encore pris en charge. Actuellement, pour utiliser l'Explorateur Azure Cosmos DB, l'utilisateur doit avoir accès à la clé primaire du compte.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Quelles API Azure Cosmos DB sont prises en charge par RBAC ?

Seule l’API SQL est actuellement prise en charge.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Est-il possible de gérer les définitions de rôle et les attributions de rôles à partir du portail Azure ?

La prise en charge de portail Azure pour la gestion des rôles n’est pas encore disponible.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Quels SDK de l’API SQL d’Azure Cosmos DB prennent en charge RBAC ?

Les SDK [.NET V3](sql-api-sdk-dotnet-standard.md) et [Java V4](sql-api-sdk-java-v4.md) sont actuellement pris en charge.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Le jeton Azure AD est-il actualisé automatiquement par les SDK Azure Cosmos DB quand il expire ?

Oui.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>Est-il possible de désactiver l’utilisation de la clé principale du compte lors de l’utilisation de RBAC ?

La désactivation de la clé principale du compte n’est pas possible actuellement.

## <a name="next-steps"></a>Étapes suivantes

- Obtenez une vue d’ensemble de l’[accès sécurisé aux données dans Cosmos DB](secure-access-to-data.md).
- En savoir plus sur [RBAC pour la gestion d’Azure Cosmos DB](role-based-access-control.md).
