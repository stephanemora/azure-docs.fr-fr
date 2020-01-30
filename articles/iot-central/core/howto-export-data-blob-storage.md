---
title: Exporter vos données vers Stockage Blob Azure | Microsoft Docs
description: Comment exporter des données de votre application Azure IoT Central vers Stockage Blob Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 79d578c910c7d08355901308e00db5912d1f3baf
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721487"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Exporter vos données vers Stockage Blob Azure

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Cette rubrique s’applique aux administrateurs.*

Cet article explique comment utiliser la fonctionnalité d’exportation continue des données dans Azure IoT Central pour exporter régulièrement des données vers votre **compte Stockage Blob Azure** ou votre **compte de stockage Azure Data Lake Storage Gen2**. Vous pouvez exporter des **mesures**, des **appareils** et des **modèles d’appareil** dans des fichiers au format JSON ou Apache Avro. Utilisez les données exportées pour l’analytique des chemins à froid, comme les modèles d’apprentissage dans Azure Machine Learning ou l’analyse de tendances à long terme dans Microsoft Power BI.

> [!Note]
> Quand vous activez l’exportation de données continue, vous obtenez seulement les données à partir de ce moment. Pour le moment, vous ne pouvez pas récupérer les données d’une période pendant laquelle l’exportation de données continue est désactivée. Pour conserver un historique des données plus étendu, activez l’exportation de données continue tôt dans le processus.


## <a name="prerequisites"></a>Conditions préalables requises

- Vous devez être administrateur dans votre application IoT Central


## <a name="set-up-export-destination"></a>Définir la destination de l’exportation

En l’absence de données de Stockage existantes à exporter, suivez ces étapes :

