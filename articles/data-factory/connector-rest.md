---
title: Copier des données d’une source REST à l’aide d’Azure Data Factory
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données d’une source REST dans le cloud ou locale vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 34abb93dd54245e03baaa6efe0130d951f7565bf
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927729"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Copier des données d’un point de terminaison REST à l’aide d’Azure Data Factory

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis un point de terminaison REST. Il s’appuie sur l’article [Activité de copie dans Azure Data Factory](copy-activity-overview.md), qui constitue une présentation de l’activité de copie.

Les différences entre ce connecteur REST, un [connecteur HTTP](connector-http.md) et le [connecteur Table Web](connector-web-table.md) sont les suivantes :

- Le **connecteur REST** prend spécifiquement en charge la copie de données à partir d’API RESTful. 
- Le **connecteur HTTP** est générique pour récupérer des données à partir de n’importe quel point de terminaison HTTP, par exemple pour télécharger un fichier. Avant que ce connecteur REST soit disponible, vous utiliserez peut-être un connecteur HTTP pour copier des données à partir d’une API RESTful, ce qui est pris en charge mais moins fonctionnel comparé au connecteur REST.
- Le **connecteur Table web** extrait le contenu de tables d’une page web HTML.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une source REST vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur REST générique prend en charge ce qui suit :

