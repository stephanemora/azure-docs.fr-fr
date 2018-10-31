---
title: Copier des données à partir d’une source HTTP à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données d’une source HTTP dans le cloud ou locale vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/24/2018
ms.author: jingwang
ms.openlocfilehash: 1f3e9be3a0048c4bf2e87ac23cbdc76b1aaa649f
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166404"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Copier des données d’un point de terminaison HTTP à l’aide d’Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-http-connector.md)
> * [Version actuelle](connector-http.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis un point de terminaison HTTP. Il s’appuie sur l’article [Activité de copie dans Azure Data Factory](copy-activity-overview.md), qui constitue une présentation de l’activité de copie.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une source HTTP vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Vous pouvez utiliser ce connecteur HTTP pour :

- Récupérer des données à partir d’un point de terminaison HTTP/S à l’aide des méthodes HTTP **GET** ou **POST**.
- Récupérer des données à l’aide de l’une des authentifications suivantes : **Anonyme**, **De base**, **Digest**, **Windows** ou **ClientCertificate**.
- Copier la réponse HTTP en l’état ou l’analyser en utilisant les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

Ce connecteur diffère du [connecteur de table web](connector-web-table.md) en ce que ce dernier est utilisé pour extraire le contenu de tables d’une page web HTML.

> [!TIP]
> Pour tester une requête HTTP pour la récupération de données avant de configurer le connecteur HTTP dans Data Factory, obtenez des informations à partir de la spécification d’API sur les exigences d’en-tête et de corps. Vous pouvez vous servir d’outils tels que Postman ou un navigateur web pour valider.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur HTTP.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié HTTP sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **HttpServer**. | Oui |
| url | URL de base du serveur web. | Oui |
| enableServerCertificateValidation | Spécifiez si vous souhaitez activer la validation du certificat SSL du serveur lors de la connexion à un point de terminaison HTTP. Si votre serveur HTTPS utilise un certificat auto-signé, affectez à cette propriété la valeur **false**. | Non <br /> (la valeur par défaut est **true**) |
| authenticationType | Spécifie le type d’authentification. Les valeurs autorisées sont : **Anonyme**, **De base**, **Digest**, **Windows** et **ClientCertificate**. <br><br> Consultez les sections à la suite de ce tableau pour accéder à d’autres propriétés et à des exemples JSON relatifs à ces types d’authentification. | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion au magasin de données. Vous pouvez utiliser Azure Integration Runtime ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. À défaut de spécification, cette propriété utilise Azure Integration Runtime par défaut. |Non  |

### <a name="using-basic-digest-or-windows-authentication"></a>Utilisation de l’authentification Basic (De base), Digest ou Windows

Définissez la valeur de la propriété **authenticationType** sur **De base**, **Digest** ou **Windows**. Outre les propriétés génériques décrites dans la section précédente, spécifiez les propriétés suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| userName | Nom d’utilisateur à utiliser pour accéder au point de terminaison HTTP. | Oui |
| password | Mot de passe de l’utilisateur (valeur **userName**). Vous pouvez marquer ce champ en tant que type **SecureString** pour le stocker de manière sécurisée dans Data Factory. Vous pouvez également [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |

**Exemple**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Utilisation de l’authentification ClientCertificate (Certificat client)

Pour utiliser l’authentification ClientCertificate, définissez la valeur de la propriété **authenticationType** sur **ClientCertificate**. Outre les propriétés génériques décrites dans la section précédente, spécifiez les propriétés suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| embeddedCertData | Données du certificat encodé en Base64. | Spécifiez **embeddedCertData** ou **certThumbprint**. |
| certThumbprint | Empreinte numérique du certificat installé dans le magasin de certificats de votre ordinateur exécutant le runtime d’intégration auto-hébergé. S’applique uniquement quand le type auto-hébergé du runtime d’intégration est spécifié dans la propriété **connectVia**. | Spécifiez **embeddedCertData** ou **certThumbprint**. |
| password | Mot de passe associé au certificat. Vous pouvez marquer ce champ en tant que type **SecureString** pour le stocker de manière sécurisée dans Data Factory. Vous pouvez également [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non  |

Si vous utilisez **certThumbprint** pour l’authentification et que le certificat est installé dans le magasin personnel de l’ordinateur local, accordez des autorisations de lecture au runtime d’intégration auto-hébergé :

1. Ouvrez MMC (Microsoft Management Console). Ajoutez le composant logiciel enfichable **Certificats** ciblant **Ordinateur local**.
2. Développez **Certificats** > **Personnel**, puis sélectionnez **Certificats**.
3. Cliquez avec le bouton droit sur le certificat du magasin personnel, puis sélectionnez **Toutes les tâches** > **Gérer les clés privées**.
3. Sous l’onglet **Sécurité**, ajoutez le compte d’utilisateur sous lequel le service hôte du runtime d’intégration (DIAHostService) s’exécute avec l’accès en lecture au certificat.

**Exemple 1 : Utilisation de certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : Utilisation d’embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

Cette section contient la liste des propriétés prises en charge par le jeu de données HTTP. 

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données et services liés](concepts-datasets-linked-services.md). 

Pour copier des données de HTTP, définissez la propriété **type** du jeu de données sur **HttpFile**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** du jeu de données doit être définie sur **HttpFile**. | Oui |
| relativeUrl | URL relative de la ressource qui contient les données. Quand cette propriété n’est pas spécifiée, seule l’URL indiquée dans la définition du service lié est utilisée. | Non  |
| requestMethod | Méthode HTTP. Les valeurs autorisées sont **Get** (par défaut) et **Post**. | Non  |
| additionalHeaders | En-têtes de requête HTTP supplémentaires. | Non  |
| RequestBody | Corps de la requête HTTP. | Non  |
| format | Si vous souhaitez récupérer des données du point de terminaison HTTP en l’état sans les analyser, puis les copier dans un magasin basé sur un fichier, ignorez la section **format** dans les définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser le contenu de la réponse HTTP pendant la copie, les types de formats de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Sous **format**, définissez la propriété **type** sur l’une de ces valeurs. Pour plus d’informations, consultez [Format JSON](supported-file-formats-and-compression-codecs.md#json-format), [Format Texte](supported-file-formats-and-compression-codecs.md#text-format), [Format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [Format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non  |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Types pris en charge : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Niveaux pris en charge : **Optimal** et **Fastest**. |Non  |

> [!NOTE]
> La taille de charge utile de requête HTTP prise en charge est d’environ 500 Ko. Si la taille de charge utile que vous souhaitez passer au point de terminaison web est supérieure à 500 Ko, envisagez de la traiter en blocs plus petits.

**Exemple 1 : Utilisation de la méthode Get (par défaut)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Exemple 2 : Utilisation de la méthode Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Cette section contient la liste des propriétés prises en charge par la source HTTP.

Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP en tant que source

Pour copier des données de HTTP, définissez le **type de source** dans l’activité de copie sur **HttpSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** de la source de l’activité de copie doit être définie sur **HttpSource**. | Oui |
| httpRequestTimeout | Délai d’expiration (valeur **TimeSpan**) pour l’obtention d’une réponse par la requête HTTP. Cette valeur correspond au délai d’expiration pour l’obtention d’une réponse, et non au délai d’expiration pour la lecture des données de la réponse. La valeur par défaut est **00:01:40**.  | Non  |

**Exemple**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
