---
title: Exporter vos données dans Azure IoT Central | Microsoft Docs
description: Comment exporter des données depuis votre application Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 6d35e3cfefcefef0b4ff40364cbdab92d486b769
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011457"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exporter vos données dans Azure IoT Central

Utilisez l’exportation de données continue pour exporter régulièrement des données dans votre compte Stockage Blob Azure. Choisissez d’exporter des **mesures**, des **appareils** et des **modèles d’appareil** dans des fichiers au format [Apache AVRO](https://avro.apache.org/docs/current/index.html). Utilisez les données exportées pour l’analytique des chemins à froid, comme les modèles d’apprentissage dans Azure Machine Learning ou l’analyse de tendances à long terme dans Power BI.

> [!Note]
> Quand vous activez l’exportation de données continue, vous obtenez seulement les données qui entrent à partir de ce moment. Il n’existe actuellement aucun moyen de récupérer des données à partir du moment où l’exportation de données continue a été désactivée. Activez l’exportation de données continue tôt dans le processus de façon à conserver un historique des données plus étendu !

## <a name="prerequisites"></a>Prérequis

- Une application d’essai de 30 jours étendue ou une application payante
- Un compte Azure avec un abonnement Azure
- Le même compte Azure est un administrateur dans votre application IoT Central
- Le même compte Azure dispose des autorisations pour créer un compte de stockage ou pour accéder à un compte de stockage existant dans le même abonnement Azure

## <a name="types-of-data-to-export"></a>Types de données à exporter

### <a name="measurements"></a>Mesures

Les mesures envoyées par les appareils sont exportées dans votre compte de stockage. Les données des mesures sont exportées environ une fois par minute ; elles contiennent tous les nouveaux messages qui ont été reçus par IoT Central en provenance de tous les appareils pendant cette fenêtre de temps. Les fichiers AVRO exportés sont au même format que les fichiers de messages exportés par [le routage des messages IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) vers le stockage Blob.

> [!NOTE]
> Les appareils qui ont envoyé les mesures sont représentés par leur ID d’appareil (voir ci-dessous). Pour obtenir les noms des appareils, vous devez également exporter les instantanés des appareils. Vous pouvez associer chaque enregistrement de message avec le connectionDeviceId qui correspond à l’ID de l’appareil.

Voici un exemple d’enregistrement dans le fichier AVRO décodé.

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Appareils

Quand vous activez l’exportation de données continue pour la première fois, un instantané unique contenant tous les appareils est exporté. Ceci inclut :
- ID des appareils
- Noms des appareils
- ID des modèles d’appareil
- Valeurs des propriétés
- Valeurs des paramètres

Environ une fois par minute, un nouvel instantané est écrit, qui contient :

- Les nouveaux appareils qui ont été ajoutés depuis le dernier instantané
- Les appareils dont les valeurs des propriétés et des paramètres ont changé depuis le dernier instantané

> [!NOTE]
> Les appareils qui ont été supprimés depuis le dernier instantané ne sont pas exportés. Il n’existe pour l’instant aucun indicateur dans les instantanés pour les appareils qui ont été supprimés.

> [!NOTE]
> Le modèle d’appareil auquel chaque appareil appartient est représenté par un ID de modèle d’appareil. Pour obtenir le nom du modèle d’appareil, vous devez également exporter des instantanés des modèles d’appareil.

Chaque enregistrement du fichier AVRO décodé se présente comme ceci :

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Modèles d’appareil

Quand vous activez l’exportation de données continue pour la première fois, un instantané unique contenant tous les modèles d’appareil est exporté. notamment : 
- ID des modèles d’appareil
- Types de données et valeurs minimales/maximales des mesures
- Types de données et valeurs par défaut des propriétés
- Types de données et valeurs par défaut des paramètres

Environ une fois par minute, un nouvel instantané est écrit, qui contient :

- Les nouveaux modèles d’appareil qui ont été ajoutés depuis le dernier instantané
- Les modèles d’appareil pour lesquels les définitions des mesures, des propriétés et des paramètres ont changé depuis le dernier instantané

> [!NOTE]
> Les modèles d’appareil qui ont été supprimés depuis le dernier instantané ne sont pas exportés. Il n’existe pour l’instant aucun indicateur dans les instantanés pour les modèles d’appareil qui ont été supprimés.

Chaque enregistrement du fichier AVRO décodé se présente comme ceci :

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="how-to-set-up-data-export"></a>Comment configurer l’exportation de données

1. Si vous n’en avez pas encore un, créez un compte Stockage Azure **dans l’abonnement Azure où se trouve votre application**. [Cliquez ici](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) pour accéder au portail Azure de façon à créer un compte Stockage Azure.

    - Choisissez le type de compte *Usage général* ou *Stockage Blob*
    - Sélectionnez l’abonnement où se trouve votre application IoT Central. Si vous ne voyez pas l’abonnement, il peut être nécessaire de vous connecter à un autre compte Azure ou de demander l’accès à l’abonnement.
    - Vous pouvez choisir un groupe de ressources existant ou en créer un. Découvrez [comment créer un compte de stockage](https://aka.ms/blobdocscreatestorageaccount)

2. Créez un conteneur dans votre compte de stockage pour y exporter vos données IoT Central. Accédez à votre Compte de stockage -> Parcourir les objets blob, puis créez un conteneur. ![Créer une image de conteneur](media/howto-export-data/createcontainer.png)

3. Connectez-vous à votre application IoT Central avec le même compte Azure.
1. Accédez à Administration -> Exportation de données continue.
![Exportation de données continue d’IoT Central](media/howto-export-data/continuousdataexport.PNG)
1. Utilisez les listes déroulantes, choisissez votre compte de stockage et votre conteneur. Utilisez ensuite les bascules pour activer ou désactiver les différents types de données à exporter.
1. Enfin, activez l’exportation de données continue avec la bascule, puis cliquez sur « Enregistrer ».
1. Patientez quelques minutes, au terme desquelles vous voyez normalement apparaître vos données dans votre compte de stockage. Vous pouvez accéder à votre compte de stockage, sélectionner Parcourir les objets blob et sélectionner votre conteneur : vous voyez alors trois dossiers. Les chemins par défaut pour les fichiers AVRO contenant les différents types de données sont :
- Messages : **{conteneur}/measurements/{nom_hub}/{AAAA}/{MM}/{
- j}/{hh}/{mm}/00.avro**Appareils : **{conteneur}/devices/{nom_hub}/{AAAA}/{MM}/{
- j}/{hh}/{mm}/00.avro**Modèles d’appareil : **{conteneur}/deviceTemplates/{nom_hub}/{AAAA}/{MM}/{

## <a name="how-to-read-exported-avro-files"></a>j}/{hh}/{mm}/00.avro**Comment lire les fichiers AVRO exportés

AVRO étant un format binaire, les fichiers ne peuvent pas être lus dans leur état brut. Ils peuvent être décodés au format JSON. Les exemples suivants montrent comment analyser les fichiers AVRO des mesures, des appareils et des modèles d’appareil en utilisant les exemples ci-dessus.

## <a name="python"></a>Python

### <a name="install-pandas-and-the-pandaavro-package"></a>Installez Pandas et le package PandaAvro :

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>Analyser le fichier AVRO des mesures

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>Analyser le fichier AVRO des appareils

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>Analyser le fichier AVRO des modèles d’appareil

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>Installer Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>Analyser le fichier AVRO des mesures

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

### <a name="parse-devices-avro-file"></a>Analyser le fichier AVRO des appareils

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```
### <a name="parse-device-templates-avro-file"></a>Analyser le fichier AVRO des modèles d’appareil

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

## <a name="javascript"></a>JavaScript

### <a name="install-avsc"></a>Installer avsc

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>Analyser le fichier AVRO des mesures

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>Analyser le fichier AVRO des appareils

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>Analyser le fichier AVRO des modèles d’appareil

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [gérer vos appareils](howto-manage-devices.md) dans l’explorateur d’appareils. 
