---
title: Copier des données dans Dynamics (Common Data Service)
description: Découvrez comment copier des données à partir de Microsoft Dynamics CRM ou Microsoft Dynamics 365 (Common Data Service) vers des banques de données réceptrices prises en charge ou à partir de banques de données sources prises en charge vers Dynamics CRM ou Dynamics 365 à l’aide de l’activité de copie disponible dans un pipeline de fabrique de données.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: d065439839ba5db479305ae81c61892cb5cf5e70
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929449"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Copier des données à partir et vers Dynamics 365 (Common Data Service) ou Dynamics CRM à l’aide d’Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis et vers Microsoft Dynamics 365 ou Microsoft Dynamics CRM. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données à partir de Dynamics 365 (Common Data Service) ou Dynamics CRM vers toute banque de données réceptrice prise en charge. Vous pouvez également copier des données à partir de n’importe quelle banque de données source prise en charge vers Dynamics 365 (Common Data Service) ou Dynamics CRM. Pour obtenir la liste des magasins de données pris en charge en tant que sources ou récepteurs pour l’activité de copie, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Ce connecteur Dynamics prend en charge Dynamics 7.x à 9.x, aussi bien en ligne qu’en local. Plus précisément :

- La version 7.x mappe vers Dynamics CRM 2015
- La version 8.x mappe vers Dynamics CRM 2016 et la version anticipée de Dynamics 365
- La version 9.x mappe vers la dernière version de Dynamics 365

Consultez le tableau suivant sur les configurations et les types d’authentification pris en charge pour en savoir plus sur les versions/produits Dynamics respectifs. (IFD est l’abréviation d’« Internet Facing Deployment ») (déploiement avec accès via Internet).

