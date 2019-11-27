---
title: Intégration de partenaire de capteur
description: Décrit l’intégration de partenaire de capteur
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 1e819c94732e1cbc2de39e6400f8305b7df5aca1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927654"
---
# <a name="sensor-partner-integration"></a>Intégration de partenaire de capteur

Cet article fournit des informations sur le composant Azure FarmBeats **Translator**, qui permet l'intégration de partenaire de capteur.

À l’aide de ce composant, les partenaires peuvent développer des capteurs qui s’intègrent à FarmBeats, en tirant parti de notre API et en envoyant des données de télémétrie et des données d’appareil client au Data Hub FarmBeats. Les données sont visualisées à l’aide de l’accélérateur FarmBeats. Les données peuvent être utilisées pour la fusion de données et pour la création de modèles d’intelligence artificielle ou de langage machine.

## <a name="link-farmbeats-account"></a>Lier un compte FarmBeats

Une fois que les clients ont acheté et déployé des appareils/capteurs, ils peuvent accéder aux données de l’appareil et aux données de télémétrie depuis le portail SaaS des partenaires d’appareils. Les partenaires d’appareils doivent permettre aux clients de lier leur compte à leur instance FarmBeats sur Azure. Les informations d’identification suivantes doivent être renseignées par client/intégrateur système :

   - Nom complet (champ facultatif permettant à l’utilisateur de définir un nom pour cette intégration)
   - Point de terminaison d’API
   - ID client
   - ID client
   - Clé secrète client
   - Chaîne de connexion du hub d’événements
   - Date de début

   > [!NOTE]
   > La date de début active le flux de données d’historique, c’est-à-dire les données de la date spécifiée par l’utilisateur.

## <a name="unlink-farmbeats"></a>Dissocier FarmBeats

Les clients ont la possibilité de dissocier une intégration FarmBeats existante. La dissociation de FarmBeats ne doit pas supprimer les métadonnées d’appareil/capteur qui ont été créées dans le Data Hub du client. La dissociation les opérations suivantes :

   - Arrête le workflow des données de télémétrie.
   - Supprime et efface les informations d’identification d’intégration sur le partenaire d’appareil.

## <a name="edit-farmbeats-integration"></a>Modifier l’intégration de FarmBeats

Le client peut modifier les paramètres d'intégration de FarmBeats, si le secret du client ou la chaîne de connexion change. Dans ce cas, le client peut uniquement modifier les champs suivants :

   - Nom complet (le cas échéant)
   - Clé secrète client (doit être affichée dans le format « 2x8 * * * * * * * * * * * » ou dans la fonctionnalité Afficher/masquer au lieu du texte en clair)
   - Chaîne de connexion (doit être affichée dans le format « 2x8 * * * * * * * * * * * » ou dans la fonctionnalité Afficher/masquer au lieu du texte en clair)

## <a name="view-last-telemetry-sent"></a>Afficher les dernières données de télémétrie envoyées

Vous pouvez afficher l’horodateur des dernières **données de télémétrie envoyées**. Il s’agit de l’heure à laquelle les données de télémétrie les plus récentes ont été correctement envoyées à FarmBeats.

## <a name="translator-development"></a>Développement de Translator

**Intégration basée sur l’API REST**

Les fonctionnalités d’intégration des données de capteur de FarmBeats sont exposées via l’API REST. Les fonctionnalités incluent la définition des métadonnées, l’approvisionnement de l’appareil/capteur, l’appareil et la gestion des capteurs.

**Ingestion de données de télémétrie**

Les données de télémétrie sont mappées à un message canonique publié sur Azure Event Hub pour traitement. Azure Event Hub est un service qui permet l’ingestion des données (de télémétrie) en temps réel à partir des appareils et applications connectés.

**Développement d’API**

Les API incluent une documentation technique Swagger. Pour plus d’informations sur les API et leurs demandes/réponses correspondantes, consultez [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)

**Authentification**

FarmBeats s’appuie sur l’authentification Active Directory de Microsoft Azure. Azure App Service offre une prise en charge intégrée de l’authentification et de l’autorisation.