1. Créez un [compte de stockage sur le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Découvrez comment créer des [comptes de stockage Blob Azure](https://aka.ms/blobdocscreatestorageaccount) ou des [comptes de stockage Azure Data Lake Storage v2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Si vous choisissez d’exporter des données vers un compte de stockage ADLS v2, vous devez définir**Type de compte** sur **BlobStorage**. 

    > [!Note] 
    > Vous pouvez exporter des données vers des comptes de stockage compris dans des abonnements autres que celui de votre application de paiement à l’utilisation IoT Central. Vous vous connecterez à l’aide d’une chaîne de connexion dans ce cas.

2. Créez un conteneur dans votre compte de stockage. Accédez à votre compte de stockage. Sous **Service blob**, sélectionnez **Parcourir les objets blob**. Sélectionnez **+ Conteneur**, en haut, pour créer un conteneur.

## <a name="set-up-continuous-data-export"></a>Configurer l’exportation de données en continu

Maintenant que vous avez une destination de Stockage pour exporter les données, suivez ces étapes pour configurer l’exportation continue des données. 

1. Connectez-vous à votre application IoT Central.

2. Dans le volet gauche, sélectionnez **Exportation de données**.

    > [!Note]
    > Si l’option Exportation de données n’apparaît pas dans le volet gauche, la raison en est que vous n’êtes pas l’administrateur de votre application. Contactez un administrateur pour configurer l’exportation de données.

3. Sélectionnez le bouton **+ Nouveau** dans le coin supérieur droit. Choisissez **Stockage Blob Azure** comme destination de votre exportation. 

    > [!NOTE] 
    > Le nombre maximal d’exportations par application est de cinq. 

    ![Créer une exportation de données continue](media/howto-export-data/export-new2.png)

4. Dans la zone de liste déroulante, sélectionnez l’**espace de noms de votre compte de stockage**. Vous pouvez également choisir la dernière option de la liste : **Entrer une chaîne de connexion**. 

    > [!NOTE] 
    > Vous verrez seulement les espaces de noms des comptes de stockage se trouvant dans le **même abonnement que votre application IoT Central**. Si vous souhaitez exporter les données vers une destination en dehors de cet abonnement, choisissez **Entrer une chaîne de connexion** et reportez-vous à l’étape 5.

    > [!NOTE] 
    > Pour les applications d’évaluation de 7 jours, le seul moyen de configurer l’exportation des données est d’utiliser une chaîne de connexion. En effet, ces applications ne sont associées à aucun abonnement Azure.

    ![Créer une exportation vers un objet blob](media/howto-export-data/export-create-blob2.png)

5. (Facultatif) Si vous avez choisi **Entrer une chaîne de connexion**, une nouvelle zone vous permettant de coller votre chaîne de connexion s’affiche. Pour obtenir la chaîne de connexion de votre compte de stockage, accédez au compte de stockage dans le portail Azure : sous **Paramètres**, sélectionnez **Clés d’accès**, puis copiez la chaîne de connexion key1 ou la chaîne de connexion key2
 
6. Choisissez un conteneur dans la liste déroulante. Si vous n’avez pas de conteneur, accédez à votre compte de stockage dans le portail Azure :
    - Sous **Service blob**, sélectionnez **Objets blob**. Cliquez sur **+ Conteneur** et donnez un nom à votre conteneur. Choisissez un niveau d’accès public pour vos données (tous sont compatibles avec l’exportation de données continue). Pour en savoir plus, consultez les [documents sur le Stockage Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7. Choisissez le **format de données** que vous préférez : format JSON ou [Apache Avro](https://avro.apache.org/docs/current/index.html).

8. Sous **Données à exporter**, spécifiez chaque type de données à exporter en définissant le type avec la valeur **Activé**.

9. Pour activer l’exportation des données continue, vérifiez que le bouton bascule **Exportation de données** est sur **Activé**. Sélectionnez **Enregistrer**.

   ![Configurer l’exportation de données continue](media/howto-export-data/export-list-blob2.png)

10. Après quelques minutes, vos données s’affichent dans votre compte de stockage.


## <a name="path-structure"></a>Structure du chemin

Les données de mesures, d’appareils et de modèles d’appareil sont exportées vers votre compte de stockage une fois par minute. Chaque fichier contient le lot de modifications apportées depuis la dernière exportation de fichier. Les données exportées sont placées dans trois dossiers au format JSON ou Avro. Les chemins d’accès par défaut dans votre compte de stockage sont les suivants :
- Messages : {conteneur}/measurements/{nom_hub}/{AAAA}/{MM}/{jj}/{hh}/{mm}/{nom_fichier}
- Appareils : {conteneur}/devices/{AAAA}/{MM}/{jj}/{hh}/{mm}/{nom_fichier}
- Modèles d’appareil : {conteneur}/deviceTemplates/{AAAA}/{MM}/{jj}/{hh}/{mm}/{nom_fichier}

Vous pouvez parcourir les fichiers exportés dans le portail Azure en accédant au fichier puis en choisissant l’onglet **Modifier l’objet blob**.

## <a name="data-format"></a>Format de données 

### <a name="measurements"></a>Mesures

Les données de mesures exportées contiennent tous les nouveaux messages reçus par IoT Central en provenance de tous les appareils pendant cette période. Les fichiers exportés utilisent le même format que les fichiers de messages exportés par [le routage des messages IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) vers Stockage Blob.

> [!NOTE]
> Vérifiez que vos appareils envoient bien des messages avec `contentType: application/JSON` et `contentEncoding:utf-8` (ou `utf-16`, `utf-32`). Pour obtenir un exemple, consultez la [documentation IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body).

> [!NOTE]
> Les appareils qui envoient les mesures sont représentés par leur ID d’appareil (voir les sections suivantes). Pour obtenir les noms des appareils, exportez les instantanés d’appareil. Associez chaque enregistrement avec le **connectionDeviceId** qui correspond au **deviceId** de l’enregistrement de l’appareil.

L’exemple suivant montre un enregistrement dans un fichier Avro décodé :

```json
{ 
  "EnqueuedTimeUtc":"2019-06-11T00:00:08.2250000Z",
  "Properties":{},
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceId>",
    "connectionAuthMethod":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"<generationId>",
    "enqueuedTime":"2019-06-11T00:00:08.2250000Z"
  },
  "Body":"{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Appareils

Quand l’exportation de données continue est activée pour la première fois, un instantané unique avec tous les appareils est exporté. Chaque appareil inclut les éléments suivants :
- `id` de l’appareil dans IoT Central
- `name` de l’appareil
- `deviceId` issu du [service de provisionnement des appareils](/azure/iot-central/core/howto-connect-nodejs)
- Informations sur le modèle d’appareil
- Valeurs de propriétés
- Valeurs de paramètres

Un nouvel instantané est écrit chaque minute. L’instantané inclut les éléments suivants :

- Nouveaux appareils ajoutés depuis le dernier instantané.
- Appareils pour lesquels des valeurs de propriétés et de paramètres ont changé depuis le dernier instantané.

> [!NOTE]
> Les appareils supprimés depuis le dernier instantané ne sont pas exportés. À l’heure actuelle, les instantanés n’ont pas d’indicateurs pour les appareils supprimés.
>
> Le modèle d’appareil auquel chaque appareil appartient est représenté par un ID de modèle d’appareil. Pour obtenir le nom du modèle d’appareil, exportez les instantanés des modèles d’appareil.

Les fichiers exportés contiennent une seule ligne par enregistrement. L’exemple suivant montre un enregistrement décodé au format Avro :

```json
{ 
  "id":"<id>",
  "name":"Refrigerator 2",
  "simulated":true,
  "deviceId":"<deviceId>",
  "deviceTemplate":{ 
    "id":"<template id>",
    "version":"1.0.0"
  },
  "properties":{ 
    "cloud":{ 
      "location":"New York",
      "maintCon":true,
      "tempThresh":20
    },
    "device":{ 
      "lastReboot":"2018-02-09T22:22:47.156Z"
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":0
    }
  }
}
```

### <a name="device-templates"></a>Modèles d’appareil

Quand l’exportation de données continue est activée pour la première fois, un instantané unique avec tous les modèles d’appareil est exporté. Chaque modèle d’appareil inclut les éléments suivants :
- `id` du modèle d’appareil
- `name` du modèle d’appareil
- `version` du modèle d’appareil
- Types de données et valeurs minimales/maximales des mesures.
- Types de données et valeurs par défaut des propriétés.
- Types de données et valeurs par défaut des paramètres.

Un nouvel instantané est écrit chaque minute. L’instantané inclut les éléments suivants :

- Nouveaux modèles d’appareil ajoutés depuis le dernier instantané.
- Modèles d’appareil pour lesquels les définitions de mesures, propriétés et paramètres ont changé depuis le dernier instantané.

> [!NOTE]
> Les modèles d’appareil supprimés depuis le dernier instantané ne sont pas exportés. À l’heure actuelle, les captures instantanées n’ont pas d’indicateurs pour les modèles d’appareil supprimés.

Les fichiers exportés contiennent une seule ligne par enregistrement. L’exemple suivant montre un enregistrement décodé au format Avro :

```json
{ 
  "id":"<id>",
  "name":"Refrigerated Vending Machine",
  "version":"1.0.0",
  "measurements":{ 
    "telemetry":{ 
      "humidity":{ 
        "dataType":"double",
        "name":"Humidity"
      },
      "magnetometerX":{ 
        "dataType":"double",
        "name":"Magnetometer X"
      },
      "magnetometerY":{ 
        "dataType":"double",
        "name":"Magnetometer Y"
      },
      "magnetometerZ":{ 
        "dataType":"double",
        "name":"Magnetometer Z"
      }
    },
    "states":{ 
      "connectivity":{ 
        "dataType":"enum",
        "name":"Connectivity"
      }
    },
    "events":{ 
      "opened":{ 
        "name":"Door Opened",
        "category":"informational"
      }
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":{ 
        "dataType":"double",
        "name":"Fan Speed",
        "initialValue":0
      }
    }
  },
  "properties":{ 
    "cloud":{ 
      "location":{ 
        "dataType":"string",
        "name":"Location",
        "initialValue":"Seattle"
      },
      "maintCon":{ 
        "dataType":"boolean",
        "name":"Maintenance Contract",
        "initialValue":true
      },
      "tempThresh":{ 
        "dataType":"double",
        "name":"Temperature Alert Threshold",
        "initialValue":30
      }
    },
    "device":{ 
      "lastReboot":{ 
        "dataType":"dateTime",
        "name":"Last Reboot"
      }
    }
  }
}
```

## <a name="read-exported-avro-files"></a>Lire les fichiers Avro exportés

Avro étant un format binaire, les fichiers ne peuvent pas être lus dans leur état brut. Les fichiers peuvent être décodés au format JSON. Les exemples suivants montrent comment analyser les fichiers Avro des mesures, appareils et modèles d’appareil. Les exemples correspondent aux exemples décrits dans la section précédente.

### <a name="read-avro-files-by-using-python"></a>Lire des fichiers Avro à l’aide de Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Installer pandas et le package pandavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analyser un fichier Avro de mesures

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>Analyser un fichier Avro d’appareils

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>Analyser un fichier Avro de modèles d’appareil

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Lire des fichiers Avro à l’aide de C#

#### <a name="install-the-microsofthadoopavro-package"></a>Installer le package Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analyser un fichier Avro de mesures

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
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

#### <a name="parse-a-devices-avro-file"></a>Analyser un fichier Avro d’appareils

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
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

#### <a name="parse-a-device-templates-avro-file"></a>Analyser un fichier Avro de modèles d’appareil

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
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

### <a name="read-avro-files-by-using-javascript"></a>Lire des fichiers Avro à l’aide de Javascript

#### <a name="install-the-avsc-package"></a>Installer le package avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Analyser un fichier Avro de mesures

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Analyser un fichier Avro d’appareils

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Analyser un fichier Avro de modèles d’appareil

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and version properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment exporter vos données, passez à l’étape suivante :

> [!div class="nextstepaction"]
> [Guide pratique pour visualiser vos données dans Power BI](howto-connect-powerbi.md)
