---
title: Copier des données à partir d’Office 365 à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données d’Office 365 vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 1a8d622aa280794d9a4d6fe7320ddcc21ac044f4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475653"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Copier des données à partir d’Office 365 dans Azure à l’aide d’Azure Data Factory

Azure Data Factory s’intègre à [de connexion Microsoft Graph data](https://docs.microsoft.com/graph/data-connect-concept-overview), ce qui vous permet de mettre les riches en données organisationnelles dans votre Office 365 client dans Azure de manière évolutive et créer des applications d’analytique et extraire des analyses en fonction de ces précieuses ressources de données. L’intégration avec Privileged Access Management fournit un contrôle d’accès sécurisé pour les données organisées dans Office 365.  Reportez-vous à [ce lien](https://docs.microsoft.com/graph/data-connect-concept-overview) pour une vue d’ensemble sur les données de Microsoft Graph se connecter et [ce lien](https://docs.microsoft.com/graph/data-connect-policies#licensing) pour les informations de licence.

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’Office 365. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge
Connecteur ADF Office 365 et les données de Microsoft Graph connectent permet à l’intégration à l’échelle de différents types de jeux de données à partir de boîtes aux lettres de l’extension de messagerie Exchange, y compris le carnet d’adresses, les événements de calendrier, messages électroniques, les informations utilisateur, paramètres de boîte aux lettres, et ainsi de suite.  Consultez [ici](https://docs.microsoft.com/graph/data-connect-datasets) pour afficher la liste complète des jeux de données disponibles.

Pour l’instant, au sein d’une seule activité de copie vous pouvez uniquement **copier des données à partir d’Office 365 dans [stockage Blob Azure](connector-azure-blob-storage.md), [Gen1 de stockage Azure Data Lake](connector-azure-data-lake-store.md), et [Azure Data Lake Storage Gen2 ](connector-azure-data-lake-storage.md) au format JSON** (de type setOfObjects). Si vous voulez charger des données Office 365 dans d’autres types de banques de données ou dans d’autres formats, vous pouvez faire suivre la première activité de copie d’une autre activité de copie pour charger les données dans une des [banques de données de destination ADF prises en charge](copy-activity-overview.md#supported-data-stores-and-formats) (reportez-vous à la colonne « prise en charge en tant que récepteur »du tableau « Banques de données et formats pris en charge »).

>[!IMPORTANT]
>- L’abonnement Azure contenant la fabrique de données et la banque de données réceptrice doit être sous le même locataire Azure Active Directory (Azure AD) que le locataire Office 365.
>- Vérifiez que la région Azure Integration Runtime utilisée pour l’activité de copie et la destination se trouvent dans la même région que celle où se trouve la boîte aux lettres des utilisateurs du locataire Office 365. Pour comprendre comment l’emplacement d’Azure Integration Runtime est déterminé, suivez [ce lien](concepts-integration-runtime.md#integration-runtime-location). Consultez [ce tableau](https://docs.microsoft.com/graph/data-connect-datasets#regions) pour obtenir la liste des régions Office et des régions Azure correspondantes prises en charge.
>- Authentification de principal du service est le seul mécanisme d’authentification pris en charge pour le stockage d’objets Blob Azure, Azure Data Lake Storage Gen1 et Gen2 de stockage Azure Data Lake comme banques de destination.

## <a name="prerequisites"></a>Conditions préalables

Pour copier des données depuis Office 365 dans Azure, vous devez effectuer les étapes prérequises suivantes :

- L’administrateur de votre locataire Office 365 doit effectuer des actions d’intégration comme décrit [ici](https://docs.microsoft.com/graph/data-connect-get-started).
- Créez et configurez une application web Azure AD dans Azure Active Directory.  Pour obtenir des instructions, consultez la page [Créer une application Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Prenez note des valeurs suivantes, que vous utiliserez afin de définir le service lié pour Office 365 :
    - ID de locataire. Pour obtenir des instructions, consultez [Obtenir l’ID de locataire](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - ID d’application et clé d’application.  Pour obtenir des instructions, consultez [Obtenir un ID d’application et une clé d’authentification](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Ajoutez l’identité de l’utilisateur effectuant la demande d’accès aux données en tant que propriétaire de l’application web Azure AD (à partir de l’application web Azure AD > Paramètres > Propriétaires > Ajouter un propriétaire). 
    - L’identité de l’utilisateur doit être dans l’organisation Office 365 à partir de laquelle vous obtenez des données et ne doit pas être un utilisateur Invité.

## <a name="approving-new-data-access-requests"></a>Approbation de nouvelles demandes d’accès aux données

Si c’est la première fois que vous demandez des données pour ce contexte (une combinaison de la table de données consultée, du compte de destination dans lequel les données sont chargées et de l’identité de l’utilisateur à l’origine de la demande d’accès aux données), vous voyez l’activité de copie avec l’état « En cours d’exécution », et l’état ne devient « RequestingConsent » que quand vous cliquez sur le [lien « Détails » sous Actions](copy-activity-overview.md#monitoring).  Un membre du groupe d’approbateurs pour l’accès aux données doit approuver la requête dans Privileged Access Management pour permettre l’extraction de données.

Cliquez [ici](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) pour obtenir des informations sur la façon dont l’approbateur peut approuver les demandes d’accès aux données et cliquez [ici](https://docs.microsoft.com/graph/data-connect-pam) pour obtenir une explication sur l’intégration globale à Privileged Access Management, notamment comment configurer le groupe d’approbateurs d’accès aux données.

## <a name="policy-validation"></a>Validation de la stratégie

Si ADF est créé dans le cadre d’une application managée et que les affectations de stratégies Azure sont effectuées sur des ressources au sein du groupe de ressources de gestion, puis pour chaque exécution de l’activité de copie, ADF vérifie que les affectations de stratégie sont appliquées. Cliquez [ici](https://docs.microsoft.com/graph/data-connect-policies#policies) pour obtenir la liste des stratégies prises en charge.

## <a name="getting-started"></a>Prise en main

>[!TIP]
>Pour obtenir une procédure pas à pas de l’utilisation du connecteur Office 365, consultez l’article [Charger des données depuis Office 365](load-office-365-data.md).

Vous pouvez créer un pipeline avec l’activité de copie à l’aide d’un des outils ou kits de développement logiciel suivants. Sélectionnez un lien pour accéder à un didacticiel contenant des instructions détaillées pour créer un pipeline avec une activité de copie. 

- [Portail Azure](quickstart-create-data-factory-portal.md)
- [Kit de développement logiciel (SDK) .NET](quickstart-create-data-factory-dot-net.md)
- [Kit de développement logiciel (SDK) Python](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [API REST](quickstart-create-data-factory-rest-api.md)
- [Modèle Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md) 

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Office 365.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Office 365 sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Office365** | Oui |
| office365TenantId | ID de locataire Azure auquel le compte Office 365 appartient. | Oui |
| servicePrincipalTenantId | Spécifiez les informations du locataire où se trouve votre application web Azure AD. | Oui |
| servicePrincipalId | Spécifiez l’ID client de l’application. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ comme SecureString pour le stocker de façon sécurisée dans Data Factory. | Oui |
| connectVia | Runtime d’intégration à utiliser pour la connexion à la banque de données.  À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non |

>[!NOTE]
> La différence entre **office365TenantId** et **servicePrincipalTenantId**, et la valeur correspondante à fournir :
>- Si vous êtes développeur en entreprise et que vous développez une application avec des données Office 365 pour l’utiliser au sein de votre propre organisation, vous devez fournir le même ID de locataire pour les deux propriétés, qui est l’ID de locataire AAD de votre organisation.
>- Si vous êtes développeur chez un éditeur de logiciels indépendant et que vous développez une application pour vos clients, office365TenantId est l’ID de locataire AAD (programme d’installation de l’application) de votre client et servicePrincipalTenantId est l’ID de locataire AAD de votre entreprise.

**Exemple :**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section contient la liste des propriétés prises en charge par le jeu de données Office 365.

Pour copier des données depuis Office 365, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **Office365Table** | Oui |
| TableName | Nom du jeu de données à extraire d’Office 365. Cliquez [ici](https://docs.microsoft.com/graph/data-connect-datasets#datasets) pour obtenir la liste des jeux de données Office 365 disponibles pour l’extraction. | Oui |
| allowedGroups | Prédicat de sélection de groupe.  Cette propriété permet de sélectionner jusqu'à 10 groupes d’utilisateurs pour lesquels les données seront récupérées.  Si aucun groupe n’est spécifié, les données seront affichera pour toute l’organisation. | Non |
| userScopeFilterUri | Lorsque `allowedGroups` propriété n’est pas spécifiée, vous pouvez utiliser une expression de prédicat est appliquée sur l’ensemble du locataire pour filtrer les lignes spécifiques à extraire à partir d’Office 365. Le format de prédicat doit correspondre au format de requête de l’API Microsoft Graph, par exemple, `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Non |
| dateFilterColumn | Nom de la colonne de filtre de date/heure. Cette propriété permet de limiter l’intervalle de temps pour les Office 365 sont extraites les données. | Oui, si le jeu de données comporte une ou plusieurs colonnes de date/heure. Consultez [ici](https://docs.microsoft.com/graph/data-connect-filtering#filtering) pour la liste des jeux de données qui nécessitent ce filtre de date/heure. |
| startTime | Démarrer la valeur DateTime à filtrer. | Oui, si `dateFilterColumn` est spécifié |
| endTime | Fin de valeur DateTime à filtrer. | Oui, si `dateFilterColumn` est spécifié |

**Exemple**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section contient la liste des propriétés prises en charge par une source Office 365.

### <a name="office-365-as-source"></a>Office 365 comme source

Pour copier des données depuis Office 365, définissez le type de source dans l’activité de copie sur **Office365Source**. Aucune autre propriété n’est prise en charge dans la section **source** de l’activité de copie.

**Exemple :**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
