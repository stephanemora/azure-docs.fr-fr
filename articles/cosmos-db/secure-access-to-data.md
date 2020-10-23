---
title: Sécuriser l’accès aux données Azure Cosmos DB
description: Découvrez les concepts liés au contrôle d’accès dans Azure Cosmos DB, notamment les clés primaires, les clés en lecture seule, les utilisateurs et les autorisations.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 30444523bfc26fc0f4eb410957bcc9ee46aff725
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760867"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Sécuriser l’accès aux données dans Azure Cosmos DB

Cet article fournit une vue d’ensemble de la sécurisation de l’accès aux données stockées dans [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB utilise deux types de clés pour authentifier les utilisateurs et permettre d’accéder à ses données et à ses ressources. 

|Type de clé|Ressources|
|---|---|
|[Clés primaires](#primary-keys) |Utilisées pour les ressources d’administration : comptes de base de données, bases de données, utilisateurs et autorisations|
|[Jetons de ressource](#resource-tokens)|Utilisés pour les ressources de l’application : conteneurs, documents, pièces jointes, procédures stockées, déclencheurs et fonctions définies par l’utilisateur|

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Clés primaires

Les clés primaires donnent accès à toutes les ressources administratives du compte de base de données. Elles présentent les caractéristiques suivantes :

- Fournissent un accès aux comptes, aux bases de données, aux utilisateurs et aux autorisations. 
- Ne peuvent pas être utilisées pour fournir un accès précis aux conteneurs et aux documents.
- Sont créées lors de la création d’un compte.
- Peuvent être régénérées à tout moment.

Chaque compte comporte deux clés primaires : une clé primaire et une clé secondaire. L’objectif de ces paires de clés est de pouvoir régénérer ou restaurer des clés tout en fournissant un accès permanent à votre compte et à vos données.

Outre les deux clés primaires du compte Azure Cosmos DB, il existe deux clés en lecture seule. Ces clés en lecture seule autorisent uniquement les opérations de lecture sur le compte. Les clés en lecture seule ne permettent pas de lire les ressources d’autorisation.

Les clés primaire, secondaire, en lecture seule, et en lecture et en écriture peuvent être récupérées et régénérées à l’aide du Portail Azure. Pour connaître la procédure, consultez [Affichage, copie et régénération des clés d’accès](manage-with-cli.md#regenerate-account-key).

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Contrôle d’accès (IAM) dans le portail Azure - Démonstration de la sécurité de la base de données NoSQL":::

### <a name="key-rotation"></a>Rotation des clés<a id="key-rotation"></a>

Le processus de renouvellement de la clé primaire est simple. 

1. Accédez au Portail Azure pour récupérer votre clé secondaire.
2. Remplacez votre clé primaire par votre clé secondaire dans votre application. Assurez-vous que tous les clients Cosmos DB de tous les déploiements sont redémarrés rapidement et qu’ils démarrent à l’aide de la clé mise à jour.
3. Faites pivoter la clé primaire dans le Portail Azure.
4. Vérifiez que la nouvelle clé primaire fonctionne sur toutes les ressources. Le processus de rotation des clés peut prendre de moins d’une minute à plusieurs heures selon la taille du compte Cosmos DB.
5. Remplacez la clé secondaire par la nouvelle clé primaire.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Contrôle d’accès (IAM) dans le portail Azure - Démonstration de la sécurité de la base de données NoSQL" border="false":::

### <a name="code-sample-to-use-a-primary-key"></a>Exemple de code utilisant une clé primaire

L’exemple de code suivant montre comment utiliser une clé primaire et un point de terminaison de compte Cosmos DB pour instancier un DocumentClient et créer une base de données :

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

L’exemple de code suivant montre comment utiliser une clé primaire et un point de terminaison de compte Azure Cosmos DB pour instancier un objet `CosmosClient` :

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>Jetons de ressource <a id="resource-tokens"></a>

Les jetons de ressource fournissent un accès aux ressources d’application au sein d’une base de données. Jetons de ressource :

- Fournissent un accès à des conteneurs, clés de partition, documents, pièces jointes, procédures stockées, déclencheurs et fonctions définies par l’utilisateur spécifiques.
- Sont créés lorsqu’un [utilisateur](#users) dispose des [autorisations](#permissions) sur une ressource spécifique.
- Sont recréés lorsqu’une ressource d’autorisation est exécutée par un appel POST, GET ou PUT.
- Utilisent un jeton de ressource de hachage créé spécifiquement pour l’utilisateur, la ressource et les autorisations.
- Sont liés à une période de validité personnalisable. La durée de validité par défaut est d’une heure. La durée de vie du jeton peut cependant être définie de manière explicite (cinq heures maximum).
- Évitent d’avoir à divulguer la clé primaire, pour plus de sécurité.
- Permettent aux clients de lire, d’écrire et de supprimer des ressources dans le compte Azure Cosmos DB en fonction des autorisations qui leur ont été accordées.

Vous pouvez utiliser un jeton de ressource (en créant des utilisateurs et des autorisations Cosmos DB) lorsque vous voulez donner accès aux ressources dans votre compte Cosmos DB à un client qui ne peut pas être approuvé avec la clé primaire.  

Les jetons de ressource Cosmos DB permettent aux clients de lire, d’écrire et de supprimer des ressources dans votre compte Cosmos DB de manière sécurisée, en fonction des autorisations que vous avez accordées et sans clé primaire ni clé en lecture seule.

Voici un modèle de conception standard dans le cadre duquel des jetons de ressource peuvent être demandés, générés et fournis aux clients :

1. Un service de niveau intermédiaire est configuré pour servir une application mobile pour partager les photos de l'utilisateur.
2. Le service de niveau intermédiaire détient la clé primaire du compte Cosmos DB.
3. L’application photo est installée sur les appareils mobiles des utilisateurs finaux.
4. Lors de la connexion, l'application photo établit l'identité de l'utilisateur avec le service de niveau intermédiaire. Ce mécanisme d'identification dépend totalement de l'application.
5. Une fois l'identité établie, le service de niveau intermédiaire demande des autorisations en fonction de l'identité.
6. Le service de niveau intermédiaire renvoie un jeton de ressource à l'application du téléphone.
7. Cette dernière peut continuer à utiliser le jeton de ressource pour accéder directement aux ressources Azure Cosmos DB avec les autorisations définies et pendant l’intervalle autorisé.
8. À expiration du jeton de ressource, les demandes suivantes reçoivent une exception non autorisée 401.  L'application du téléphone établit alors de nouveau l'identité de l'utilisateur et demande un nouveau jeton de ressource.

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Contrôle d’accès (IAM) dans le portail Azure - Démonstration de la sécurité de la base de données NoSQL" border="false":::

La gestion et la génération des jetons de ressource sont prises en charge par les bibliothèques clientes natives Azure Cosmos DB. Toutefois, si vous utilisez REST, vous devez créer les en-têtes de demande/d’authentification. Pour plus d’informations sur la création d’en-têtes d’authentification pour REST, consultez [Contrôle d’accès aux ressources Azure Cosmos DB](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) ou le code source de notre [Kit de développement logiciel (SDK) .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) ou [Kit de développement logiciel (SDK) Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Pour obtenir un exemple de service de niveau intermédiaire utilisé pour générer ou répartir les jetons de ressource, consultez [l’application ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

## <a name="users"></a>Utilisateurs<a id="users"></a>

Les utilisateurs d’Azure Cosmos DB sont associés à une base de données Cosmos.  Chaque base de données peut contenir zéro, un ou plusieurs utilisateurs Azure Cosmos DB. L’exemple de code suivant indique comment créer un utilisateur Cosmos DB à l’aide du [Kit de développement logiciel (SDK) Azure Cosmos DB .NET v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Chaque utilisateur Cosmos DB dispose d’une méthode ReadAsync() qui permet de récupérer la liste des [autorisations](#permissions) qui lui sont associées.

## <a name="permissions"></a>Autorisations<a id="permissions"></a>

Une ressource d’autorisation est associée à un utilisateur et attribuée au conteneur ainsi qu’au niveau de la clé de partition. Chaque utilisateur peut contenir zéro, une ou plusieurs autorisations. Une ressource d’autorisation donne accès à un jeton de sécurité dont l’utilisateur a besoin lorsqu’il tente d’accéder à un conteneur ou à des données spécifiques dans une clé de partition spécifique. Il existe deux niveaux d’accès disponibles qui peuvent être fournis par une ressource d’autorisation :

- Tout : l’utilisateur dispose de toutes les autorisations sur la ressource.
- Lecture : L’utilisateur peut uniquement lire le contenu de la ressource, il ne peut pas procéder à des opérations d’écriture, de mise à jour ou de suppression au niveau de la ressource.

> [!NOTE]
> Pour exécuter des procédures stockées, l’utilisateur doit disposer de toutes les autorisations sur le conteneur dans lequel la procédure stockée est exécutée.

### <a name="code-sample-to-create-permission"></a>Exemple de code pour créer une autorisation

L’exemple de code suivant indique comment créer une ressource d’autorisation, lire le jeton de ressource de la ressource d’autorisation et associer les autorisations à [l’utilisateur](#users) créé ci-dessus.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>Exemple de code pour l’autorisation de lecture de l’utilisateur

L’extrait de code suivant indique comment récupérer l’autorisation associée à l’utilisateur créé ci-dessus et instancier un nouveau CosmosClient au nom de l’utilisateur, limité à une clé de partition unique.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Ajouter des utilisateurs et attribuer des rôles

Pour ajouter l’accès en lecture aux comptes Azure Cosmos DB à votre compte d’utilisateur, demandez à un propriétaire d’abonnement d’effectuer les étapes suivantes dans le portail Azure.

1. Ouvrez le portail Azure, puis sélectionnez votre compte Azure Cosmos DB.
2. Cliquez sur l’onglet **Contrôle d’accès (IAM)** , puis cliquez sur **+ Ajouter une attribution de rôle**.
3. Dans le volet **Ajouter une attribution de rôle**, dans la zone **Rôle**, sélectionnez **Rôle de lecteur de compte Cosmos DB**.
4. Dans la zone **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou application Azure AD**.
5. Dans votre annuaire, sélectionnez l’utilisateur, le groupe ou l’application qui doit recevoir l’accès.  Dans l’annuaire, vous pouvez rechercher des noms d’affichage, des adresses e-mail et des identificateurs d’objet.
    L’utilisateur, le groupe ou l’application s’affiche alors dans la liste des membres sélectionnés.
6. Cliquez sur **Enregistrer**.

L’entité peut désormais lire les ressources Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Supprimer ou exporter des données utilisateur

Azure Cosmos DB vous permet de rechercher, de sélectionner, de modifier et de supprimer des données personnelles, situées dans une base de données ou des collections. Azure Cosmos DB propose des API pour rechercher et supprimer des données personnelles. Toutefois, il vous incombe d’utiliser les API et de définir la logique nécessaire pour effacer les données personnelles. Chaque API multimodèle (SQL, MongoDB, Gremlin, Cassandra, Table) fournit différents kits SDK de langage qui contiennent des méthodes pour rechercher et supprimer les données personnelles. Vous pouvez également activer la fonctionnalité de [durée de vie (TTL)](time-to-live.md) pour supprimer automatiquement les données après une période spécifiée, sans frais supplémentaires.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la sécurité des bases de données Cosmos, consultez [Sécurité des bases de données Azure Cosmos DB](database-security.md).
- Pour savoir comment créer des jetons d’autorisation Azure Cosmos DB, consultez [Access Control on Azure Cosmos DB Resources](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) (Contrôle d’accès aux ressources Azure Cosmos DB).
- Exemples de gestion des utilisateurs avec des utilisateurs et des autorisations, [Exemples de gestion des utilisateurs à l’aide du Kit de développement logiciel (SDK) .NET v3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
