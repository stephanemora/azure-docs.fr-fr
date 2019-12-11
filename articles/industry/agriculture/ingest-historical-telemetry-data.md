---
title: Ingérer des données de télémétrie historiques
description: Décrit comment ingérer des données de télémétrie historiques
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 27aec53fd2e92e19f1c749e833217fb8b5deae57
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672571"
---
# <a name="ingest-historical-telemetry-data"></a>Ingérer des données de télémétrie historiques

Cet article explique comment ingérer des données de capteur historiques dans Azure FarmBeats.

L’ingestion de données historiques à partir de ressources IoT (Internet des objets) telles que des appareils et des capteurs est un scénario courant dans FarmBeats. Vous créez des métadonnées pour les appareils et les capteurs, puis vous ingérez les données historiques dans FarmBeats dans un format canonique.

## <a name="before-you-begin"></a>Avant de commencer

Avant de poursuivre cet article, vérifiez que vous avez installé FarmBeats et collecté des données historiques à partir de ressources IoT.
Vous devez également activer l’accès partenaire comme cela est expliqué ci-après.

## <a name="enable-partner-access"></a>Activer l’accès partenaire

Vous devez activer l’intégration des partenaires à votre instance Azure FarmBeats. Cette étape consiste à créer un client qui aura accès à votre instance Azure FarmBeats en tant que partenaire d’appareil. Elle vous permet également d’obtenir les valeurs ci-après, requises pour les étapes suivantes.

- Point de terminaison d’API : URL du hub de données, par exemple https://<datahub>.azurewebsites.net
- ID client
- ID client
- Clé secrète client
- Chaîne de connexion du hub d’événements

Suivez les étapes ci-dessous pour les générer :

>[!NOTE]
> Pour effectuer les étapes suivantes, vous devez être administrateur.

