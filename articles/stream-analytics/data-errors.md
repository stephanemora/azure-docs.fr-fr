---
title: Erreurs de données de journal de diagnostic Stream Analytique Azure
description: Cet article explique les différentes d’entrée et les erreurs de données de sortie qui peuvent se produire lors de l’utilisation d’Azure Stream Analytique.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: b00eb12092838746f4bfe16f00eac55df9224b09
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65607227"
---
# <a name="azure-stream-analytics-data-errors"></a>Erreurs de données Azure Stream Analytique

Lorsqu’il existe une incohérence dans les données qui sont traitées par un travail Azure Stream Analytique, Analytique de Stream envoie un événement d’erreur de données pour les journaux de diagnostic. Analytique de Stream écrit des informations détaillées et des événements de l’exemple, dans ses journaux de diagnostic lorsque des erreurs de données se produisent. Un résumé de ces informations est également fourni via les notifications du portail pour certaines erreurs.

Cet article décrit les différents types d’erreurs, les causes et les détails du journal de diagnostic pour les erreurs de données d’entrée et de sortie.

## <a name="diagnostic-log-schema"></a>Schéma des journaux de diagnostic

Consultez [résoudre les problèmes d’Azure Stream Analytique à l’aide des journaux de diagnostic](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) pour afficher le schéma pour les journaux de diagnostic. Le code JSON suivant est un exemple de valeur pour le **propriétés** champ d’un journal de diagnostic pour une erreur de données.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Erreurs de données d’entrée

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Cause : Le type de compression d’entrée sélectionné ne correspond pas les données.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Entrez l’identificateur de message. Pour le concentrateur d’événements, l’identificateur est le PartitionId, le décalage et le numéro de séquence.

**Message d’erreur**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Cause : L’en-tête de données d’entrée n’est pas valide. Par exemple, un fichier CSV comporte des colonnes avec des noms dupliqués.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Entrez l’identificateur de message. 
   * Charge utile réelle jusqu'à quelques kilo-octets.

**Message d’erreur**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Cause : Les colonnes d’entrée définis avec CREATE TABLE ou par le biais TIMESTAMP BY n’existe pas.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Entrez l’identificateur de message. 
   * Noms des colonnes qui sont manquants. 
   * Charge utile réelle jusqu'à (quelques kilo-octets).

**Messages d’erreur**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Cause : Impossible de convertir l’entrée pour le type spécifié dans l’instruction CREATE TABLE.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Entrez l’identificateur de message. 
   * Nom de la colonne et le type attendu.

**Messages d’erreur**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Cause : Données d’entrée ne sont pas dans le bon format. Par exemple, l’entrée n’est pas un JSON valide.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Entrez l’identificateur de message. 
   * Charge utile réelle jusqu'à quelques kilo-octets.

**Messages d’erreur**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Cause : La valeur de l’expression TIMESTAMP BY ne peut pas être convertie en datetime.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Entrez l’identificateur de message. 
   * Message d’erreur. 
   * Charge utile réelle jusqu'à quelques kilo-octets.

**Message d’erreur**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Cause : La valeur de TIMESTAMP BY OVER timestampColumn est NULL.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * La charge utile réelle jusqu'à quelques kilo-octets.

**Message d’erreur**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Cause : La différence entre l’heure de l’application et l’heure d’arrivée est supérieure à tolérance d’arrivée tardive.
* Notification du portail fournie : Non
* Niveau de journal de diagnostic : Information
* Détails des journaux
   * Heure de l’application et l’heure d’arrivée. 
   * Charge utile réelle jusqu'à quelques kilo-octets.

**Message d’erreur**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Cause : La différence entre l’heure de l’Application et l’heure d’arrivée est supérieure à 5 minutes.
* Notification du portail fournie : Non
* Niveau de journal de diagnostic : Information
* Détails des journaux
   * Heure de l’application et l’heure d’arrivée. 
   * Charge utile réelle jusqu'à quelques kilo-octets.

**Message d’erreur**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Cause : Événement est considéré comme en désordre en fonction de la fenêtre de tolérance de désordre définie.
* Notification du portail fournie : Non
* Niveau de journal de diagnostic : Information
* Détails des journaux
   * Charge utile réelle jusqu'à quelques kilo-octets.

**Message d’erreur**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Erreurs de données de sortie

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Cause : La colonne obligatoire pour la sortie n’existe pas. Par exemple, une colonne définie comme Azure Table PartitionKey does't existe.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Nom de la colonne et l’identificateur d’enregistrement ou partie de l’enregistrement.

**Message d’erreur**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Cause : La valeur de colonne ne sont pas conformes avec la sortie. Par exemple, le nom de colonne n’est pas une colonne de table Azure valide.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Nom de la colonne et identificateur d’enregistrement ou partie de l’enregistrement.

**Message d’erreur**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Cause : Une colonne ne peut pas être convertie en un type valide dans la sortie. Par exemple, la valeur de colonne est incompatible avec les contraintes ou type défini dans la table SQL.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Nom de la colonne.
   * Identificateur d’enregistrement ou partie de l’enregistrement.

**Message d’erreur**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Cause : La valeur du message est supérieure à la taille de sortie pris en charge. Par exemple, un enregistrement est supérieur à 1 Mo pour une sortie de concentrateur d’événements.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Identificateur d’enregistrement ou partie de l’enregistrement.

**Message d’erreur**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Cause : Un enregistrement contient déjà une colonne avec le même nom qu’une colonne système. Par exemple, la sortie CosmosDB avec une colonne nommée ID lors de la colonne d’ID est à une autre colonne.
* Notification du portail fournie : Oui
* Niveau de journal de diagnostic : Avertissement
* Détails des journaux
   * Nom de la colonne.
   * Identificateur d’enregistrement ou partie de l’enregistrement.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Étapes suivantes

* [Résoudre les problèmes d’Azure Stream Analytique à l’aide des journaux de diagnostic](stream-analytics-job-diagnostic-logs.md)

* [Comprendre comment surveiller les requêtes et surveillance des travaux d’Analytique de Stream](stream-analytics-monitoring.md)
