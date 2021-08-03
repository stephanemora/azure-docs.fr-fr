---
title: Utiliser le chiffrement côté client avec Always Encrypted pour Azure Cosmos DB
description: Découvrir comment utiliser le chiffrement côté client avec Always Encrypted pour Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/25/2021
ms.author: thweiss
ms.openlocfilehash: 884626bd5b0cbbf6fb24bf0e3da3b7776e720f77
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071638"
---
# <a name="use-client-side-encryption-with-always-encrypted-for-azure-cosmos-db-preview"></a>Utiliser le chiffrement côté client avec Always Encrypted pour Azure Cosmos DB (préversion)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Always Encrypted est une fonctionnalité conçue pour protéger les données sensibles, telles que les numéros de carte de crédit ou les numéros nationaux d’identification (par exemple, les numéros de sécurité sociale aux États-Unis), qui sont stockées dans Azure Cosmos DB. Always Encrypted permet aux clients de chiffrer des données sensibles dans des applications clientes sans jamais révéler les clés de chiffrement à la base de données.

Always Encrypted apporte des fonctionnalités de chiffrement côté client à Azure Cosmos DB. Le chiffrement de vos données côté client peut être requis dans les scénarios suivants :

- **Protection des données sensibles ayant des caractéristiques de confidentialité spécifiques** : Always Encrypted permet aux clients de chiffrer des données sensibles dans leurs applications et de ne jamais révéler les données de texte brut ou les clés de chiffrement au service Azure Cosmos DB.
- **Implémentation du contrôle d’accès par propriété** : Étant donné que le chiffrement est contrôlé avec les clés que vous possédez et gérez à partir d’Azure Key Vault, vous pouvez appliquer des stratégies d’accès pour contrôler les propriétés sensibles auxquelles chaque client a accès.

> [!IMPORTANT]
> Always Encrypted pour Azure Cosmos DB est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Pour commencer à utiliser la préversion d’Always Encrypted pour Azure Cosmos DB, vous pouvez :