| Versions de Dynamics | Types d’authentification | Exemples de services liés |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 (en ligne) <br> Dynamics CRM en ligne | Principal de service AAD <br> office365 | [Dynamics en ligne + principal de service AAD ou Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local avec IFD <br> Dynamics CRM 2016 local avec IFD <br> Dynamics CRM 2015 local avec IFD | IFD | [Dynamics local avec IFD + authentification IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Plus spécifiquement pour Dynamics 365, les types d’applications suivants sont pris en charge :

- Dynamics 365 pour les ventes
- Dynamics 365 pour le service client
- Dynamics 365 pour le service après-vente
- Dynamics 365 pour l’automatisation de service de projet
- Dynamics 365 pour le marketing

Les autres types d’applications, par exemple, Finance and Operations, Talent, etc., ne sont pas pris en charge par ce connecteur.

Ce connecteur Dynamics se base sur les [outils Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Pour copier des données issues de **Dynamics 365 for Finance and Operations**, vous pouvez utiliser le [connecteur Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques de Dynamics.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Dynamics sont les suivantes :

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 et Dynamics CRM en ligne

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Dynamics**, **DynamicsCrm** ou **CommonDataServiceForApps**. | OUI |
| deploymentType | Type de déploiement de l’instance Dynamics. Il doit être **« en ligne »** pour Dynamics en ligne. | OUI |
| serviceUri | L’URL de service de votre instance Dynamics, par exemple `https://adfdynamics.crm.dynamics.com`. | OUI |
| authenticationType | Type d’authentification pour se connecter à un serveur Dynamics. Les valeurs autorisées sont les suivantes : **AADServicePrincipal** ou **« Office365 »** . | OUI |
| servicePrincipalId | Spécifiez l’ID de l’application Azure Active Directory. | Oui, en utilisant l’authentification `AADServicePrincipal` |
| servicePrincipalCredentialType | Spécifiez le type d’informations d’identification à utiliser pour l’authentification de principal du service. Les valeurs autorisées sont les suivantes : **ServicePrincipalKey** ou **ServicePrincipalCert**. | Oui, en utilisant l’authentification `AADServicePrincipal` |
| servicePrincipalCredential | Spécifier les informations d'identification du principal de service. <br>Lors de l’utilisation de `ServicePrincipalKey` comme type d’informations d’identification, `servicePrincipalCredential` peut être une chaîne (ADF le chiffrera lors du déploiement du service lié) ou une référence à un secret dans AKV. <br>Lors de l’utilisation de `ServicePrincipalCert` comme informations d’identification, `servicePrincipalCredential` doit être une référence à un certificat dans AKV. | Oui, en utilisant l’authentification `AADServicePrincipal` | 
| username | Indiquez le nom d'utilisateur à utiliser pour se connecter à Dynamics. | Oui en utilisant l’authentification `Office365` |
| password | Indiquez le mot de passe du compte d’utilisateur défini pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui en utilisant l’authentification `Office365` |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non pour la source, oui pour le récepteur si le service lié à la source n’a pas de runtime d’intégration |

>[!NOTE]
>Le connecteur Dynamics utilisé pour la propriété facultative « organizationName » afin d’identifier votre instance de Dynamics CRM/365 Online. Pendant qu’il continue de fonctionner, vous êtes invité à spécifier à la place la nouvelle propriété « serviceUri » pour obtenir de meilleures performances pour l’instance de détection.

**Exemple : Dynamics en ligne à l’aide du principal de service AAD + l’authentification de la clé**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**Exemple : Dynamics en ligne à l’aide du principal de service AAD + l’authentification de certificat**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**Exemple : Dynamics en ligne utilisant l’authentification Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 et Dynamics CRM locaux avec IFD

*Les propriétés supplémentaires comparables à celles de Dynamics en ligne sont « hostName » et « port ».*

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Dynamics**, **DynamicsCrm** ou **CommonDataServiceForApps**. | OUI |
| deploymentType | Type de déploiement de l’instance Dynamics. Cela doit être **« OnPremisesWithIfd »** pour Dynamics local avec IFD.| OUI |
| hostName | Nom d’hôte du serveur Dynamics local. | OUI |
| port | Port du serveur Dynamics local. | Non, la valeur par défaut est 443 |
| organizationName | Nom d’organisation de l’instance Dynamics. | OUI |
| authenticationType | Type d’authentification pour se connecter au serveur Dynamics. Spécifiez **« Ifd »** pour Dynamics local avec IFD. | OUI |
| username | Indiquez le nom d'utilisateur à utiliser pour se connecter à Dynamics. | OUI |
| password | Indiquez le mot de passe du compte d’utilisateur défini pour le nom d’utilisateur. Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie en tirer (pull) les données lors de la copie. Pour plus d’informations, consultez la page [Stocker des informations d’identification dans Key Vault](store-credentials-in-key-vault.md). | OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non pour Source, Oui pour Récepteur |

**Exemple : Dynamics local avec IFD utilisant l’authentification IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Dynamics.

Pour copier des données depuis et vers Dynamics, les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **DynamicsEntity**, **DynamicsCrmEntity** ou **CommonDataServiceForAppsEntity**. |OUI |
| entityName | Nom logique de l’entité à récupérer. | Non pour la source (si « query » est spécifié dans la source de l’activité) ; Oui pour le récepteur |

**Exemple :**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par les types de source et de récepteur Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics en tant que type de source

Pour copier des données depuis Dynamics, les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source de l’activité de copie doit être définie sur **DynamicsSource**, **DynamicsCrmSource** ou **CommonDataServiceForAppsSource**. | OUI |
| query | FetchXML est un langage de requête propriétaire qui est utilisé dans Dynamics (en ligne et local). Consultez l’exemple qui suit. Pour en savoir plus, consultez [Générer des requêtes avec FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Non (si « entityName » est spécifié dans le jeu de données) |

>[!NOTE]
>La colonne PK sera toujours copiée, même si la projection de colonne que vous avez configurée dans la requête FetchXML ne la contient pas.

> [!IMPORTANT]
>- Quand vous copiez des données depuis Dynamics, le mappage de colonnes explicite de Dynamics vers le récepteur est facultatif mais vivement recommandé pour garantir un résultat de copie déterministe.
>- Quand vous importez un schéma dans l’interface utilisateur de création, ADF déduit le schéma en échantillonnant les premières lignes du résultat de requête Dynamics pour initialiser la liste de colonne source, dans laquelle les colonnes de cas sans valeurs dans les premières lignes seront omises. Le même comportement s’applique aux exécutions de copies en l’absence d’un mappage explicite. Vous pouvez examiner le mappage et y ajouter des colonnes, ce qui sera respecté pendant l’exécution de la copie.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Exemple de requête FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics comme type de récepteur

Pour copier des données dans Dynamics, les propriétés suivantes sont prises en charge dans la section **récepteur** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du récepteur d’activité de copie doit être définie sur **DynamicsSink**, **DynamicsCrmSink**ou **CommonDataServiceForAppsSink**. | OUI |
| writeBehavior | Comportement d’écriture de l’opération.<br/>La valeur autorisée est **« Upsert »** . | OUI |
| alternateKeyName | Spécifiez le nom de clé de remplacement défini sur votre entité pour exécuter « Upsert ». | Non |
| writeBatchSize | Nombre de lignes de données écrites dans Dynamics pour chaque lot. | Non (valeur par défaut : 10) |
| ignoreNullValues | Indique si les valeurs Null des données d’entrée (à l’exception des champs clés) doivent être ignorées pendant une opération d’écriture.<br/>Les valeurs autorisées sont **true** et **false**.<br>- **True** : Laisser inchangées les données dans l’objet de destination quand vous effectuez une opération upsert/mise à jour. Insérer une valeur définie par défaut lorsque vous effectuez une opération insert.<br/>- **False** : Mettre à jour les données dans l’objet de destination quand vous effectuez une opération upsert/mise à jour. Insérer une valeur NULL lorsque vous effectuez une opération insert. | Non (valeur par défaut : false) |

>[!NOTE]
>La valeur par défaut du récepteur **writeBatchSize** et de l’activité de copie **[parallelCopies](copy-activity-performance.md#parallel-copy)** pour le récepteur Dynamics est de 10. Par conséquent, 100 enregistrements sont soumis simultanément à Dynamics.

Pour Dynamics 365 (en ligne), il existe une limite de [2 appels simultanés de lot par organisation](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Si cette limite est dépassée, une erreur « Serveur occupé » est levée avant l’exécution de la première demande. Conservez une valeur « writeBatchSize » inférieure ou égale à 10 pour éviter la limitation des appels simultanés.

La combinaison optimale des paramètres « **writeBatchSize** » et « **parallelCopies** » dépend du schéma de votre entité, par exemple du nombre de colonnes, de la taille de ligne et du nombre de plug-ins/workflows/activités de workflow rattachés à ces appels, etc. Selon le service Dynamics, le paramètre par défaut 10 writeBatchSize * 10 parallelCopies est recommandé, car il fonctionne pour la plupart des entités Dynamics, bien qu’il ne garantisse pas des performances optimales. Vous pouvez améliorer les performances en ajustant cette combinaison dans les paramètres de votre activité de copie.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mappage de type de données pour Dynamics

Lorsque vous copiez de données de Dynamics, les mappages suivants sont utilisés entre les types de données Dynamics et les types de données intermédiaires Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

Configurez le type de données Data Factory correspondant dans la structure du jeu de données en fonction du type de données Dynamics de votre source à l’aide des tables de mappage suivantes :

| Type de données Dynamics | Type de données intermédiaires d’Azure Data Factory | Prise en charge en tant que source | Prise en charge en tant que récepteur |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Chaîne | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (avec une seule cible associée) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | Chaîne | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Chaîne | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Les types de données AttributeType.CalendarRules, AttributeType.MultiSelectPicklist et AttributeType.PartyList de Dynamics ne sont pas pris en charge.

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
