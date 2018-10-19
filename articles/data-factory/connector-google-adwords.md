---
title: Copier des données de Google AdWords à l’aide d’Azure Data Factory (préversion) | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de Google AdWords vers des banques de données réceptrices prises en charge.
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
ms.date: 09/07/2018
ms.author: jingwang
ms.openlocfilehash: 402d4fb0c1eb7c6760f800bdd408e9a4d8161ccc
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095964"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Copier des données de Google AdWords à l’aide d’Azure Data Factory (préversion)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de Google AdWords. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!IMPORTANT]
> Ce connecteur est actuellement en préversion. Essayez-le et envoyez-nous vos commentaires. Si vous souhaitez établir une dépendance sur les connecteurs en préversion dans votre solution, veuillez contacter le [support Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données depuis Google AdWords vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Google AdWords.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Google AdWords sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **GoogleAdWords** | Oui |
| clientCustomerID | ID client du client du compte AdWords pour lequel vous souhaitez extraire des données de rapport.  | Oui |
| developerToken | Jeton de développeur associé au compte de gestionnaire que vous utilisez pour accorder l’accès à l’API AdWords.  Vous pouvez choisir de marquer ce champ comme un SecureString pour le stocker de manière sécurisée dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie du fichier de définition d’application en tirer (pull) les données lors de la copie des données. Pour plus d’informations, consultez [Stocker les informations d’identification dans le coffre de clés](store-credentials-in-key-vault.md). | Oui |
| authenticationType | Mécanisme d’authentification OAuth 2.0 utilisé pour l’authentification. ServiceAuthentication ne peut être utilisé que sur un runtime d’intégration auto-hébergé. <br/>Les valeurs autorisées sont : **ServiceAuthentication**, **UserAuthentication** | Oui |
| refreshToken | Jeton d’actualisation obtenu depuis Google pour autoriser l’accès à AdWords pour UserAuthentication. Vous pouvez choisir de marquer ce champ comme un SecureString pour le stocker de manière sécurisée dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie du fichier de définition d’application en tirer (pull) les données lors de la copie des données. Pour plus d’informations, consultez [Stocker les informations d’identification dans le coffre de clés](store-credentials-in-key-vault.md). | Non  |
| clientId | Id client de l’application google utilisée pour acquérir le jeton d’actualisation. Vous pouvez choisir de marquer ce champ comme un SecureString pour le stocker de manière sécurisée dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie du fichier de définition d’application en tirer (pull) les données lors de la copie des données. Pour plus d’informations, consultez [Stocker les informations d’identification dans le coffre de clés](store-credentials-in-key-vault.md). | Non  |
| clientSecret | Clé secrète client de l’application google utilisée pour acquérir le jeton d’actualisation. Vous pouvez choisir de marquer ce champ comme un SecureString pour le stocker de manière sécurisée dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie du fichier de définition d’application en tirer (pull) les données lors de la copie des données. Pour plus d’informations, consultez [Stocker les informations d’identification dans le coffre de clés](store-credentials-in-key-vault.md). | Non  |
| email | ID d’e-mail du compte de service utilisé pour ServiceAuthentication et qui ne peut être utilisé que sur un runtime d’intégration auto-hébergé.  | Non  |
| keyFilePath | Chemin complet du fichier de clé .p12 utilisé pour authentifier l’adresse e-mail du compte de service et qui ne peut être utilisé que sur un runtime d’intégration auto-hébergé.  | Non  |
| trustedCertPath | Chemin d’accès complet du fichier .pem contenant les certificats d’autorité de certification approuvés permettant de vérifier le serveur en cas de connexion via SSL. Cette propriété n’est disponible que si le protocole SSL est utilisé sur un runtime d’intégration auto-hébergé. Valeur par défaut : le fichier cacerts.pem installé avec le runtime d’intégration.  | Non  |
| useSystemTrustStore | Indique s’il faut utiliser un certificat d’autorité de certification provenant du magasin de confiance du système ou d’un fichier PEM spécifié. La valeur par défaut est false.  | Non  |

**Exemple :**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                 "type": "SecureString",
                 "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refreshToken>"
            },
            "clientId": {
                 "type": "SecureString",
                 "value": "<clientId>"
            },
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Google AdWords.

Pour copier des données à partir de Google AdWords, définissez la propriété type du jeu de données sur **GoogleAdWordsObject**. Il n’y a aucune autre propriété propre au type dans cette sorte de jeu de données.

**Exemple**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords comme source

Pour copier des données à partir de Google AdWords, définissez le type de source sur **GoogleAdWordsSource** dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **GoogleAdWordsSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | OUI |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
