---
title: Copier et transformer des données dans Azure Cosmos DB (API SQL)
description: Apprenez à copier des données vers et depuis Azure Cosmos DB (API SQL), ainsi qu'à transformer des données dans Azure Cosmos DB (API SQL) à l'aide de Data Factory.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/18/2021
ms.openlocfilehash: 36fae5b71e9aa5c2c6c252ad1aa306bb64d9aecb
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110480077"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copier et transformer des données dans Azure Cosmos DB (API SQL) à l’aide d’Azure Data Factory

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [Version actuelle](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article indique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis et vers Azure Cosmos DB (API SQL), et utiliser Data Flow pour transformer les données dans Azure Cosmos DB (API SQL). Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).



>[!NOTE]
>Ce connecteur prend uniquement en charge l’API SQL Cosmos DB. Pour l'API MongoDB, reportez-vous à [Connecteur de l'API Azure Cosmos DB pour MongoDB](connector-azure-cosmos-db-mongodb-api.md). Les autres types d’API ne sont pas pris en charge actuellement.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Azure Cosmos DB (API SQL) est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Pour l'activité de copie, ce connecteur Azure Cosmos DB (API SQL) prend en charge :

- Copier des données depuis et vers [l0'API SQL](../cosmos-db/introduction.md) d’Azure Cosmos DB à l’aide de la clé, du principal du service ou des identités gérées pour les authentifications des ressources Azure.
- Écrire dans Azure Cosmos DB comme **insert** ou **upsert**.
- Importer et exporter des documents JSON en l’état, ou copier des données depuis ou vers un jeu de données tabulaire. Les exemples incluent une base de données SQL et un fichier CSV. Pour copier des documents en l'état vers ou depuis des fichiers JSON ou une autre collection Azure Cosmos DB, consultez [Importer et exporter des documents JSON](#import-and-export-json-documents).

Data Factory s’intègre à la [bibliothèque d’exécuteur en bloc Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) pour offrir les meilleures performances quand vous écrivez dans Azure Cosmos DB.

> [!TIP]
> La [vidéo de la migration des données](https://youtu.be/5-SRNiC_qOU) vous guide tout au long des étapes de la copie des données depuis le stockage Blob Azure vers Azure Cosmos DB. La vidéo décrit également les considérations de réglage des performances pour l’ingestion des données dans Azure Cosmos DB en général.

## <a name="get-started"></a>Bien démarrer

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres à Azure Cosmos DB (API SQL).

## <a name="linked-service-properties"></a>Propriétés du service lié

Le connecteur Azure Cosmos DB (API SQL) prend en charge les types d’authentification suivants. Consultez les sections correspondantes pour plus d’informations :

- [Authentification par clé](#key-authentication)
- [Authentification du principal de service (préversion)](#service-principal-authentication)
- [Identités managées pour l’authentification des ressources Azure (préversion)](#managed-identity)

### <a name="key-authentication"></a>Authentification par clé

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** doit être définie sur **CosmosDb**. | Oui |
| connectionString |Spécifiez les informations requises pour se connecter à la base de données Azure Cosmos DB.<br />**Remarque** : Vous devez spécifier les informations de base de données dans la chaîne de connexion, comme indiqué dans les exemples ci-dessous. <br/> Vous pouvez également définir une clé de compte dans Azure Key Vault et extraire la configuration `accountKey` de la chaîne de connexion. Pour plus d’informations, reportez-vous aux exemples suivants et à l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion au magasin de données. Vous pouvez utiliser Azure Integration Runtime ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. Si cette propriété n’est pas spécifiée, Azure Integration Runtime par défaut est utilisé. |Non |

**Exemple**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : stockage de la clé de compte dans Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication-preview"></a><a name="service-principal-authentication"></a> Authentification du principal de service (préversion)

>[!NOTE]
>Actuellement, l’authentification du principal du service n’est pas prise en charge dans le flux de données.

Pour l’authentification de principal de service, effectuez les étapes suivantes.

1. Inscrivez une entité d’application dans Azure Active Directory (Azure AD) en suivant les instructions de la section [Inscrire votre application à un locataire Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. Accordez l’autorisation nécessaire au principal de service. Pour obtenir des exemples sur le fonctionnement des autorisations dans Cosmos DB, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](../cosmos-db/how-to-setup-rbac.md). Plus spécifiquement, créez une définition de rôle et attribuez le rôle au principal de service par le biais de l’ID d’objet du principal de service. 

Ces propriétés sont prises en charge pour le service lié :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **CosmosDb**. |Oui |
| accountEndpoint | Spécifiez l’URL du point de terminaison du compte pour Azure Cosmos DB. | Oui |
| database | Spécifiez le nom de la base de données. | Oui |
| servicePrincipalId | Spécifiez l’ID client de l’application. | Oui |
| servicePrincipalCredentialType | Type d’informations d’identification à utiliser pour l’authentification de principal du service. Les valeurs autorisées sont **ServicePrincipalKey** et **ServicePrincipalCert**. | Oui |
| servicePrincipalCredential | Informations d’identification du principal du service. <br/> Quand vous utilisez **ServicePrincipalKey** comme type d’informations d’identification, spécifiez la clé de l’application. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). <br/> Quand vous utilisez **ServicePrincipalCert** comme informations d’identification, référencez un certificat dans Azure Key Vault. | Oui |
| tenant | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Récupérez-les en pointant la souris dans le coin supérieur droit du Portail Azure. | Oui |
| azureCloudType | Pour l’authentification du principal du service, spécifiez le type d’environnement cloud Azure auprès duquel votre application Azure Active Directory est inscrite. <br/> Les valeurs autorisées sont **AzurePublic**, **AzureChina**, **AzureUsGovernment** et **AzureGermany**. Par défaut, l’environnement cloud de la fabrique de données est utilisé. | Non |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser le runtime d'intégration Azure ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. |Non |

**Exemple : utilisation de l’authentification de la clé du principal de service**

Vous pouvez également stocker la clé du principal du service dans Azure Key Vault.

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : utilisation de l’authentification par certificat du principal du service**
```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication-preview"></a><a name="managed-identity"></a> Identités managées pour l’authentification des ressources Azure (préversion)

>[!NOTE]
>Actuellement, l’authentification d’identité managée n’est pas prise en charge dans le flux de données.

Une fabrique de données peut être associée à une [identité managée pour les ressources Azure](data-factory-service-identity.md), laquelle représente cette même fabrique de données. Vous pouvez utiliser directement cette identité managée pour l’authentification Cosmos DB, ce qui revient à utiliser votre propre principal de service. Cela permet à la fabrique désignée d’accéder aux données et de les copier depuis ou vers votre Cosmos DB.

Pour utiliser les identités managées afin d’authentifier les ressources Azure, procédez comme suit.

1. [Récupérez les informations d’identité managée de Data Factory](data-factory-service-identity.md#retrieve-managed-identity) en copiant la valeur de l’**ID d’objet d’identité managée** générée en même temps que votre fabrique.

2. Accordez l’autorisation nécessaire à l’identité managée. Pour obtenir des exemples sur le fonctionnement des autorisations dans Cosmos DB, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](../cosmos-db/how-to-setup-rbac.md). Plus précisément, créez une définition de rôle et attribuez le rôle à l’identité gérée.

Ces propriétés sont prises en charge pour le service lié :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **CosmosDb**. |Oui |
| accountEndpoint | Spécifiez l’URL du point de terminaison du compte pour Azure Cosmos DB. | Oui |
| database | Spécifiez le nom de la base de données. | Oui |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser le runtime d'intégration Azure ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. |Non |

**Exemple :**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données et services liés](concepts-datasets-linked-services.md).

Les propriétés prises en charge pour le jeu de données Azure Cosmos DB (API SQL) sont les suivantes : 

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** du jeu de données doit être définie sur **CosmosDbSqlApiCollection**. |Oui |
| collectionName |Nom de la collection de documents Azure Cosmos DB. |Oui |

Si vous utilisez un jeu de données de type « DocumentDbCollection », il est toujours pris en charge tel quel pour la compatibilité descendante dans l’activité de copie et de recherche, mais il n'est pas pris en charge pour le flux de données. Nous vous suggérons d’utiliser le nouveau modèle à l’avenir.

**Exemple**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Cette section fournit la liste des propriétés prises en charge par Azure Cosmos DB (API SQL) en tant que source et récepteur. Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (API SQL) en tant que source

Pour copier des données à partir d’Azure Cosmos DB (SQL API), affectez la valeur **DocumentDbCollectionSource** au type **source** dans l’activité de copie. 

Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** de la source d’activité de copie doit être définie sur **CosmosDbSqlApiSource**. |Oui |
| query |Spécifiez la requête Azure Cosmos DB pour lire les données.<br/><br/>Exemple :<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Non <br/><br/>À défaut de spécification, cette instruction SQL est exécutée : `select <columns defined in structure> from mycollection` |
| preferredRegions | Liste des régions préférées auxquelles se connecter lors de la récupération des données de Cosmos DB. | Non |
| pageSize | nombre de documents par page du résultat de la requête. La valeur par défaut est « -1 », qui utilise la page dynamique côté service jusqu’à 1000. | Non |
| detectDatetime | Indique s'il faut détecter la date/heure à partir des valeurs de chaîne dans les documents. Valeurs autorisées : **true** (par défaut) et **false**. | Non |

Si vous utilisez une source de type « DocumentDbCollectionSource », elle est toujours prise en charge telle quelle pour une compatibilité descendante. Nous vous suggérons d'utiliser le nouveau modèle à l'avenir, qui offre des fonctionnalités plus riches pour copier les données à partir de Cosmos DB.

**Exemple**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Lorsque vous copiez des données à partir de Cosmos DB, à moins que vous ne souhaitiez [exporter des documents JSON en l'état](#import-and-export-json-documents), la meilleure pratique consiste à spécifier le mappage dans l'activité de copie. Data Factory respecte le mappage que vous avez spécifié sur l'activité. Si une ligne ne contient pas de valeur pour une colonne, une valeur nulle est fournie pour la colonne. Si vous ne spécifiez aucun mappage, Data Factory déduit le schéma à l'aide de la première ligne des données. Si la première ligne ne contient pas le schéma complet, certaines colonnes ne seront pas incluses dans le résultat de l’opération d’activité.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (API SQL) en tant que récepteur

Pour copier des données vers Azure Cosmos DB (SQL API), affectez la valeur **DocumentDbCollectionSink** au type **récepteur** dans l’activité de copie. 

Les propriétés suivantes sont prises en charge dans la section **sink** de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** du récepteur d’activité de copie doit être définie sur **CosmosDbSqlApiSink**. |Oui |
| writeBehavior |Décrit comment écrire des données dans Azure Cosmos DB. Les valeurs autorisées sont **insert** et **upsert**.<br/><br/>Le comportement de la valeur **upsert** consiste à remplacer le document si un document portant le même identificateur existe déjà ; sinon, le document est inséré.<br /><br />**Remarque** : Azure Data Factory génère automatiquement un ID pour un document s’il n’est pas spécifié dans le document d’origine ni par le mappage de colonnes. Cela signifie que vous devez vérifier que votre document comporte un ID afin qu’**upsert** fonctionne comme prévu. |Non<br />(la valeur par défaut est **insert**) |
| writeBatchSize | Data Factory utilise la [bibliothèque d’exécuteur en bloc Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) pour écrire les données dans Azure Cosmos DB. La propriété **writeBatchSize** contrôle la taille des documents fournis par ADF à la bibliothèque. Vous pouvez essayer d’augmenter la valeur de **writeBatchSize** pour améliorer les performances et diminuer la valeur si la taille de votre document est grande (voir les conseils ci-dessous). |Non<br />(la valeur par défaut est **10 000**) |
| disableMetricsCollection | Data Factory collecte des métriques telles que les unités de requête Cosmos DB pour effectuer des suggestions et optimiser les performances de copie. Si ce comportement vous préoccupe, spécifiez `true` pour le désactiver. | Non (la valeur par défaut est `false`) |
| maxConcurrentConnections |La limite supérieure de connexions simultanées établies au magasin de données pendant l’exécution de l’activité. Spécifiez une valeur uniquement lorsque vous souhaitez limiter les connexions simultanées.| Non |


>[!TIP]
>Pour importer des documents JSON en l'état, consultez la section [Importer ou exporter des documents JSON](#import-and-export-json-documents). Pour copier à partir de données au format tabulaire, consultez [Migrer de la base de données relationnelle vers Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB limite la taille des demandes uniques à 2 Mo. La formule est la suivante : taille de la demande = taille de document unique * taille d’écriture Batch. Si vous rencontrez le message d’erreur **« La taille de la demande est trop grande »**, **réduisez la valeur de `writeBatchSize`** dans la configuration du récepteur de copie.

Si vous utilisez une source de type « DocumentDbCollectionSink », elle est toujours prise en charge telle quelle pour une compatibilité descendante. Nous vous suggérons d'utiliser le nouveau modèle à l'avenir, qui offre des fonctionnalités plus riches pour copier les données à partir de Cosmos DB.

**Exemple**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Mappage de schéma

Pour copier des données d'Azure Cosmos DB vers un récepteur tabulaire ou inversement, consultez [Mise en correspondance du schéma](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Lors de la transformation de données dans le flux de données de mappage, vous pouvez lire et écrire dans des collections de Cosmos DB. Pour plus d’informations, consultez la [transformation de la source](data-flow-source.md) et la [transformation du récepteur](data-flow-sink.md) dans le flux de données de mappage.

### <a name="source-transformation"></a>Transformation de la source

Les paramètres spécifiques à Azure Cosmos DB sont disponibles dans l'onglet **Options de la source** de la transformation de la source. 

**Inclure des colonnes système :** si ce paramètre est vrai, ```id```, ```_ts``` et d'autres colonnes système seront englobées dans vos métadonnées de flux de données de CosmosDB. Lors de la mise à jour de collections, il est important de l’inclure, afin de pouvoir récupérer l’ID de ligne existant.

**Taille de la page :** nombre de documents par page du résultat de la requête. La valeur par défaut est « -1 », qui utilise la page dynamique du service jusqu’à 1 000.

**Débit :** définissez une valeur facultative du nombre de RU que vous souhaitez appliquer à votre collection CosmosDB pour chaque exécution de ce flux de données pendant l’opération de lecture. La valeur minimale est 400.

**Régions préférées :** choisissez les régions de lecture privilégiées pour ce processus.

#### <a name="json-settings"></a>Paramètres JSON

**Un seul document :** sélectionnez cette option si ADF doit traiter l’intégralité du fichier comme un document JSON unique.

**Noms de colonnes sans guillemets :** sélectionnez cette option si les noms de colonnes dans le fichier JSON ne sont pas entre guillemets.

**A des commentaires :** utilisez cette sélection si vos documents JSON comportent des commentaires dans les données.

**Entre guillemets simples :** sélectionnez cette option si les colonnes et les valeurs de votre document sont placées entre guillemets simples.

**Barres obliques inverses d’échappement :** si vous utilisez des barres obliques inverses pour échapper des caractères dans votre fichier JSON, choisissez cette option.

### <a name="sink-transformation"></a>Transformation du récepteur

Les paramètres spécifiques à Azure Cosmos DB sont disponibles dans l'onglet **Paramètres** de la transformation du récepteur.

**Méthode de mise à jour** : détermine les opérations autorisées sur la destination de votre base de données. Par défaut, seules les insertions sont autorisées. Pour mettre à jour, effectuer un upsert ou supprimer des lignes, une transformation alter-row est requise afin de baliser les lignes relatives à ces actions. Pour les mises à jour, les opérations upsert et les suppressions, une ou plusieurs colonnes clés doivent être définies pour déterminer la ligne à modifier.

**Action relative à la collection :** détermine si la collection de destination doit être recréée avant l'écriture.
* Aucune : aucune action ne sera effectuée sur la collection.
* Recréer : la collection sera supprimée et recréée.

**Taille du lot** : Entier représentant le nombre d'objets en cours d'écriture dans la collection Cosmos DB au sein de chaque lot. En général, la taille de lot par défaut est suffisante pour commencer. Pour affiner cette valeur, notez que :

- Cosmos DB limite la taille des demandes uniques à 2 Mo. La formule est la suivante : Taille de la demande = Taille d'un seul document x Taille du lot. Si vous rencontrez le message d'erreur « La taille de la demande est trop importante », réduisez la valeur de la taille du lot.
- Plus la taille du lot est importante, meilleur est le débit qu'ADF peut atteindre. Vous devez aussi vous assurer d'allouer suffisamment d'unités de requête pour optimiser votre charge de travail.

**Clé de partition :** Entrez une chaîne qui représente la clé de partition de votre collection. Exemple : ```/movies/title```

**Débit :** définissez une valeur facultative du nombre de RU que vous souhaitez appliquer à votre collection CosmosDB pour chaque exécution de ce flux de données. La valeur minimale est 400.

**Budget du débit d'écriture :** Entier représentant les unités de requête que vous souhaitez allouer pour cette opération d'écriture de flux de données, par rapport au débit total alloué à la collection.

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importer et exporter des documents JSON

À l’aide de ce connecteur Azure Cosmos DB (SQL API), vous pouvez facilement :

* Copier des documents entre deux collections Azure Cosmos DB en l’état.
* Importer des documents JSON de différentes sources dans Azure Cosmos DB, notamment depuis le stockage Blob Azure, Azure Data Lake Store et d’autres banques de données basées sur des fichiers prises en charge par Azure Data Factory.
* Exporter des documents JSON d’une collection Azure Cosmos DB vers différentes banques basées sur des fichiers.

Pour obtenir une copie indépendante du schéma :

* Lors de l’utilisation de l’outil de copie de données, sélectionnez l’option **Exporter en l’état dans des fichiers JSON ou une collection Cosmos DB**.
* Lors de l’utilisation de la création d’activité, choisissez le format JSON avec le magasin de fichiers correspondant à la source ou au récepteur.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrer de la base de données relationnelle vers Cosmos DB

Lors de la migration d'une base de données relationnelle (par exemple, SQL Server) vers Azure Cosmos DB, l'activité de copie peut facilement mapper des données tabulaires de la source pour aplatir les documents JSON dans Cosmos DB. Dans certains cas, vous souhaiterez peut-être reconcevoir le modèle de données afin de l'optimiser pour les cas d'usage NoSQL, conformément à [Modélisation des données dans Azure Cosmos DB](../cosmos-db/modeling-data.md), par exemple pour dénormaliser les données en incorporant tous les sous-éléments associés dans un document JSON. Reportez-vous alors à [cet article](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md). Celui-ci contient une procédure pas à pas décrivant comment y parvenir à l’aide d’une activité de copie Azure Data Factory.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).