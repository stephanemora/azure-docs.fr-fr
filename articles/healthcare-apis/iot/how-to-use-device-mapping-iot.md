---
title: modèle de mappage d’appareil dans IoT Connector-api de santé Azure
description: Cet article explique comment utiliser le modèle de mappage d’appareil dans le IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: jasteppe
ms.openlocfilehash: 3550bab190c2b5bbec5a03faa8131094003ee85b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446008"
---
# <a name="how-to-use-device-mapping"></a>Utilisation du mappage des appareils

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Le connecteur IoT requiert deux types de mappages JSON. Le premier type, le **mappage d’appareils**, est chargé de mapper les charges utiles d’appareils envoyées au point de terminaison `devicedata` Azure Event Hub. Il extrait les types, les identificateurs d’appareil, les dates et heures de mesure, ainsi que la ou les valeur(s) de mesure. 

Le deuxième type de **mappage de destination FHIR (Fast Healthcare Interoperability Resources&#174;)** contrôle le mappage de la ressource FHIR. Il permet de configurer la durée de la période d’observation, le type de données FHIR utilisé pour stocker les valeurs et le ou les code(s) de terminologie. 

Les deux types de mappages sont composés dans un document JSON en fonction de leur type. Ces documents JSON sont ensuite ajoutés à votre connecteur IoT via le Portail Azure. Le document de mappage d’appareil est ajouté via la page **mappage d’appareil** et le document de mappage de destination FHIR via la page de **destination** .

> [!NOTE]
> Les mappages sont stockés dans un stockage d’objets BLOB sous-jacent et chargés à partir d’un objet BLOB par exécution de calcul. Une fois mis à jour, ils doivent prendre effet immédiatement. 

> [!TIP]
> Consultez l’outil [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages de chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la [version open source](https://github.com/microsoft/iomt-fhir) du connecteur IOT.

## <a name="device-mapping"></a>Mappage d’appareil

Le mappage d’appareils fournit des fonctionnalités de mappage pour extraire le contenu de l’appareil dans un format commun pour une évaluation supplémentaire. Chaque message reçu est évalué par rapport à tous les modèles. Cette approche permet de projeter un message entrant unique pour plusieurs messages sortants, qui sont ensuite mappés à différentes observations dans FHIR. Le résultat est un objet de données normalisé représentant la valeur ou les valeurs analysées par les modèles. Le modèle de données normalisé contient quelques propriétés obligatoires qui doivent être recherchées et extraites :

| Propriété             | Description                                                                                                                                                                                                                                                   |
|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**             | Nom/type pour classifier la mesure. Cette valeur est utilisée pour établir une liaison avec le mappage de destination FHIR requis.  Plusieurs mappages peuvent être générés dans le même type, ce qui vous permet de mapper différentes représentations sur plusieurs appareils à une sortie commune unique. |
| **OccurenceTimeUtc** | Heure à laquelle la mesure a été effectuée.                                                                                                                                                                                                                            |
| **DeviceId**         | Identificateur de l’appareil. Cette valeur doit correspondre à un identificateur sur la ressource de l’appareil qui existe sur le service FHIR de destination.                                                                                                                       |
| **Propriétés**       | Extrayez au moins une propriété afin que la valeur puisse être enregistrée dans la ressource d’observation créée.  Les propriétés sont un regroupement de paires de valeurs clés extraites pendant la normalisation.                                                                                  |

Vous trouverez ci-dessous un exemple conceptuel de ce qui se passe lors de la normalisation.

![Exemple de normalisation](media/concepts-iot-mapping-templates/normalization-example.png#lightbox)

La charge utile de contenu est un message Azure Event Hub, qui est composé de trois parties : Corps, Propriétés et SystemProperties. Le `Body` est un tableau d’octets qui représente une chaîne encodée en UTF-8. Pendant l’évaluation du modèle, le tableau d’octets est automatiquement converti en valeur de chaîne. `Properties` est un regroupement de valeurs clés à utiliser par le créateur du message. `SystemProperties` est également un regroupement de valeurs clés réservé par l’infrastructure Azure Event Hub avec des entrées automatiquement remplies par celles-ci.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
### <a name="mapping-with-json-path"></a>Le mappage avec le chemin d’accès JSON

Les trois types de mappage de contenu d’appareil pris en charge aujourd’hui s’appuient sur le chemin JSON pour correspondre au mappage et aux valeurs extraites requis. Des informations supplémentaires sur le chemin d’accès JSON sont disponibles [ici](https://goessner.net/articles/JsonPath/). Les trois types de modèles utilisent l’[implémentation .NET JSON](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) pour la résolution des expressions de chemin d’accès JSON.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate permet la correspondance et l’extraction des valeurs d’un message Event Hub à l’aide du chemin d’accès JSON.

| Propriété | Description |Exemple |
| --- | --- | --- |
|**TypeName**|Type à associer aux mesures correspondant au modèle.|`heartrate`
|**TypeMatchExpression**|Expression de chemin d’accès JSON qui est évaluée par rapport à la charge utile Event Hub. Si un JToken correspondant est trouvé, le modèle est considéré comme une correspondance. Toutes les expressions suivantes sont évaluées par rapport aux JToken extraits correspondants.|`$..[?(@heartRate)]`
|**TimestampExpression**|Expression de chemin d’accès JSON pour extraire la valeur d’horodatage pour le OccurenceTimeUtc de la mesure.|`$.endDate`
|**DeviceIdExpression**|Expression de chemin d’accès JSON pour extraire l’identificateur d’appareil.|`$.deviceId`
|**PatientIdExpression**|*Facultatif* : Expression de chemin d’accès JSON pour extraire l’identificateur de patient.|`$.patientId`
|**EncounterIdExpression**|*Facultatif* : Expression de chemin d’accès JSON pour extraire l’identificateur de rencontre.|`$.encounterId`
|**Values[].ValueName**|Nom à associer à la valeur extraite par l’expression suivante. Utilisé pour lier la valeur/le composant requis dans le mappage de destination FHIR. |`hr`
|**Values[].ValueExpression**|Expression de chemin d’accès JSON pour extraire la valeur requise.|`$.heartRate`
|**Values[].Required**|Nécessite que la valeur soit présente dans la charge utile.  Si la valeur est introuvable, la mesure n’est pas générée et une exception InvalidOperationException est levée.|`true`

##### <a name="examples"></a>Exemples

**Fréquence cardiaque**

*Message*

```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modèle*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
**Tension artérielle**

*Message*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modèle*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
**Projeter plusieurs mesures à partir d’un seul message**

*Message*

```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modèle 1*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

*Modèle 2*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
**Projeter plusieurs mesures à partir d’un tableau dans un message**

*Message*

```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2021-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2021-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2021-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Modèle*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate est similaire à JsonPathContentTemplate, sauf que DeviceIdExpression et TimestampExpression ne sont pas requis.

L’hypothèse de l’utilisation de ce modèle est que les messages en cours d’évaluation ont été envoyés à l’aide des [kits de développement logiciel (SDK) d’appareils Azure IoT Hub](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) ou de la fonctionnalité [Exporter des données (héritée)](../../iot-central/core/howto-export-data-legacy.md) d’[Azure IoT Central](../../iot-central/core/overview-iot-central.md). Lors de l’utilisation de ces kits de développement logiciel (SDK), l’identité de l’appareil (en supposant que l’identificateur d’appareil d’Azure IOT Hub/central est inscrit comme identificateur pour une ressource d’appareil sur le service FHIR de destination) et que l’horodateur du message est connu. Si vous utilisez les kits de développement logiciel (SDK) d’appareils Azure IoT Hub, mais que vous utilisez des propriétés personnalisées dans le corps du message pour l’identité de l’appareil ou l’horodatage de mesure, vous pouvez toujours utiliser JsonPathContentTemplate.

> [!NOTE]
> Lors de l’utilisation de `IotJsonPathContentTemplate` , le `TypeMatchExpression` doit correspondre à l’intégralité du message en tant que JToken. Pour plus d’informations, reportez-vous aux exemples suivants.

##### <a name="examples"></a>Exemples

**Fréquence cardiaque**

*Message*

```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Modèle*

```json

    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**Tension artérielle**

*Message*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Modèle*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate ne nécessite pas non plus DeviceIdExpression et TimestampExpression. elle est utilisée lorsque les messages en cours d’évaluation sont envoyés par le biais de la fonctionnalité [exporter des données](../../iot-central/core/howto-export-data.md) de [Azure IoT Central](../../iot-central/core/overview-iot-central.md). Lorsque vous utilisez cette fonctionnalité, l’identité de l’appareil (en supposant que l’identificateur d’appareil d’Azure IOT central est inscrit en tant qu’identificateur pour une ressource d’appareil sur le serveur de FHIR de destination) et que l’horodateur du message est connu. Si vous utilisez la fonctionnalité Exporter des données d’Azure IoT Central, mais que vous utilisez des propriétés personnalisées dans le corps du message pour l’identité de l’appareil ou l’horodatage de mesure, vous pouvez toujours utiliser JsonPathContentTemplate.

> [!NOTE]
> Lors de l’utilisation de IotCentralJsonPathContentTemplate, TypeMatchExpression doit être résolu en tout le message en tant que JToken. Pour plus d’informations, reportez-vous aux exemples suivants.
 
##### <a name="examples"></a>Exemples

**Fréquence cardiaque**

*Message*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Modèle*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**Tension artérielle**

*Message*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Modèle*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
>[Utilisation du mappage de destination FHIR](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.