- La récupération des données à partir du point de terminaison REST à l’aide des méthodes **GET** ou **POST**.
- Le récupération de données avec une des authentifications suivantes : **Anonyme**, **De base**, **Principal de service AAD** et **Identités managées pour ressources Azure**.
- La **[Pagination](#pagination-support)**  dans les API REST.
- La copie de la réponse JSON REST [en l’état](#export-json-response-as-is) ou son analyse à l’aide d’un [mappage de schéma](copy-activity-schema-and-type-mapping.md#schema-mapping). Seule la charge utile de réponse dans **JSON** est prise en charge.

> [!TIP]
> Pour tester une requête pour l’extraction de données avant de configurer le connecteur REST dans Data Factory, obtenez des informations à partir de la spécification d’API sur les exigences d’en-tête et de corps. Vous pouvez vous servir d’outils tels que Postman ou un navigateur web pour valider.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur REST.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié REST sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** doit être définie sur **RestService**. | OUI |
| url | URL de base du service REST. | OUI |
| enableServerCertificateValidation | Indique s’il faut ou non valider le certificat SSL côté serveur lors de la connexion au point de terminaison. | Non<br /> (la valeur par défaut est **true**) |
| authenticationType | Type d’authentification utilisé pour se connecter au service REST. Les valeurs autorisées sont **Anonyme**, **De base**, **AadServicePrincipal** et **ManagedServiceIdentity**. Pour d’autres propriétés et exemples, voir les sections correspondantes ci-dessous. | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion au magasin de données. Pour plus d’informations, consultez la section [Prérequis](#prerequisites). À défaut de spécification, cette propriété utilise Azure Integration Runtime par défaut. |Non |

### <a name="use-basic-authentication"></a>Utiliser une authentification de base

Définissez la propriété **authenticationType** sur **De base**. Outre les propriétés génériques décrites dans la section précédente, spécifiez les propriétés suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| userName | Nom d’utilisateur à utiliser pour accéder au point de terminaison REST. | OUI |
| password | Mot de passe de l’utilisateur (valeur **userName**). Vous pouvez marquer ce champ en tant que type **SecureString** pour le stocker de manière sécurisée dans Data Factory. Vous pouvez également [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |

**Exemple**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>Utiliser une authentification de principal de service AAD

Définissez la propriété **authenticationType** sur **AadServicePrincipal**. Outre les propriétés génériques décrites dans la section précédente, spécifiez les propriétés suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| servicePrincipalId | Spécifiez l’ID de l’application Azure Active Directory. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application Azure Active Directory. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Récupérez-le en pointant la souris dans le coin supérieur droit du Portail Azure. | OUI |
| aadResourceId | Spécifiez la ressource AAD pour laquelle vous demandez une autorisation, par exemple, `https://management.core.windows.net`.| OUI |

**Exemple**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Utiliser des identités managées afin d’authentifier les ressources Azure

Définissez la propriété **authenticationType** sur **ManagedServiceIdentity**. Outre les propriétés génériques décrites dans la section précédente, spécifiez les propriétés suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| aadResourceId | Spécifiez la ressource AAD pour laquelle vous demandez une autorisation, par exemple, `https://management.core.windows.net`.| OUI |

**Exemple**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Cette section contient la liste des propriétés prises en charge par le jeu de données REST. 

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données et services liés](concepts-datasets-linked-services.md). 

Pour copier des données à partir de REST, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** du jeu de données doit être définie sur **RestResource**. | OUI |
| relativeUrl | URL relative de la ressource qui contient les données. Quand cette propriété n’est pas spécifiée, seule l’URL indiquée dans la définition du service lié est utilisée. Le connecteur HTTP copie les données à partir de l’URL combinée : `[URL specified in linked service]/[relative URL specified in dataset]`. | Non |

Si vous avez défini `requestMethod`, `additionalHeaders`, `requestBody` et `paginationRules` dans le jeu de données, il reste pris en charge tel quel, mais nous vous suggérons d’utiliser le nouveau modèle dans la source d’activité à l’avenir.

**Exemple :**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Cette section fournit la liste des propriétés prises en charge par la source REST.

Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST en tant que source

Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** de la source d’activité de copie doit être définie sur **RestSource**. | OUI |
| requestMethod | Méthode HTTP. Les valeurs autorisées sont **Get** (par défaut) et **Post**. | Non |
| additionalHeaders | En-têtes de requête HTTP supplémentaires. | Non |
| requestBody | Corps de la requête HTTP. | Non |
| paginationRules | Règles de pagination pour composer des requêtes de page suivantes. Pour plus de détails, voir la section [Prise en charge la pagination](#pagination-support). | Non |
| httpRequestTimeout | Délai d’expiration (valeur **TimeSpan**) pour l’obtention d’une réponse par la requête HTTP. Cette valeur correspond au délai d’expiration pour l’obtention d’une réponse, et non au délai d’expiration pour la lecture des données de la réponse. La valeur par défaut est **00:01:40**.  | Non |
| requestInterval | Durée d’attente avant d’envoyer la requête de page suivante. La valeur par défaut est **00:00:01** |  Non |

>[!NOTE]
>Le connecteur REST ignore tout en-tête « accepter » spécifié dans `additionalHeaders`. Comme le connecteur REST ne prend en charge que la réponse dans JSON, il génère automatiquement un en-tête de `Accept: application/json`.

**Exemple 1 : Utilisation de la méthode Get avec la pagination**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemple 2 : Utilisation de la méthode Post**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Prise en charge la pagination

Normalement, l’API REST limite sa taille de charge utile de réponse par demande à un nombre raisonnable. Pour retourner une grande quantité de données, elle fractionne le résultat en plusieurs pages et exige des appelants qu’ils envoient des demandes consécutives pour obtenir la page suivante du résultat. En règle générale, la requête d’une page est dynamique et composée par les informations retournées à partir de la réponse de la page précédente.

Ce connecteur REST générique prend en charge les modèles de pagination suivants : 

* URL absolue ou relative de la requête suivante = valeur de propriété dans le corps de la réponse en cours
* URL absolue ou relative de la requête suivante = valeur d’en-tête dans les en-têtes de la réponse en cours
* Paramètre de requête de la demande suivante = valeur de propriété dans le corps de la réponse en cours
* Paramètre de requête de la demande suivante = valeur d’en-tête dans les en-têtes de la réponse en cours
* En-tête de la requête suivante = valeur de propriété dans le corps de la réponse en cours
* En-tête de la requête suivante = valeur d’en-tête dans les en-têtes de la réponse en cours

Les **règles de pagination** sont définies en tant que dictionnaire dans un jeu de données qui contient une ou plusieurs paires clé-valeur respectant la casse. La configuration sera utilisée pour générer la requête à partir de la deuxième page. Le connecteur arrête les itérations quand il obtient le code d’état HTTP 204 (Aucun contenu) ou quand une expression JSONPath dans « paginationRules » retourne la valeur null.

**Clés prises en charge** dans les règles de pagination :

| Clé | Description |
|:--- |:--- |
| AbsoluteUrl | Indique l’URL pour l’émission de la requête suivante. Il peut s’agit d’une **URL absolue ou relative**. |
| QueryParameters.*request_query_parameter* OU QueryParameters[’request_query_parameter’] | « request_query_parameter » est défini par l’utilisateur et fait référence à un nom de paramètre de requête dans l’URL de la requête HTTP suivante. |
| Headers.*request_header* OR Headers[’request_header’] | « request_header » est défini par l’utilisateur et fait référence à un nom d’en-tête dans la requête HTTP suivante. |

**Valeurs prises en charge** dans les règles de pagination :

| Valeur | Description |
|:--- |:--- |
| Headers.*response_header* OR Headers[’response_header’] | « response_header » est défini par l’utilisateur et fait référence à un nom d’en-tête dans la réponse HTTP en cours, dont la valeur sera être utilisée pour émettre la requête suivante. |
| Expression JSONPath commençant par « $ » (représentant la racine du corps de la réponse) | Le corps de la réponse ne doit contenir qu’un seul objet JSON. L’expression JSONPath doit retourner une seule valeur primitive qui sera utilisée pour émettre la requête suivante. |

**Exemple :**

L’API Graph Facebook retourne une réponse dans la structure suivante. Dans ce cas, l’URL de la page suivante est représentée dans ***paging.next*** :

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

La configuration de la source de l’activité de copie REST correspondante, en particulier `paginationRules`, se présente comme suit :

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="export-json-response-as-is"></a>Exporter la réponse JSON en l’état

Vous pouvez utiliser ce connecteur REST pour exporter une réponse JSON d’API REST en l’état à différentes banques basées sur un fichier. Pour obtenir une telle copie indépendante du schéma, ignorez la section « structure » (également appelée *schéma*) dans le mappage de schéma et de jeu de données dans l’activité de copie.

## <a name="schema-mapping"></a>Mappage de schéma

Pour copier des données d’un point de terminaison REST vers un récepteur tabulaire, consultez [Mappage de schéma](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
