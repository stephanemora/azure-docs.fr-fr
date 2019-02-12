---
title: Copier des données depuis et vers Oracle à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser Data Factory pour copier des données de banque de données sources prises en charge vers une base de données Oracle ou d’Oracle vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 64f348880bf8872c61a1d1c90930c25ce9551bbf
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658027"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copier des données depuis/vers Oracle à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-onprem-oracle-connector.md)
> * [Version actuelle](connector-oracle.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis/vers une base de données Oracle. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une base de données Oracle vers toute banque de données réceptrice prise en charge. Vous pouvez également copier des données de n’importe quel magasin de données source pris en charge vers une base de données Oracle. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Oracle prend en charge les versions suivantes d’une base de données Oracle. Il prend également en charge les authentifications De base ou OID :

- Oracle 12c R1 (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10.1, 10.2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

> [!Note]
> Le serveur proxy Oracle n’est pas pris en charge.

## <a name="prerequisites"></a>Prérequis

Pour copier des données depuis et vers une base de données Oracle qui n’est pas accessible publiquement, vous devez configurer un runtime d’intégration auto-hébergé. Pour plus d’informations sur le runtime d’intégration, consultez l’article relatif au [runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md). Le runtime d’intégration intègre un pilote Oracle. Par conséquent, vous n’avez pas besoin d’installer manuellement un pilote pour copier des données depuis et vers Oracle.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Oracle.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Oracle sont les suivantes.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Oracle**. | Oui |
| connectionString | Spécifie les informations requises pour se connecter à l’instance Oracle Database. <br/>Marquez ce champ comme SecureString pour le stocker de façon sécurisée dans Data Factory. Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `password` de la chaîne de connexion. Pour plus d'informations, reportez-vous aux exemples suivants et à l'article [Stocker des informations d'identification dans Azure Key Vault](store-credentials-in-key-vault.md). <br><br>**Type de connexion pris en charge** : Vous pouvez utiliser le **SID Oracle** ou le **nom du service Oracle** pour identifier votre base de données :<br>- Si vous utilisez le SID : `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Si vous utilisez le nom du service : `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Oui |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser un runtime d’intégration auto-hébergé ou un runtime d’intégration Azure (si votre banque de données est accessible publiquement). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

>[!TIP]
>Si vous rencontrez le message d’erreur indiquant « ORA-01025: UPI parameter out of range » et que votre version d’Oracle est 8i, ajoutez `WireProtocolMode=1` à votre chaîne de connexion, puis réessayez.

**Pour activer le chiffrement sur la connexion Oracle**, deux options s’offrent à vous :

1.  Pour utiliser le **chiffrement 3DES (Triple-DES) et AES (Advanced Encryption Standard)**, du côté du serveur Oracle, accédez à Oracle Advanced Security (OAS) et configurez les paramètres de chiffrement. Consultez les détails [ici](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Le connecteur Oracle ADF négocie automatiquement la méthode de chiffrement pour utiliser celle que vous configurez dans OAS lors de l’établissement de connexion à Oracle.

2.  Pour utiliser **SSL**, suivez les étapes ci-dessous :

    1.  Obtenez des informations de certificat SSL. Obtenez les informations de certificat encodé DER de votre certificat SSL et enregistrez la sortie (---Begin Certificate ... End Certificate---) sous forme de fichier texte.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exemple :** extrait les informations de certificat de DERcert.cer, puis enregistre la sortie dans cert.txt

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Créez le magasin de clés ou le fichier truststore. La commande suivante crée le fichier truststore avec ou sans mot de passe au format PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exemple :** crée un fichier truststore PKCS12 nommé MyTrustStoreFile avec un mot de passe

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Placez le fichier truststore sur la machine IR auto-hébergée, par exemple, à l’emplacement C:\MyTrustStoreFile.
    4.  Dans ADF, configurez la chaîne de connexion Oracle avec `EncryptionMethod=1` et la valeur `TrustStore`/`TrustStorePassword`correspondante, par exemple `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Exemple :**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : stockage du mot de passe dans Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Oracle.

Pour copier des données depuis et vers Oracle, affectez la valeur **OracleTable** à la propriété type du jeu de données. Les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **OracleTable**. | Oui |
| TableName |Nom de la table dans la base de données Oracle à laquelle le service lié fait référence. | Oui |

**Exemple :**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Oracle en tant que source et récepteur.

### <a name="oracle-as-a-source-type"></a>Oracle comme un type de source

Pour copier des données d’Oracle, définissez le type de source dans l’activité de copie sur **OracleSource**. Les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **OracleSource**. | Oui |
| oracleReaderQuery | Utiliser la requête SQL personnalisée pour lire les données. Par exemple `"SELECT * FROM MyTable"`. | Non  |

Si vous ne spécifiez pas « oracleReaderQuery », les colonnes définies dans la section « structure » du jeu de données sont utilisées pour construire une requête (`select column1, column2 from mytable`) à exécuter sur la base de données Oracle. Si la définition du jeu de données ne possède pas de « structure », toutes les colonnes de la table sont sélectionnées.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-a-sink-type"></a>Oracle comme type de récepteur

Pour copier des données vers Oracle, définissez **OracleSink** comme type de récepteur dans l’activité de copie. Les propriétés suivantes sont prises en charge dans la section **récepteur** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du récepteur d’activité de copie doit être définie sur **OracleSink**. | Oui |
| writeBatchSize | Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize<br/>Valeurs autorisées : integer (nombre de lignes). |Non (valeur par défaut : 10 000) |
| writeBatchTimeout | Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br/>Valeurs autorisées : timespan. Exemple : 00:30:00 (30 minutes). | Non  |
| preCopyScript | Spécifiez une requête SQL pour l’activité de copie à exécuter avant l’écriture de données dans Oracle à chaque exécution. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Mappage de type de données pour Oracle

Lorsque vous copiez des données depuis et vers Oracle, les mappages suivants sont utilisés entre les types de données Oracle et les types de données intermédiaires Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type de données Oracle | Type de données intermédiaires d’Azure Data Factory |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(seulement pris en charge sur Oracle 10g et les versions ultérieures) |
| CHAR |Chaîne |
| CLOB |Chaîne |
| DATE |DateTime |
| FLOAT |Décimale, chaîne (si précision > 28) |
| INTEGER |Décimale, chaîne (si précision > 28) |
| LONG |Chaîne |
| LONG RAW |Byte[] |
| NCHAR |Chaîne |
| NCLOB |Chaîne |
| NUMBER |Décimale, chaîne (si précision > 28) |
| NVARCHAR2 |Chaîne |
| RAW |Byte[] |
| ROWID |Chaîne |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |Chaîne |
| TIMESTAMP WITH TIME ZONE |Chaîne |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |Chaîne |
| XML |Chaîne |

> [!NOTE]
> Les types de données INTERVAL YEAR TO MONTH et INTERVAL DAY TO SECOND ne sont pas pris en charge.


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