Pour plus d’informations, consultez [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

Le Data Hub de FarmBeats utilise l’authentification de porteur, qui nécessite les informations d’identification suivantes :
   - ID client
   - Clé secrète client
   - ID client

À l’aide des informations d’identification ci-dessus, l’appelant peut demander un jeton d’accès, qui doit être envoyé dans les requêtes d’API suivantes (dans la section d’en-tête) comme suit :

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Voici un exemple de code Python qui donne le jeton d’accès, qui peut être utilisé pour les appels d’API suivants à FarmBeats : 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**En-têtes de requête HTTP**

Voici les en-têtes de requête les plus courants qui doivent être spécifiés lors d’un appel d’API au Data Hub de FarmBeats :


**En-tête** | **Description et exemple**
--- | ---
Content-Type | Le format de demande (Content-Type: application/<format>) pour l’API du Data Hub de FarmBeats est JSON. Type de contenu : application/json
Authorization | Spécifie le jeton d’accès requis pour effectuer une autorisation dappel d’API : Porteur <jeton-accès>
Acceptation | Format de la réponse. Pour les API du hub de données FarmBeats, le format est JSON. Accept: application/json

**Requêtes d’API**

Pour effectuer une demande d’API REST (Representational State Transfer), vous associez la méthode HTTP (obtenir, poster ou PUT), l’URL au service d’API, l’URI (Uniform Resource Identifier) à une ressource à interroger, vers laquelle envoyer des données, à mettre à jour ou à supprimer, et un ou plusieurs en-têtes de requête HTTP. L’URL vers le service API est le point de terminaison d’API fourni par le client. Voici un exemple : https://\<yourdatahub-website>.azurewebsites.net

Si vous le souhaitez, vous pouvez inclure des paramètres de requête à des appels GET pour filtrer les données dans les réponses, en limiter la taille et les trier.

L’exemple de requête ci-dessous porte sur l’obtention de la liste des appareils :

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
La plupart des appels GET, POST et PUT nécessitent un corps de requête JSON.

L’exemple de requête ci-dessous porte sur la création d’un appareil (cette exemple comporte un code JSON d’entrée avec le corps de la requête).

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Format de données

JSON (JavaScript Object Notation) est un format de données courant, indépendant du langage, qui fournit une représentation textuelle simple de structures de données arbitraires. Pour plus d’informations, consultez [json.org](http://json.org).

## <a name="metadata-specifications"></a>Spécifications de métadonnées

Le hub de données FarmBeats offre les API suivantes, qui permettent aux partenaires d’appareil de créer et de gérer les métadonnées d’appareil/de capteur.  

- /**DeviceModel** : correspond aux métadonnées de l’appareil, telles que le fabricant et le type d’appareil (passerelle ou nœud).  
- /**Device** : correspond à un appareil physique présent dans la ferme.
- /**SensorModel** : correspond aux métadonnées du capteur, telles que le fabricant, le type de capteur (analogique ou numérique), la mesure effectuée par le capteur (température ambiante, pression, etc.).
- /**Sensor** : correspond à un capteur physique qui enregistre des valeurs. Un capteur est généralement connecté à un appareil avec un ID d’appareil.

  Modèle de l'appareil| DeviceModel correspond aux métadonnées de l’appareil, telles que le fabricant et le type d’appareil (passerelle ou nœud).
  --- | ---
  Type (nœud, passerelle)  | 1 étoile |
  Fabricant  | 2 étoiles |
  ProductCode  | Code produit de l’appareil ou nom/numéro du modèle. Par exemple, EnviroMonitor#6800 |
  Ports  | Nom et type de port (numérique/analogique)  |
  Nom  | Nom destiné à identifier la ressource. Par exemple, le nom du modèle ou du produit |
  Description  | Description explicite du modèle |
  properties  | Propriétés supplémentaires fournies par le fabricant |
  **Appareil** | **Appareil correspond à un appareil physique présent dans la ferme. Chaque appareil a un ID d’appareil unique** |
DeviceModelId  |ID du modèle d’appareil associé. |
HardwareId   |ID unique de l’appareil, par exemple l’adresse MAC  |
ReportingInterval |Intervalle de rapport en secondes |
Location    |Latitude (-90 à +90)/longitude (-180 à 180)/Élévation (en mètres) de l’appareil |
ParentDeviceId | ID de l’appareil parent auquel cet appareil est connecté. Par exemple, Un nœud connecté à une passerelle ; parentDeviceId sera la passerelle pour le nœud |
  Nom  | Nom destiné à identifier la ressource.  Les partenaires d’appareil doivent envoyer un nom correspondant à celui de l’appareil côté partenaire. Si le nom de l’appareil est défini par l’utilisateur côté appareil partenaire, ce nom doit être propagé sur FarmBeats  |
  Description  | Description explicite  |
  properties  |Propriétés supplémentaires fournies par le fabricant  |
  **Modèle de capteur** | SensorModel correspond aux métadonnées du capteur, telles que le fabricant, le type de capteur (analogique ou numérique), la mesure effectuée par le capteur (température ambiante, pression, etc.). |
  Type (analogique, numérique)  |Mentionne le type de capteur (analogique ou numérique)|
  manufacturer  | Nom du fabricant |
  ProductCode  | Code produit ou nom/numéro du modèle. Par exemple, RS-CO2-N01  |
  SensorMeasures > Name  | Nom de la mesure du capteur. Seules les minuscules sont prises en charge. Pour une mesure effectuée à différentes profondeurs, spécifiez la profondeur. Par exemple, humidité_sol_15cm. Ce nom doit être cohérent avec les données de télémétrie. |
  SensorMeasures > DataType  | Type de données de télémétrie. Actuellement, le type double est pris en charge  |
  SensorMeasures > Type  | Type de mesure des données de télémétrie du capteur. Voici les types définis par le système : AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Pour en ajouter, reportez-vous à la documentation sur l’API /ExtendedType
  SensorMeasures > Unit | Unité des données de télémétrie du capteur. Voici les unités définies par le système : NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour. Pour en ajouter, reportez-vous à la documentation sur l’API /ExtendedType
  SensorMeasures > aggregationType  | Soit none, average, maximum, minimum ou StandardDeviation
  SensorMeasures > depth  | Profondeur du capteur en centimètres (par exemple, mesure de 10 cm d’humidité sous le sol)
  sensorMeasures > description  | Description explicite de la mesure
  Nom  | Nom destiné à identifier la ressource. Par exemple, le nom du modèle ou du produit
  description  | Description explicite du modèle
  properties  | Propriétés supplémentaires fournies par le fabricant
  **Capteur**  |
  HardwareId  | ID unique du capteur défini par le fabricant
  SensorModelId  | ID du modèle de capteur associé.
  location  | Latitude (-90 à +90)/longitude (-180 à 180)/Élévation (en mètres) du capteur
  port > name  |Nom et type du port auquel le capteur est connecté sur l’appareil. Ce nom doit être identique à celui défini dans le modèle d’appareil
  deviceId  | ID de l’appareil auquel le capteur est connecté
  Nom  | Nom destiné à identifier la ressource. Par exemple, nom du capteur/nom du produit et numéro du modèle/code produit.
  description  | Description explicite
  properties  | Propriétés supplémentaires fournies par le fabricant

 Pour plus d’informations sur chacun des objets et leurs propriétés, consultez [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Les API retournent des ID uniques pour chaque instance créée. Cet ID doit être conservé par le traducteur pour la gestion des périphériques et la synchronisation des métadonnées.


**Synchronisation des métadonnées**

Le traducteur doit envoyer des mises à jour sur les métadonnées. Par exemple, les scénarios de mise à jour sont : modification du nom de l’appareil/capteur, modification de l’emplacement de l’appareil/capteur.

Le traducteur doit avoir la possibilité d’ajouter de nouveaux appareils et/ou capteurs installés par l’utilisateur après la liaison de FarmBeats. De même, si un appareil/capteur a été mis à jour par l’utilisateur, l’appareil/capteur correspondant doit être mis à jour dans FarmBeats. Les scénarios classiques pour la mise à jour d’appareil/capteur peuvent être : modification de l’emplacement de l’appareil, ajout de capteurs dans un nœud, etc.


> [!NOTE]
> La suppression n’est pas prise en charge pour les métadonnées d’appareil/capteur.
>
> Pour mettre à jour les métadonnées, il est obligatoire d’appeler /Get/{ID} sur l’appareil/capteur, de mettre à jour les propriétés modifiées, puis d’effectuer un /Put/{ID} afin que les propriétés définies par l’utilisateur ne soient pas perdues

### <a name="adding-new-typesunit"></a>Ajout de nouveaux types/unités

FarmBeats prend en charge l’ajout de nouveaux types et unités de mesure de capteur. Pour plus d’informations sur l’API /ExtendedType, consultez [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Spécifications des données de télémétrie

Les données de télémétrie sont mappées à un message canonique publié sur Azure Event Hub pour traitement. Azure Event Hub est un service qui permet l’ingestion des données (de télémétrie) en temps réel à partir des appareils et applications connectés.

## <a name="send-telemetry-data-to-farmbeats"></a>Envoyer des données de télémétrie à FarmBeats

Pour envoyer des données de télémétrie à FarmBeats, vous devez créer un client qui envoie des messages à un hub d’événements dans FarmBeats. Pour plus d’informations sur les données de télémétrie, consultez [Envoi de données de télémétrie à Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Voici un exemple de code Python qui envoie des données de télémétrie en tant que client à un Event Hub spécifié :

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Le format de message canonique se présente comme suit :

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
Tous les noms clés dans le JSON des données de télémétrie doivent être en minuscules, par exemple deviceid, sensordata, etc.

Par exemple, message de données de télémétrie :


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
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

## <a name="troubleshooterror-management"></a>Dépannage/gestion des erreurs

**Option de dépannage/prise en charge**

Si le client n’est pas en mesure de recevoir des données de l’appareil et/ou des données de télémétrie dans l’instance FarmBeats spécifiée, le partenaire de l’appareil doit fournir un support technique et un mécanisme pour le résoudre.

**Conservation des données de télémétrie**

Les données de télémétrie doivent également être conservées pour une période prédéfinie, elles sont utiles pour le débogage ou le renvoi de la télémétrie en cas d’erreur ou de perte de données.

**Gestion d’erreurs/notification d’erreurs**

En cas d’erreur qui affecte l’appareil/les métadonnées du capteur/l’intégration de données ou le flux de données de télémétrie dans le système partenaire de l’appareil, il faut le notifié au client. Un mécanisme pour résoudre la ou les erreurs doit également être conçu et implémenté.

**Liste de contrôle de connexion**

Les fabricants/partenaires d’appareils peuvent avoir le test/la liste de contrôle de validité qui suit pour s’assurer que les informations d’identification fournies par le client sont exactes.

   - Vérifier si un jeton d’accès est reçu avec les informations d’identification fournies
   - Vérifier si un appel d’API a échoué avec le jeton d’accès reçu
   - Vérifier si la connexion cliente EventHub est établie

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’API REST, consultez [API REST](references-for-farmbeats.md#rest-api).