1. Téléchargez ce [script](https://aka.ms/farmbeatspartnerscript) et extrayez-le sur votre lecteur local. Vous trouverez deux fichiers dans le fichier ZIP.
2. Connectez-vous au [portail Azure](https://portal.azure.com/) et ouvrez Cloud Shell (cette option est disponible dans la barre supérieure droite du portail).  

    ![Projet FarmBeats](./media/for-tutorials/navigation-bar-1.png)

3. Vérifiez que l’environnement est défini sur **PowerShell**.

    ![Projet FarmBeats](./media/for-tutorials/power-shell-new-1.png)

4. Chargez les deux fichiers que vous avez téléchargés (à l’étape 1 ci-dessus) dans Cloud Shell.  

    ![Projet FarmBeats](./media/for-tutorials/power-shell-two-1.png)

5. Accédez au répertoire où les fichiers ont été chargés.

   >[!NOTE]
   > Par défaut, le chargement s’effectue dans le répertoire de base /home/username/.
6. Exécutez le script à l’aide de la commande suivante :  

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. Suivez les instructions à l’écran pour effectuer la procédure.

## <a name="create-devicesensor-metadata"></a>Créer des métadonnées d’appareil/de capteur

 Maintenant que vous disposez des informations d’identification nécessaires, vous pouvez définir l’appareil et les capteurs en créant les métadonnées à l’aide des API FarmBeats.

 Le hub de données FarmBeats propose les API suivantes, qui permettent de créer et de gérer les métadonnées d’appareil/de capteur.   

- /**DeviceModel** : correspond aux métadonnées de l’appareil, telles que le fabricant et le type d’appareil (passerelle ou nœud).  
- /**Device** : correspond à un appareil physique présent dans la ferme.  
- /**SensorModel** : correspond aux métadonnées du capteur, telles que le fabricant, le type de capteur (analogique ou numérique), la mesure effectuée par le capteur (température ambiante, pression, etc.).
- /**Sensor** : correspond à un capteur physique qui enregistre des valeurs. Un capteur est généralement connecté à un appareil avec un ID d’appareil.  


|        Modèle de l'appareil   |  Suggestions   |
| ------- | -------             |
|     Type (Node, Gateway)        |          1 étoile      |
|          Fabricant            |         2 étoiles     |
|  ProductCode                    |  Code produit de l’appareil ou nom/numéro du modèle. Par exemple, EnviroMonitor#6800.  |
|            Ports          |     Nom et type de port (numérique/analogique)
|     Nom                 |  Nom destiné à identifier la ressource. Par exemple, le nom du modèle ou du produit.
      Description     | Description explicite du modèle
|    properties          |    Propriétés supplémentaires fournies par le fabricant   |
|    **Appareil**             |                      |
|   DeviceModelId     |     ID du modèle d’appareil associé  |
|  HardwareId          | ID unique de l’appareil, par exemple l’adresse MAC
|  ReportingInterval        |   Intervalle de rapport en secondes
|  Location            |  Latitude (-90 à +90)/longitude (-180 à 180)/Élévation (en mètres) de l’appareil   
|ParentDeviceId       |    ID de l’appareil parent auquel cet appareil est connecté. Par exemple, un nœud connecté à une passerelle. Pour un nœud, le parentDeviceId sera l’ID d’une passerelle.  |
|    Nom            | Nom destiné à identifier la ressource. Les partenaires d’appareil doivent envoyer un nom correspondant à celui de l’appareil côté partenaire. Si le nom de l’appareil partenaire est défini par l’utilisateur, ce nom doit être propagé sur FarmBeats.|
|     Description       |      Description explicite  |
|     properties    |  Propriétés supplémentaires fournies par le fabricant
|     **Modèle de capteur**        |          |
|       Type (Analog, Digital)          |      Type de capteur (analogique ou numérique)       |
|          Fabricant            |       Fabricant du capteur     |
|     ProductCode| Code produit ou nom/numéro du modèle. Par exemple, RS-CO2-N01. |
|       SensorMeasures > Name       | Nom de la mesure du capteur. Seules les minuscules sont prises en charge. Pour une mesure effectuée à différentes profondeurs, spécifiez la profondeur. Par exemple, humidité_sol_15cm. Ce nom doit être cohérent avec les données de télémétrie.  |
|          SensorMeasures > DataType       |Type de données de télémétrie. Actuellement, le type double est pris en charge.|
|    SensorMeasures > Type    |Type de mesure des données de télémétrie de capteur. Voici les types définis par le système : AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Pour en ajouter, reportez-vous à la documentation sur l’API /ExtendedType.|
|        SensorMeasures > Unit              | Unité des données de télémétrie du capteur. Voici les unités définies par le système : NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour. Pour en ajouter, reportez-vous à la documentation sur l’API /ExtendedType.|
|    SensorMeasures > aggregationType    |  Les valeurs peuvent être : none, average, maximum, minimum ou StandardDeviation.  |
|          Nom            | Nom destiné à identifier la ressource. Par exemple, le nom du modèle ou du produit.  |
|    Description        | Description explicite du modèle  |
|   properties       |  Propriétés supplémentaires fournies par le fabricant  |
|    **Capteur**      |          |
| HardwareId          |   ID unique du capteur défini par le fabricant |
|  SensorModelId     |    ID du modèle de capteur associé   |
| location          |  Latitude (-90 à +90)/longitude (-180 à 180)/Élévation (en mètres) du capteur|
|   Port > name        |  Nom et type du port auquel le capteur est connecté sur l’appareil. Ce nom doit être identique à celui défini dans le modèle d’appareil. |
|    DeviceID  |    ID de l’appareil auquel le capteur est connecté     |
| Nom            |   Nom destiné à identifier la ressource. Par exemple, nom du capteur/nom du produit et numéro du modèle/code produit.|
|    Description      | Description explicite |
|    properties        |Propriétés supplémentaires fournies par le fabricant |

Pour plus d’informations sur les objets, consultez [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

**Requête d’API pour la création de métadonnées**

Pour effectuer une requête d’API, vous devez associer la méthode HTTP (POST), l’URL du service d’API, l’URI d’une ressource (pour l’interrogation, l’envoi des données, la création ou la suppression d’une requête) et un ou plusieurs en-têtes de requête HTTP. L’URL du service d’API est le point de terminaison d’API, c’est-à-dire l’URL du hub de données (https://<yourdatahub>.azurewebsites.net)  

**Authentification** :

Le hub de données FarmBeats utilise l’authentification de porteur, qui nécessite les informations d’identification suivantes (générées dans le cadre de la section précédente).

- ID client
- Clé secrète client
- ID client  

À l’aide des informations d’identification ci-dessus, l’appelant peut demander un jeton d’accès, qui doit être envoyé dans les requêtes d’API suivantes (dans la section d’en-tête) comme suit :

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

**En-têtes de requête HTTP** :

Voici les en-têtes de requête les plus courants qui doivent être spécifiés lors d’un appel d’API au hub de données FarmBeats :

- Content-Type: application/json
- Autorisation : Bearer <jeton-accès>
- Accept: application/json

**Charge utile d’entrée pour la création des métadonnées** :

**DeviceModel**


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Appareil

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Capteur

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
L’exemple de requête ci-dessous porte sur la création d’un appareil (qui comporte un code JSON d’entrée en tant que charge utile avec le corps de la requête).  

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> Les API retournent des ID uniques pour chaque instance créée. Vous devez conserver les ID pour l’envoi des messages de télémétrie correspondants.

**Envoyer des données de télémétrie**

Maintenant que vous avez créé les appareils et les capteurs dans FarmBeats, vous pouvez envoyer les messages de télémétrie associés.  

**Créer un client de télémétrie**

Vous devez envoyer les données de télémétrie à Azure Event Hub pour traitement. Azure Event Hub est un service assurant l’ingestion de données en temps réel (télémétrie) à partir des appareils et applications connectés. Pour envoyer des données de télémétrie à FarmBeats, vous devez créer un client qui envoie des messages à un hub d’événements dans FarmBeats. Pour plus d’informations sur l’envoi de données de télémétrie, consultez [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

**Envoyer un message de télémétrie en tant que client**

Après avoir établi une connexion en tant que client du hub d’événements, vous pouvez envoyer des messages au hub d’événements sous forme de code JSON.  
Convertissez les données de capteur historiques dans un format canonique compréhensible par Azure FarmBeats. Le format de message canonique se présente comme suit :  

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

Après avoir ajouté les appareils et capteurs correspondants, récupérez les ID d’appareil et de capteur dans le message de télémétrie comme décrit dans la section précédente.

Exemple de message de télémétrie :


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’intégration basée sur l’API REST, consultez [API REST](references-for-farmbeats.md#rest-api).
