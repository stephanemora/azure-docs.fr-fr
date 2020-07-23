---
title: Intégration de partenaire - Données météorologiques
description: Cet article explique comment un fournisseur de données météorologiques peut s’intégrer avec FarmBeats
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: d4ceb25b9b81c831ed1b285a875742ebfaa6d24f
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232642"
---
# <a name="weather-partner-integration"></a>Intégration de partenaire - Données météorologiques

Cet article fournit des informations sur le composant de Docker **Connecteur** Azure FarmBeats que les fournisseurs de données météorologiques peuvent développer pour s’intégrer à FarmBeats en tirant parti de ses API et envoyer des données météorologiques à FarmBeats. Une fois que les données sont disponibles dans FarmBeats, il est possible de s’en servir pour fusionner des données et générer des modèles de Machine Learning et d’intelligence artificielle.

 > [!NOTE]
 > Dans le cadre de cette documentation, nous allons utiliser une implémentation de référence créée à l’aide de NOAA à partir d’Azure Open Datasets, disponible sur [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker).
 > L’image Docker correspondante est disponible à l’adresse [https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

Un partenaire météorologique devra fournir un programme/une image Docker (avec les spécifications mentionnées ci-dessous) et héberger l’image Docker dans un registre de conteneurs accessible par les clients. Le partenaire météorologique devra fournir les informations suivantes à ses clients :

- URL de l’image Docker
- Étiquette de l’image Docker
- Clés/informations d’identification pour accéder à l’image Docker
- Clés/informations d’identification d’API spécifiques au client pour accéder aux données à partir du système du partenaire météorologique
- Détails de la référence SKU de machine virtuelle (les partenaires peuvent fournir cela au cas où leur Docker a des exigences spécifiques en matière de machines virtuelles, sinon les clients peuvent choisir parmi les références des machines virtuelles prises en charge dans Azure)

À l’aide des informations de Docker ci-dessus, le client inscrit un partenaire météorologique dans son instance FarmBeats. Pour en savoir plus sur la façon dont les clients peuvent utiliser le Docker pour ingérer des données météorologiques dans FarmBeats, consultez le guide pour [obtenir des données météorologiques](https://docs.microsoft.com/azure/industry/agriculture/get-weather-data-from-weather-partner)

## <a name="connector-docker-development"></a>Développement de Docker Connecteur

**Intégration basée sur l’API REST**

Les API FarmBeats incluent une documentation technique Swagger. Pour plus d’informations sur toutes les API et leurs demandes ou réponses correspondantes, consultez [FarmBeats Swagger](https://aka.ms/farmbeatsswagger). 

Si vous avez installé FarmBeats, vous pouvez accéder à votre FarmBeats Swagger à l’adresse [https://yourfarmbeatswebsitename-api.azurewebsites.net](https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger)

Notez que « -api » est ajouté au nom de votre site web FarmBeats.
Le point de terminaison de l’API est : [https://yourfarmbeatswebsitename-api.azurewebsites.net](https://yourfarmbeatswebsitename-api.azurewebsites.net)

### <a name="datahub-lib"></a>Bibliothèque Datahub

FarmBeats fournit une bibliothèque qui peut être utilisée par le partenaire météorologique. La bibliothèque est actuellement disponible dans le cadre de l’implémentation de référence [ici](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). À l’avenir, elle sera disponible en tant que Kit SDK pour plusieurs langues.

### <a name="authentication"></a>Authentification

**Authentification avec les API FarmBeats**

FarmBeats utilise l’authentification du porteur et les API sont accessibles en fournissant un jeton d’accès dans la section d’en-tête de la demande, comme indiqué ci-dessous :

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Le jeton d’accès peut être demandé à partir d’une fonction Azure qui s’exécute sur l’instance FarmBeats du client. L’URL de la fonction Azure sera fournie au programme de Docker en tant qu’argument et le jeton d’accès peut être obtenu en effectuant une requête GET sur l’URL. La réponse de l’URL contient le jeton d’accès. La bibliothèque DataHub fournit des fonctions d’assistance pour permettre aux partenaires d’obtenir le jeton d’accès. Plus de détails [ici](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

Le jeton d’accès est valide uniquement pendant quelques heures et doit être demandé à nouveau après expiration.

**Authentification avec les API côté partenaire**

Pour permettre aux clients de s’authentifier avec des API côté partenaire lors de l’exécution de Docker, les clients doivent fournir les informations d’identification lors de l’inscription du partenaire, comme suit :

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
Le service d’API sérialise ce dictionnaire et le stocke dans un [KeyVault](https://docs.microsoft.com/azure/key-vault/basic-concepts).

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) permet d’orchestrer des travaux météorologiques et de faire tourner les ressources pour exécuter le code de Docker. Il fournit également un mécanisme pour envoyer (push) les données de façon sécurisée à la machine virtuelle où le travail de Docker s’exécute. Les informations d’identification de l’API, qui sont maintenant stockées en toute sécurité dans KeyVault, sont lues en tant que chaînes sécurisées à partir de KeyVault et sont rendues disponibles en tant que propriétés étendues dans le répertoire de travail du conteneur de Docker en tant que fichier activity.json (le chemin d’accès au fichier est « /mnt/working_dir/activity.json »). Le code de Docker peut lire les informations d’identification à partir de ce fichier durant l’exécution pour accéder aux API côté partenaire au nom du client. Les informations d’identification seront disponibles dans le fichier comme suit :

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
Notez que « partnerCredentials » est disponible de la manière exacte fournie par le client lors de l’inscription du partenaire.

La bibliothèque FarmBeats fournit des fonctions d’assistance pour permettre aux partenaires de lire les informations d’identification à partir des propriétés de l’activité. Plus de détails [ici](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

La durée de vie du fichier correspond à la durée d’exécution du code de Docker et le fichier sera supprimé une fois l’exécution de Docker terminée.

Pour plus d’informations sur le fonctionnement des pipelines et des activités ADF, consultez [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping).

**En-têtes de requête HTTP**

Voici les en-têtes de demande les plus courants qui doivent être spécifiés lors d’un appel d’API à FarmBeats.

**En-tête** | **Description et exemple**
--- | ---
Content-Type | Format de la demande (Content-Type: application/<format>). Pour les API FarmBeats Datahub, le format est JSON. Content-Type: application/json
Autorisation | Spécifie le jeton d’accès requis pour effectuer un appel d’API. Autorisation : Porteur <jeton-accès>
Acceptation | Format de la réponse. Pour les API FarmBeats Datahub, le format est JSON. Accept: application/json

## <a name="data-format"></a>Format de données

JSON est un format de données courant, indépendant du langage, qui fournit une représentation textuelle simple de structures de données arbitraires. Pour plus d’informations, consultez [json.org](http://json.org).

## <a name="docker-specifications"></a>Spécifications de Docker

Le programme de Docker doit avoir deux composants : **Bootstrap** (Démarrage) et **Jobs** (Travaux). Il peut y avoir plusieurs travaux.

### <a name="bootstrap"></a>Bootstrap

Ce composant doit s’exécuter lorsque le client lance l’inscription de Docker sur FarmBeats. Les arguments (arg1, arg2) passés à ce programme sont les suivants :

- Point de terminaison de l’API FarmBeats : Point de terminaison de l’API FarmBeats pour les demandes d’API : Il s’agit du point de terminaison pour effectuer des appels d’API au déploiement FarmBeats.
- URL de fonction Azure : Il s’agit de votre propre point de terminaison personnel qui vous donne votre jeton d’accès pour les API FarmBeats. Le simple fait d’appeler GET sur cette URL permet de récupérer le jeton d’accès dans la réponse.

La responsabilité du composant Bootstrap consiste à créer les métadonnées nécessaires pour que les utilisateurs puissent exécuter vos travaux afin d’obtenir des données météorologiques. Consultez l’implémentation de référence [ici](https://github.com/azurefarmbeats/noaa_docker). Vous pouvez mettre à jour le fichier bootstrap_manifest.json en fonction de vos besoins et le programme de démarrage de référence crée les métadonnées requises pour vous.

Les métadonnées suivantes sont créées dans le cadre de ce processus. 

 > [!NOTE]
 > **Notez** que si vous mettez à jour le fichier bootstrap_manifest.json comme indiqué dans [l’implémentation de référence](https://github.com/azurefarmbeats/noaa_docker), vous n’avez pas besoin de créer les métadonnées ci-dessous, car le programme de démarrage crée les mêmes données en fonction de votre fichier manifeste.

- /**WeatherDataModel** :  WeatherDataModel est un modèle qui représente des données météorologiques et qui correspond à différents jeux de données fournis par la source. Par exemple, un modèle DailyForecastSimpleModel peut fournir des informations sur la température, l’humidité et les précipitations moyennes une fois par jour, tandis qu’un modèle DailyForecastAdvancedModel peut fournir de nombreuses informations avec une précision horaire. Vous pouvez créer autant de modèles WeatherDataModels que nécessaire.
- /**JobType** : FarmBeats dispose d’un système extensible de gestion des travaux. En tant que fournisseur de données météorologiques, vous avez différents jeux de données/API (par exemple GetDailyForecasts) et vous pouvez les activer dans FarmBeats en tant que JobType. Une fois qu’un JobType est créé, un client peut déclencher des Travaux de ce type afin d’obtenir des données météorologiques pour son emplacement/sa batterie de serveurs (voir les API JobType et Job dans [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)).

### <a name="jobs"></a>travaux

Ce composant est appelé à chaque fois qu’un utilisateur de FarmBeats exécute un travail du /JobType que vous avez créé dans le cadre du processus de démarrage. La commande d’exécution de Docker pour le travail est définie dans le cadre du **/JobType** que vous avez créé.
- La responsabilité du travail consiste à extraire les données de la source et à les envoyer à FarmBeats. Les paramètres requis pour l’extraction des données doivent être définis dans le cadre du /JobType dans le processus de démarrage.
- Pour le travail, le programme doit créer un **/WeatherDataLocation** en fonction du /WeatherDataModel créé dans le cadre du processus de démarrage. **/WeatherDataLocation** correspond à un emplacement (latitude/longitude) qui est fourni par l’utilisateur en tant que paramètre du travail.

### <a name="details-of-the-objects"></a>Détails des objets

  **WeatherDataModel** |  |
  --- | ---
  Nom  | Nom du modèle de données météorologiques |
  Description  | Description explicite du modèle. |
  Propriétés  | Propriétés supplémentaires définies par le fournisseur de données. |
  weatherMeasures > Name  | Nom de la mesure météorologique. Par exemple : humidity_max |
  weatherMeasures > DataType  | Double ou Énum. Pour Énum., measureEnumDefinition est requis |
  weatherMeasures > measureEnumDefinition  | Obligatoire uniquement si DataType est égal à Énum. Par exemple { "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 } |
  weatherMeasures > Type  | Type de données de télémétrie météorologiques. Par exemple, « RelativeHumidity ». Voici les types définis par le système : AmbientTemperature, NoUnit, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Pour en ajouter d’autres, reportez-vous à l’API /ExtendedType ou à la section [Ajouter des types et des unités](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) ci-dessous
  weatherMeasures > Unit | Unité des données de télémétrie météorologiques. Voici les unités définies par le système : NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond et InchesPerHour. Pour en ajouter d’autres, reportez-vous à l’API /ExtendedType ou à la section [Ajouter des types et des unités](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) ci-dessous.
  weatherMeasures > AggregationType  | None, Average, Maximum, Minimum, StandardDeviation, Sum ou Total
  weatherMeasures > Depth  | Profondeur du capteur en centimètres. Par exemple, la mesure de l’humidité à 10 cm dans le sol.
  weatherMeasures > Description  | Description explicite de la mesure. |
  **JobType** |  |
  Nom  | nom du travail, par exemple Get_Daily_Forecast ; travail exécuté par le client pour récupérer les données météorologiques|
  pipelineDetails > parameters > name  | nom du paramètre |
  pipelineDetails > parameters > type | String, Int, Float, Bool ou Array |
  pipelineDetails > parameters > isRequired | booléen ; true si le paramètre est obligatoire, sinon false ; valeur par défaut : true |
  pipelineDetails > parameters > defaultValue | Valeur par défaut du paramètre |
  pipelineDetails > parameters > description | Description du paramètre |
  Propriétés  | Propriétés supplémentaires fournies par le fabricant.
  Properties > **programRunCommand** | commande d’exécution de Docker : cette commande est exécutée lorsque le client exécute le travail météorologique. |
  **WeatherDataLocation** |  |
  weatherDataModelId  | ID du modèle WeatherDataModel correspondant qui a été créé au cours du démarrage|
  location  | représente la latitude, la longitude et l’altitude |
  Nom | Nom de l’objet |
  Description | Description |
  farmId | **facultatif** ID de la batterie de serveurs fournie par le client dans le cadre du paramètre du travail |
  Propriétés  | Propriétés supplémentaires fournies par le fabricant.

 Pour plus d’informations sur chacun des objets et leurs propriétés, consultez [Swagger](https://aka.ms/FarmBeatsSwagger).

 > [!NOTE]
 > Les API retournent des ID uniques pour chaque instance créée. Cet ID doit être conservé par le traducteur pour la gestion des périphériques et la synchronisation des métadonnées.

**Synchronisation des métadonnées**

Le Docker Connecteur doit avoir la possibilité d’envoyer des mises à jour sur les métadonnées. Voici quelques exemples de scénarios de mise à jour : l’ajout de nouveaux paramètres météorologiques dans le jeu de données du fournisseur météorologique, l’ajout de fonctionnalités (par exemple, ajout de la prévision sur 30 jours)

> [!NOTE]
> La suppression n’est pas prise en charge pour les métadonnées, par exemple, modèle de données météorologiques.
>
> Pour mettre à jour les métadonnées, il est obligatoire d’appeler /Get/{ID} sur le modèle de données météorologiques, de mettre à jour les propriétés modifiées, puis d’exécuter une commande /Put/{ID} afin que les propriétés définies par l’utilisateur ne soient pas perdues.

## <a name="weather-data-telemetry-specifications"></a>Spécifications des données météorologiques (télémétrie)

Les données météorologiques sont mappées à un message canonique envoyé (push) à Azure Event Hub pour traitement. Azure Event Hub est un service assurant l’ingestion de données en temps réel (télémétrie) à partir des appareils et applications connectés. Pour envoyer des données météorologiques à FarmBeats, vous devez créer un client qui envoie des messages à un Event Hub dans FarmBeats. Pour en savoir plus sur l’envoi de données de télémétrie, consultez [Envoi de données de télémétrie à un Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)

Voici un exemple de code Python qui envoie des données de télémétrie en tant que client à un Event Hub spécifié.

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
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Par exemple, voici un message de télémétrie :

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Résolution des problèmes et gestion des erreurs

**Journalisation des erreurs**

Étant donné que le travail du partenaire s’exécute dans l’infrastructure de travail existante, les erreurs sont enregistrées de la même façon que les erreurs d’autres travaux préexistants dans FarmBeats (comme GetFarmData, SensorPlacement, etc.). L’activité ADF en cours d’exécution dans le pipeline ADF journalise STDERR et STDOUT. Les deux fichiers sont disponibles dans le compte de stockage « datahublogs-xxx » au sein du groupe de ressources FarmBeats.

La bibliothèque DataHub fournit des fonctions d’assistance pour activer la journalisation dans le cadre des journaux DataHub généraux. Plus de détails [ici](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

**Support ou option de résolution de problèmes**

Si le client n’est pas en mesure de recevoir des données météorologiques dans l’instance FarmBeats spécifiée, le partenaire météorologique doit fournir une assistance et un mécanisme de résolution du problème.

## <a name="add-extendedtype"></a>Ajouter ExtendedType

FarmBeats prend en charge l’ajout de nouveaux types et unités de mesure de capteur. Notez qu’un partenaire météorologique peut ajouter de nouvelles unités/nouveaux types en mettant à jour le fichier bootstrap_manifest.json dans l’implémentation de référence [ici](https://github.com/azurefarmbeats/noaa_docker)

Pour ajouter un nouveau type WeatherMeasure, par exemple « PrecipitationDepth », suivez les étapes ci-dessous.

1. Effectuez une demande GET sur /ExtendedType avec le filtre et la clé pour la demande = WeatherMeasureType
2. Notez l’ID de l’objet retourné.
3. Ajoutez le nouveau type à la liste dans l’objet retourné et effectuez une demande PUT sur /ExtendedType{ID} avec la nouvelle liste suivante. La charge utile en entrée doit être la même que la réponse reçue ci-dessus et la nouvelle unité doit être ajoutée à la fin de la liste des valeurs.

Pour plus d’informations sur l’API /ExtendedType, consultez [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Étapes suivantes

Vous disposez maintenant d’un Docker Connecteur qui s’intègre à FarmBeats. Vous pouvez ensuite voir comment obtenir des données météorologiques à l’aide de votre Docker dans FarmBeats. Consultez [Obtenir des données météorologiques](get-weather-data-from-weather-partner.md).
