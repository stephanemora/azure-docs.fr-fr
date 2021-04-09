---
title: Copier et transformer des données dans Azure Database pour PostgreSQL
description: Apprenez à copier et à transformer des données dans Azure Database pour PostgreSQL à l’aide d’Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/25/2021
ms.openlocfilehash: ec4ea645e325ef48d4cb5951cd39fd4e9cbe1617
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738053"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Copier et transformer des données dans Azure Database pour PostgreSQL à l’aide d’Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article indique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis et vers Azure Database pour PostgreSQL, et utiliser Data Flow pour transformer les données dans Azure Database pour PostgreSQL. Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

Ce connecteur est spécialisé pour le [service Azure Database pour PostgreSQL](../postgresql/overview.md). Pour copier des données à partir d’une base de données PostgreSQL générique locale ou située dans le cloud, utilisez le [connecteur PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Azure Database pour PostgreSQL est pris en charge pour les activités suivantes :

- [Activité de copie](copy-activity-overview.md) avec [prise en charge de la matrice source/récepteur](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Actuellement, le flux de données prend en charge un serveur unique Azure Database pour PostgreSQL, mais pas un serveur flexible ou hyperscale (Citus).

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes détaillent les propriétés utilisées pour définir des entités Data Factory propres au connecteur Azure Database pour PostgreSQL.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Azure Database pour PostgreSQL :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **AzurePostgreSql**. | Oui |
| connectionString | Chaîne de connexion ODBC permettant de se connecter à Azure Database pour PostgreSQL.<br/>Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `password` de la chaîne de connexion. Pour plus d’informations, consultez les exemples suivants et [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| connectVia | Cette propriété correspond au [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

Voici un exemple de chaîne de connexion typique : `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Selon votre cas de figure, vous pouvez définir d’autres propriétés :

| Propriété | Description | Options | Obligatoire |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| La méthode utilisée par le pilote pour chiffrer les données envoyées entre le pilote et le serveur de base de données. Par exemple, `EncryptionMethod=<0/1/6>;`| 0 (aucun chiffrement) **(par défaut)** / 1 (SSL) / 6 (RequestSSL) | Non |
| ValidateServerCertificate (VSC) | Détermine si le pilote valide le certificat envoyé par le serveur de base de données lorsque le chiffrement SSL est activé (EncryptionMethod = 1). Par exemple, `ValidateServerCertificate=<0/1>;`| 0 (désactivé) **(par défaut)** / 1 (activé) | Non |

**Exemple** :

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Exemple** :

***Stockage du mot de passe dans Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données dans Azure Data Factory](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure Database pour PostgreSQL.

Pour copier des données d’Azure Database pour PostgreSQL, affectez la valeur **AzurePostgreSqlTable** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **AzurePostgreSqlTable** | Oui |
| tableName | Nom de la table | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple** :

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez [Pipelines et activités dans Azure Data Factory](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par une source Azure Database pour PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database pour PostgreSQL en tant que source

Pour copier des données d’Azure Database pour PostgreSQL, affectez la valeur **AzurePostgreSqlSource** au type source de l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source de l’activité de copie doit être définie sur **AzurePostgreSqlSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple, `SELECT * FROM mytable` ou `SELECT * FROM "MyTable"`. Notez que dans PostgreSQL, le nom de l’entité est traité comme insensible à la casse s’il n’est pas placé entre guillemets. | Non (si la propriété tableName du jeu de données est spécifiée) |

**Exemple** :

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database pour PostgreSQL en tant que récepteur

Pour copier des données vers Azure Database pour PostgreSQL, les propriétés suivantes sont prises en charge dans la section **sink** de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du récepteur d’activité de copie doit être définie sur **AzurePostgreSQLSink**. | Oui |
| preCopyScript | Spécifiez une requête SQL pour l’activité de copie à exécuter avant d'écrire des données dans Azure Database pour PostgreSQL à chaque exécution. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. | Non |
| writeMethod | Méthode utilisée pour écrire des données dans Azure Database pour PostgreSQL.<br>Les valeurs autorisées sont les suivantes : **CopyCommand** (préversion, qui est plus performante), **BulkInsert** (par défaut). | Non |
| writeBatchSize | Nombre de lignes chargées dans Azure Database pour PostgreSQL par lot.<br>La valeur autorisée est un entier qui représente le nombre de lignes. | Non (valeur par défaut : 1 000 000) |
| writeBatchTimeout | Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br>Les valeurs autorisées sont des intervalles de temps. Exemple : 00:30:00 (30 minutes). | Non (valeur par défaut : 00:30:00) |

**Exemple** :

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Lors de la transformation de données dans le flux de données de mappage, vous pouvez lire et écrire dans des tables à partir d’Azure Database pour PostgreSQL. Pour plus d’informations, consultez la [transformation de la source](data-flow-source.md) et la [transformation du récepteur](data-flow-sink.md) dans le flux de données de mappage. Vous pouvez choisir d’utiliser un jeu de données Azure Database pour PostgreSQL ou un [jeu de données inlined](data-flow-source.md#inline-datasets) en tant que type de source et de récepteur.

### <a name="source-transformation"></a>Transformation de la source

Le tableau ci-dessous répertorie les propriétés prises en charge par une source Azure Database pour PostgreSQL. Vous pouvez modifier ces propriétés sous l’onglet **Options de la source**.

| Nom | Description | Obligatoire | Valeurs autorisées | Propriété du script de flux de données |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Table de charge de travail | Si vous sélectionnez Table comme entrée, le flux de données extrait toutes les données de la table spécifiée dans le jeu de données. | Non | - |*(pour le jeu de données inlined uniquement)*<br>tableName |
| Requête | Si vous sélectionnez Requête comme entrée, spécifiez une requête SQL pour extraire des données de la source, qui remplace toute table que vous spécifiez dans le jeu de données. L’utilisation de requêtes est un excellent moyen de réduire le nombre de lignes pour les tests ou les recherches.<br><br>La clause **Order By** n’est pas prise en charge, mais vous pouvez définir une instruction SELECT FROM complète. Vous pouvez également utiliser des fonctions de table définies par l’utilisateur. **select * from udfGetData()** est une fonction UDF dans SQL qui retourne une table que vous pouvez utiliser dans le flux de données.<br>Exemple de requête : `select * from mytable where customerId > 1000 and customerId < 2000` ou `select * from "MyTable"`. Notez que dans PostgreSQL, le nom de l’entité est traité comme insensible à la casse s’il n’est pas placé entre guillemets.| Non | String | query |
| Taille du lot | Spécifiez la taille que doivent avoir les lots créés à partir d’un large volume de données. | Non | Integer | batchSize |
| Niveau d’isolation | Choisissez l’un des niveaux d’isolation suivants :<br>– Lecture validée.<br>– Lecture non validée (par défaut).<br>– Lecture renouvelable.<br>– Sérialisable.<br>– Aucun (ignorer le niveau d’isolation). | Non | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Exemple de script de source Azure Database pour PostgreSQL

Quand vous utilisez Azure Database pour PostgreSQL comme type de source, le script de flux de données associé est le suivant :

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Transformation du récepteur

Le tableau ci-dessous répertorie les propriétés prises en charge par un récepteur Azure Database pour PostgreSQL. Vous pouvez modifier ces propriétés sous l’onglet **Options du récepteur**.

| Name | Description | Obligatoire | Valeurs autorisées | Propriété du script de flux de données |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Mettre à jour la méthode | Spécifiez les opérations autorisées sur la destination de votre base de données. Par défaut, seules les insertions sont autorisées.<br>Pour mettre à jour, effectuer un upsert ou supprimer des lignes, une [transformation de modification de ligne](data-flow-alter-row.md) est requise afin de baliser les lignes relatives à ces actions. | Oui | `true` ou `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| Colonnes clés | Pour les mises à jour, les opérations upsert et les suppressions, une ou plusieurs colonnes clés doivent être définies afin de déterminer la ligne à modifier.<br>Le nom de colonne que vous choisissez comme clé sera utilisé dans le cadre des opérations suivantes de mise à jour, d’upsert et de suppression. Vous devez donc choisir une colonne qui existe dans le mappage du récepteur. | Non | Array | clés |
| Ignorer l’écriture des colonnes clés | Si vous ne souhaitez pas écrire la valeur dans la colonne clé, sélectionnez « Ignorer l’écriture des colonnes clés ». | Non | `true` ou `false` | skipKeyWrites |
| Action table |Détermine si toutes les lignes de la table de destination doivent être recréées ou supprimées avant l’écriture.<br>- **Aucun** : Aucune action ne sera effectuée sur la table.<br>- **Recréer** : La table sera supprimée et recréée. Obligatoire en cas de création dynamique d’une nouvelle table.<br>- **Tronquer** : Toutes les lignes de la table cible seront supprimées. | Non | `true` ou `false` | recreate<br/>truncate |
| Taille du lot | Spécifiez le nombre de lignes écrites dans chaque lot. Les plus grandes tailles de lot améliorent la compression et l’optimisation de la mémoire, mais risquent de lever des exceptions de type mémoire insuffisante lors de la mise en cache des données. | Non | Integer | batchSize |
| Pré et post-scripts SQL | Spécifiez des scripts SQL multilignes qui s’exécutent avant (prétraitement) et après (post-traitement) l’écriture de données dans votre base de données de réception. | Non | String | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Exemple de script de récepteur Azure Database pour PostgreSQL

Quand vous utilisez Azure Database pour PostgreSQL comme type de récepteur, le script de flux de données associé est le suivant :

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour plus d’informations sur les propriétés, consultez [Activité Lookup dans Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l'activité de copie d'Azure Data Factory, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