- Utiliser la version 2.11.13.0 ou ultérieure de l’[émulateur Azure Cosmos DB local](local-emulator.md).
- Demander à ce que la préversion soit activée sur votre compte Azure Cosmos DB en remplissant [ce formulaire](https://ncv.microsoft.com/poTcF52I6N).

> [!TIP]
> Avez-vous des commentaires à partager concernant la préversion d’Always Encrypted pour Azure Cosmos DB ? Contactez [azurecosmosdbcse@service.microsoft.com](mailto:azurecosmosdbcse@service.microsoft.com).

## <a name="concepts"></a>Concepts

Always Encrypted pour Azure Cosmos DB introduit de nouveaux concepts impliqués dans la configuration du chiffrement côté client.

### <a name="encryption-keys"></a>Clés de chiffrement

#### <a name="data-encryption-keys"></a>Clés de chiffrement des données

Lorsque vous utilisez Always Encrypted, les données sont chiffrées à l’aide de clés de chiffrement de données (DEK) qui doivent être créées au préalable. Ces chiffrements sont stockés dans le service Azure Cosmos DB et sont définis au niveau de la base de données, de sorte qu’un DEK peut être partagé entre plusieurs conteneurs. La création des DEK est effectuée côté client à l’aide du SDK Azure Cosmos DB.

Vous pouvez :

- Créez un DEK par propriété à chiffrer, ou
- Utilisez le même DEK pour chiffrer plusieurs propriétés.

#### <a name="customer-managed-keys"></a>Clés gérées par le client

Avant que les DEK soient stockés dans Azure Cosmos DB, elles sont encapsulées par une clé gérée par le client (CMK). En contrôlant l’encapsulation et le désencapsulage des DEK, les CMK contrôlent efficacement l’accès aux données chiffrées à l’aide de leur chiffrement correspondant. Le stockage de CMK est conçu comme un modèle extensible/plug-in, avec une implémentation par défaut qui s’attend à ce qu’elles soient stockées dans Azure Key Vault.

:::image type="content" source="./media/how-to-always-encrypted/encryption-keys.png" alt-text="Clés de chiffrement" border="true":::

### <a name="encryption-policy"></a>Stratégie de chiffrement

À l’instar d’une [stratégie d’indexation](index-policy.md), une stratégie de chiffrement est une spécification au niveau du conteneur qui décrit la façon dont les propriétés JSON doivent être chiffrées. Cette stratégie doit être fournie lors de la création du conteneur et elle est immuable. Dans la version actuelle, vous ne pouvez pas mettre à jour la stratégie de chiffrement.

Pour chaque propriété que vous souhaitez chiffrer, la stratégie de chiffrement définit :

- Le chemin d’accès de la propriété sous la forme de `/property`. Seuls les chemins d’accès de niveau supérieur sont actuellement pris en charge. Les chemins d’accès imbriqués tels que `/path/to/property` ne sont pas pris en charge.
- ID de la [DEK](#data-encryption-keys) à utiliser lors du chiffrement et du déchiffrement de la propriété.
- Un type de chiffrement. Il peut être aléatoire ou déterministe.
- L’algorithme de chiffrement à utiliser lors du chiffrement de la propriété. L’algorithme spécifié peut remplacer l’algorithme défini lors de la création de la clé s’ils sont compatibles.

> [!NOTE]
> Les propriétés suivantes ne peuvent pas être chiffrées :
> - id
> - Clé de partition du conteneur

#### <a name="randomized-vs-deterministic-encryption"></a>Chiffrement aléatoire ou déterministe

Le service Azure Cosmos DB ne voit jamais le texte brut des propriétés chiffrées avec Always Encrypted. Toutefois, il prend toujours en charge certaines fonctionnalités d’interrogation sur les données chiffrées, en fonction du type de chiffrement utilisé pour une propriété. Always Encrypted prend en charge les deux types de chiffrement suivants :

- **Chiffrement déterministe** : génère toujours la même valeur chiffrée pour une valeur de texte brut et une configuration de chiffrement données. L’utilisation du chiffrement déterministe permet aux requêtes d’appliquer des filtres d’égalité sur les propriétés chiffrées. Toutefois, elle peut permettre aux attaquants de deviner des informations sur les valeurs chiffrées en examinant les séquences dans la propriété chiffrée. C’est particulièrement vrai si l’ensemble de valeurs chiffrées possibles est restreint, par exemple Vrai/Faux ou Région Nord/Sud/Est/Ouest.

- **Chiffrement aléatoire** : utilise une méthode qui chiffre les données de manière moins prévisible. Le chiffrement aléatoire est plus sécurisé, mais il empêche de filtrer les requêtes sur les propriétés chiffrées.

Consultez [Génération du vecteur d’initialisation (IV)](/sql/relational-databases/security/encryption/always-encrypted-cryptography#step-1-generating-the-initialization-vector-iv) pour en savoir plus sur le chiffrement déterministe et aléatoire dans Always Encrypted.

## <a name="setup-azure-key-vault"></a>Configurer Azure Key Vault

La première étape pour commencer avec Always Encrypted consiste à créer vos CMK dans Azure Key Vault :

1. Créez une nouvelle instance d’Azure Key Vault ou accédez à une instance existante.
1. Créez une nouvelle clé dans la section **Clés**.
1. Une fois la clé créée, accédez à sa version actuelle et copiez son identificateur de clé complet :<br>`https://<my-key-vault>.vault.azure.net/keys/<key>/<version>`. Si vous omettez la version de clé à la fin de l’identificateur de clé, la version la plus récente de la clé est utilisée.

Ensuite, vous devez configurer la manière dont le SDK Azure Cosmos DB accédera à votre instance Azure Key Vault. Cette authentification s’effectue par le biais d’une identité Azure Active Directory (AD). Il est très probable que vous utilisiez l’identité d’une application Azure AD ou une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) en tant que proxy entre votre code client et votre instance Azure Key Vault, même si n’importe quel type d’identité peut être utilisé. Procédez comme suit pour utiliser une application Azure AD comme proxy :

1. Créez une nouvelle application et ajoutez une clé secrète client comme décrit dans [ce guide de démarrage rapide](../active-directory/develop/quickstart-register-app.md).

1. Revenez à votre instance Azure Key Vault, accédez à la section **Stratégies d’accès** et ajoutez une nouvelle stratégie :

   1. Dans **Autorisations de clé**, sélectionnez **Obtenir**, **Répertorier**, **Désencapsuler la clé**, **Encapsuler la clé**, **Vérifier** et **Signer**.
   1. Dans **Sélectionner le principal**, recherchez l’application AAD que vous avez créée précédemment.

### <a name="protect-your-cmk-from-accidental-deletion"></a>Protéger votre CMK de la suppression accidentelle

Pour vous assurer que vous ne perdez pas l’accès à vos données chiffrées après la suppression accidentelle de vos CMK, il est recommandé de définir deux propriétés sur votre instance Azure Key Vault : la **suppression réversible** et la **protection de purge**.

Si vous créez une instance Azure Key Vault, activez ces propriétés lors de la création :

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Activation de la suppression réversible et de la protection de purge pour une nouvelle instance Azure Key Vault":::

Si vous utilisez une instance Azure Key Vault existante, vous pouvez vérifier que ces propriétés sont activées en examinant la section **Propriétés** dans le Portail Azure. Si une de ces propriétés n’est pas activée, consultez les sections intitulées « Activation de la suppression réversible » et « Activation de la protection de purge » dans l’un des articles suivants :

- [Guide pratique pour utiliser la suppression réversible avec Power​Shell](../key-vault/general/key-vault-recovery.md)
- [Guide pratique pour utiliser la suppression réversible avec Azure CLI](../key-vault/general/key-vault-recovery.md)

## <a name="initialize-the-sdk"></a>Initialiser le SDK

> [!NOTE]
> Always Encrypted pour Azure Cosmos DB est actuellement pris en charge :
> - En **.NET**, avec le [package Microsoft.Azure.Cosmos.Encryption](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Encryption).
> - En **Java** avec le [package azure.cosmos.encryption](https://mvnrepository.com/artifact/com.azure/azure-cosmos-encryption).

Pour utiliser Always Encrypted, une instance `EncryptionKeyStoreProvider` doit être attachée à votre instance de SDK Azure Cosmos DB. Cet objet est utilisé pour interagir avec le magasin de clés qui héberge vos CMK. Le fournisseur de magasin de clés par défaut pour Azure Key Vault est nommé `AzureKeyVaultKeyStoreProvider`.

Les extraits de code suivants montrent comment utiliser l’identité d’une application Azure AD avec une clé secrète client. Vous trouverez des exemples de création de différents types de classes `TokenCredential` :

- [Dans .NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [Dans Java](/java/api/overview/azure/identity-readme#credential-classes)

# <a name="net"></a>[.NET](#tab/dotnet)

> [!NOTE]
> En .NET, vous aurez besoin du [package supplémentaire Microsoft.Data.Encryption.AzureKeyVaultProvider](https://www.nuget.org/packages/Microsoft.Data.Encryption.AzureKeyVaultProvider) pour accéder à la classe `AzureKeyVaultKeyStoreProvider`.

```csharp
var tokenCredential = new ClientSecretCredential(
    "<aad-app-tenant-id>", "<aad-app-client-id>", "<aad-app-secret>");
var keyStoreProvider = new AzureKeyVaultKeyStoreProvider(tokenCredential);
var client = new CosmosClient("<connection-string>")
    .WithEncryption(keyStoreProvider);
```

# <a name="java"></a>[Java](#tab/java)

```java
TokenCredential tokenCredential = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<aad-app-tenant-id>")
    .clientId("<aad-app-client-id>")
    .clientSecret("<aad-app-secret>")
    .build();
AzureKeyVaultKeyStoreProvider encryptionKeyStoreProvider =
    new AzureKeyVaultKeyStoreProvider(tokenCredential);
CosmosAsyncClient client = new CosmosClientBuilder()
    .endpoint("<endpoint>")
    .key("<primary-key>")
    .buildAsyncClient();
EncryptionAsyncCosmosClient encryptionClient =
    EncryptionAsyncCosmosClient.buildEncryptionAsyncClient(client, encryptionKeyStoreProvider);
```
---

## <a name="create-a-data-encryption-key"></a>Créer une clé de chiffrement des données

Pour que les données puissent être chiffrées dans un conteneur, une [clé de chiffrement de données](#data-encryption-keys) doit être créée dans la base de données parente. Cette opération s’effectue en appelant la méthode `CreateClientEncryptionKeyAsync` et en passant :

- Un identificateur de chaîne qui identifie de façon unique la clé dans la base de données.
- L’algorithme de chiffrement destiné à être utilisé avec la clé. Un seul algorithme est actuellement pris en charge.
- L’identificateur de clé de la [CMK](#customer-managed-keys) stockée dans Azure Key Vault. Ce paramètre est passé dans un objet générique `EncryptionKeyWrapMetadata`, où `name` peut être n’importe quel nom convivial que vous souhaitez, et `value` doit être l’identificateur de clé.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var database = client.GetDatabase("my-database");
await database.CreateClientEncryptionKeyAsync(
    "my-key",
    DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256,
    new EncryptionKeyWrapMetadata(
        keyStoreProvider.ProviderName,
        "akvKey",
        "https://<my-key-vault>.vault.azure.net/keys/<key>/<version>"));
```

# <a name="java"></a>[Java](#tab/java)

```java
EncryptionCosmosAsyncDatabase database =
    client.getEncryptedCosmosAsyncDatabase("my-database");
database.createClientEncryptionKey(
    "my-key",
    CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256,
    new EncryptionKeyWrapMetadata(
        "akvKey",
        "https://<my-key-vault>.vault.azure.net/keys/<key>/<version>"));
```
---

## <a name="create-a-container-with-encryption-policy"></a>Créer un conteneur avec une stratégie de chiffrement

Spécifiez la stratégie de chiffrement au niveau du conteneur lors de la création du conteneur.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var path1 = new ClientEncryptionIncludedPath
{
    Path = "/property1",
    ClientEncryptionKeyId = "my-key",
    EncryptionType = EncryptionType.Deterministic.ToString(),
    EncryptionAlgorithm = DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256.ToString()
};
var path2 = new ClientEncryptionIncludedPath
{
    Path = "/property2",
    ClientEncryptionKeyId = "my-key",
    EncryptionType = EncryptionType.Randomized.ToString(),
    EncryptionAlgorithm = DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256.ToString()
};
await database.DefineContainer("my-container", "/partition-key")
    .WithClientEncryptionPolicy()
    .WithIncludedPath(path1)
    .WithIncludedPath(path2)
    .Attach()
    .CreateAsync();
```

# <a name="java"></a>[Java](#tab/java)

```java
ClientEncryptionIncludedPath path1 = new ClientEncryptionIncludedPath();
path1.clientEncryptionKeyId = "my-key":
path1.path = "/property1";
path1.encryptionType = CosmosEncryptionType.DETERMINISTIC;
path1.encryptionAlgorithm = CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256;

ClientEncryptionIncludedPath path2 = new ClientEncryptionIncludedPath();
path2.clientEncryptionKeyId = "my-key":
path2.path = "/property2";
path2.encryptionType = CosmosEncryptionType.RANDOMIZED;
path2.encryptionAlgorithm = CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256;

List<ClientEncryptionIncludedPath> paths = new ArrayList<>();
paths.add(path1);
paths.add(path2);

CosmosContainerProperties containerProperties =
    new CosmosContainerProperties("my-container", "/id");
containerProperties.setClientEncryptionPolicy(new ClientEncryptionPolicy(paths));
database.createEncryptionContainerAsync(containerProperties);
```
---

## <a name="read-and-write-encrypted-data"></a>Lire et écrire des données chiffrées

### <a name="how-data-gets-encrypted"></a>Mode de chiffrement des données

Chaque fois qu’un document est écrit dans Azure Cosmos DB, le SDK recherche la stratégie de chiffrement pour déterminer quelles propriétés doivent être chiffrées et comment. Le résultat du chiffrement est une chaîne de base 64.

**Chiffrement de types complexes** :

- Lorsque la propriété à chiffrer est un tableau JSON, chaque entrée du tableau est chiffrée.

- Lorsque la propriété à chiffrer est un objet JSON, seules les valeurs feuille de l’objet sont chiffrées. Les noms de sous-propriétés intermédiaires restent sous forme de texte brut.

### <a name="read-encrypted-items"></a>Lire les éléments chiffrés

Aucune action explicite n’est requise pour déchiffrer les propriétés chiffrées lors de l’émission de lectures de points (extraction d’un seul élément par son ID et sa clé de partition), des requêtes ou de la lecture du flux de modification. La raison est la suivante :

- Le SDK recherche la stratégie de chiffrement pour déterminer les propriétés qui doivent être déchiffrées.
- Le résultat du chiffrement incorpore le type JSON d’origine de la valeur.

Notez que la résolution des propriétés chiffrées et leur déchiffrement ultérieur sont basés uniquement sur les résultats retournés par vos requêtes. Par exemple, si la propriété `property1` est chiffrée mais est projetée dans `property2` (`SELECT property1 AS property2 FROM c`), elle n’est pas identifiée comme une propriété chiffrée lorsqu’elle est reçue par le SDK.

### <a name="filter-queries-on-encrypted-properties"></a>Filtrer les requêtes sur les propriétés chiffrées

Lors de l’écriture de requêtes qui filtrent sur des propriétés chiffrées, la méthode `AddParameterAsync` doit être utilisée pour passer la valeur du paramètre de requête. Cette méthode prend les arguments suivants :

- Nom du paramètre de requête.
- Valeur à utiliser dans la requête.
- Chemin d’accès de la propriété chiffrée (telle que définie dans la stratégie de chiffrement).

> [!IMPORTANT]
> Les propriétés chiffrées peuvent uniquement être utilisées dans les filtres d’égalité (`WHERE c.property = @Value`). Toute autre utilisation renverra des résultats imprévisibles et incorrects. Cette contrainte sera mieux appliquée dans les prochaines versions du SDK.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var queryDefinition = container.CreateQueryDefinition(
    "SELECT * FROM c where c.property1 = @Property1");
await queryDefinition.AddParameterAsync(
    "@Property1",
    1234,
    "/property1");
```

# <a name="java"></a>[Java](#tab/java)

```java
EncryptionSqlQuerySpec encryptionSqlQuerySpec = new EncryptionSqlQuerySpec(
    new SqlQuerySpec("SELECT * FROM c where c.property1 = @Property1"), container);
encryptionSqlQuerySpec.addEncryptionParameterAsync(
    new SqlParameter("@Property1", 1234), "/property1")
```
---

### <a name="reading-documents-when-only-a-subset-of-properties-can-be-decrypted"></a>Lecture de documents lorsque seul un sous-ensemble de propriétés peut être déchiffré

Dans les situations où le client n’a pas accès à toutes les CMK utilisées pour chiffrer les propriétés, seul un sous-ensemble de propriétés peut être déchiffré lorsque les données sont lues. Par exemple, si `property1` a été chiffré avec key1 et `property2` avec key2, une application cliente qui a uniquement accès à key1 peut toujours lire les données, mais pas `property2`. Dans ce cas, vous devez lire vos données par le biais de requêtes SQL et projeter les propriétés que le client ne peut pas déchiffrer : `SELECT c.property1, c.property3 FROM c`.

## <a name="cmk-rotation"></a>Rotation de clés gérées par le client

Vous souhaiterez peut-être « faire pivoter » votre CMK (autrement dit, utiliser une nouvelle CMK au lieu de l’actuelle) si vous soupçonnez que la CMK actuelle a été compromise. Faire pivoter régulièrement les CMK est également une pratique de sécurité courante. Pour effectuer cette rotation, il vous suffit de fournir l’identificateur de clé de la nouvelle CMK qui doit être utilisée pour encapsuler une DEK spécifique. Notez que cette opération n’affecte pas le chiffrement de vos données, mais la protection de la DEK. L’accès à la CMK précédente ne doit pas être révoqué tant que la rotation n’est pas terminée.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
await database.RewrapClientEncryptionKeyAsync(
    "my-key",
    new EncryptionKeyWrapMetadata(
        keyStoreProvider.ProviderName,
        "akvKey",
        " https://<my-key-vault>.vault.azure.net/keys/<new-key>/<version>"));
```

# <a name="java"></a>[Java](#tab/java)

```java
database. rewrapClientEncryptionKey(
    "my-key",
    new EncryptionKeyWrapMetadata(
        "akvKey", " https://<my-key-vault>.vault.azure.net/keys/<new-key>/<version>"));
```
---

## <a name="next-steps"></a>Étapes suivantes

- Obtenez une vue d’ensemble de l’[accès sécurisé aux données dans Cosmos DB](secure-access-to-data.md).
- Découvrez plus d’informations sur les [clés gérées par le client](how-to-setup-cmk.md).