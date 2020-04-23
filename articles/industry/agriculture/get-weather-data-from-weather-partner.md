---
title: Obtenir des données météorologiques auprès de partenaires météo
description: Cet article décrit comment obtenir des données météorologiques auprès de partenaires.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265069"
---
# <a name="get-weather-data-from-weather-partners"></a>Obtenir des données météorologiques auprès de partenaires météo

Azure FarmBeats vous permet d’importer des données météorologiques à partir de vos fournisseurs de données météorologiques à l’aide d’un framework de connecteur basé sur Docker. À l’aide de ce framework, les fournisseurs de données météorologiques implémentent un Docker qui peut être intégré à FarmBeats. Actuellement, les fournisseurs de données météorologiques suivants sont pris en charge :

[Données NOAA d’Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)

Les données météorologiques peuvent être utilisées pour générer des insights actionnables et créer des modèles AI/ML sur FarmBeats.

## <a name="before-you-start"></a>Avant de commencer

Pour obtenir des données météorologiques, vérifiez que vous avez installé FarmBeats. **L’intégration des données météorologiques est prise en charge dans la version 1.2.11 ou supérieure**. Pour installer Azure FarmBeats, consultez [Installer FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Activer l’intégration des données météorologiques avec FarmBeats

Pour commencer à obtenir des données météorologiques sur votre hub de données FarmBeats, effectuez les étapes suivantes :

1. Accédez à votre swagger de hub de données FarmBeats (https://yourdatahub.azurewebsites.net/swagger)

2. Accédez à l’API /Partner et effectuez une requête POST avec la charge utile d’entrée suivante :

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Par exemple, pour obtenir des données météorologiques de NOAA par Azure Open Datasets, utilisez la charge utile ci-dessous. Vous pouvez modifier le nom et la description en fonction de vos préférences.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Pour plus d’informations sur l’objet Partner, consultez [Annexe.](get-weather-data-from-weather-partner.md#appendix)

   L’étape précédente provisionne les ressources pour permettre à Docker de s’exécuter dans l’environnement FarmBeats du client.  

   Il faut environ 10 à 15 minutes pour provisionner les ressources ci-dessus.

3. Vérifiez l’état de l’objet /Partner que vous avez créé à l’étape 2. Pour ce faire, effectuez une requête GET sur l’API /Partner et vérifiez l’**état** de l’objet Partner. Une fois que FarmBeats a provisionné le partenaire, l’état devient **Active**.

4. Accédez à l’API /JobType et effectuez une requête GET sur cette API. Vérifiez les travaux météorologiques créés dans le cadre du processus d’ajout de partenaires à l’étape 2. Le champ **pipelineName** dans les travaux météorologiques aura le format suivant : « nom-partenaire_type-partenaire_nom-travail ».

5. Votre instance FarmBeats a maintenant un partenaire de données météorologiques actif, et vous pouvez exécuter des travaux pour demander des données météorologiques pour un lieu particulier (latitude/longitude) et une plage de dates. Les JobTypes auront des détails sur les paramètres requis pour exécuter des travaux météorologiques.

   Par exemple, pour les données NOAA d’Azure Open Datasets, les JobTypes suivants seront créés :

   - get_weather_data (obtenir des données météorologiques historiques/IFS)
   - get_weather_forecast_data (obtenir des données de prévisions météorologiques/GFS)

6. Prenez note de l’**ID** et des paramètres des JobTypes.

7. Accédez à l’API /Jobs et effectuez une requête POST sur /Jobs avec la charge utile d’entrée suivante :

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Par exemple, pour exécuter **get_weather_data**, utilisez la charge utile suivante :

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. L’étape précédente exécutera les travaux météorologiques tels qu’ils sont définis dans le Docker du partenaire, et ingérera les données météorologiques dans FarmBeats. Vous pouvez vérifier l’état du travail en effectuant une requête GET sur/Jobs et en recherchant **currentState** dans la réponse. Une fois l’opération terminée, currentState prend la valeur **Succeeded**.

## <a name="query-ingested-weather-data"></a>Données météorologiques ingérées par la requête

Une fois les travaux météorologiques terminés, vous pouvez interroger les données météorologiques ingérées pour créer des modèles ou des insights actionnables. Il existe deux façons d’accéder aux données météorologiques et de les interroger à partir de FarmBeats :

- API ; et
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Interroger à l’aide des API REST

Pour interroger les données météorologiques à l’aide de l’API REST FarmBeats, effectuez les étapes suivantes :

1. Dans votre swagger de hub de données FarmBeats (https://yourdatahub.azurewebsites.net/swagger), accédez à l’API /WeatherDataLocation et effectuez une requête GET. La réponse aura un ou plusieurs objets /WeatherDataLocation créés pour le lieu (latitude/longitude) qui a été spécifié dans le cadre de l’exécution du travail. Prenez note de l’**ID** et du **weatherDataModelId** des objets.

2. Effectuez une requête GET/{id} sur l’API /WeatherDataModel pour le **weatherDataModelId** noté à l’étape 1. Le modèle de données météorologiques contient toutes les métadonnées et les détails sur les données météorologiques ingérées. Par exemple, **Weather Measure** dans l’objet **Weather Data Model** contient des détails sur les informations météorologiques prises en charge, et dans quels types et unités. Par exemple,

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Prenez note de la réponse de l’appel GET/{id} pour le modèle de données météorologiques.

3. Accédez à l’API **Telemetry** et effectuez une requête POST avec la charge utile d’entrée suivante :

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. La réponse contenant les données météorologiques disponibles pour l’intervalle de temps spécifié se présentera comme suit :

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

Dans l’exemple précédent, la réponse contient des données pour deux horodatages, ainsi que le nom de la mesure (« Temperature ») et les valeurs des données météorologiques signalées pour les deux horodatages. Vous devrez faire référence au modèle de données météorologiques associé (comme décrit à l’étape 2 ci-dessus) pour interpréter le type et l’unité des valeurs signalées.

### <a name="query-using-azure-time-series-insights-tsi"></a>Interroger à l’aide d’Azure Time Series Insights (TSI)

FarmBeats utilise [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) pour ingérer, stocker, interroger et visualiser les données à l’échelle IoT, données qui sont hautement contextuelles et optimisées pour les séries chronologiques.

Les données météorologiques sont reçues sur un EventHub, puis transmises à un environnement TSI au sein du groupe de ressources FarmBeats. Les données peuvent ensuite être interrogées directement à partir de STI. Pour plus d’informations, consultez la [documentation sur TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Procédez comme suit pour visualiser les données sur TSI :

1. Accédez au **portail Azure** > **Groupe de ressources FarmBeats Datahub** > sélectionnez l’environnement **Time Series Insights** (tsi-xxxx) > **Stratégies d’accès aux données**. Ajoutez un utilisateur avec un accès Lecteur ou Contributeur.

2. Accédez à la page **Présentation** de l’environnement **Time Series Insights** (tsi-xxxx) et sélectionnez l’**URL de l’Explorateur Time Series Insights**. Vous pouvez maintenant visualiser les données météorologiques ingérées.

Outre le stockage, l’interrogation et la visualisation des données météorologiques, TSI autorise également l’intégration à un tableau de bord Power BI. Pour plus d’informations, consultez [Visualiser des données à partir de Time Series Insights dans Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Annexe

|        Partenaire   |  Détails   |
| ------- | -------             |
|     DockerDetails - imageName         |          Nom de l’image Docker. Par exemple, docker.io/azurefarmbeats/farmbeats-noaa (image in hub.docker.com) OU myazureacr.azurecr.io/mydockerimage (image dans Azure Container Registry), et ainsi de suite. Si aucun registre n’est fourni, la valeur par défaut est hub.docker.com      |
|          DockerDetails - imageTag             |         Nom d’étiquette de l’image Docker. La valeur par défaut est « latest »     |
|  DockerDetails - credentials      |  Informations d’identification pour accéder au Docker privé. Elles seront fournies au client par le partenaire   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Référence SKU de machine virtuelle Azure Batch Consultez [cette page](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour connaître toutes les machines virtuelles Linux disponibles. Les valeurs autorisées sont : "Small', 'ExtraLarge', 'Large', 'A8', 'A9', 'Medium', 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'A11', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_G3', 'STANDARD_G4', 'STANDARD_G5', 'STANDARD_D5_V2', 'BASIC_A1', 'BASIC_A2', 'BASIC_A3', 'BASIC_A4', 'STANDARD_A1', 'STANDARD_A2', 'STANDARD_A3', 'STANDARD_A4', 'STANDARD_A5', 'STANDARD_A6', 'STANDARD_A7', 'STANDARD_A8', 'STANDARD_A9', 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_D15_V2', 'STANDARD_F1', 'STANDARD_F2', 'STANDARD_F4', 'STANDARD_F8', 'STANDARD_F16', 'STANDARD_NV6', 'STANDARD_NV12', 'STANDARD_NV24', 'STANDARD_NC6', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24r', 'STANDARD_H8', 'STANDARD_H8m', 'STANDARD_H16', 'STANDARD_H16m', 'STANDARD_H16mr', 'STANDARD_H16r', 'STANDARD_A1_V2', 'STANDARD_A2_V2', 'STANDARD_A4_V2', 'STANDARD_A8_V2', 'STANDARD_A2m_V2', 'STANDARD_A4m_V2', 'STANDARD_A8m_V2', 'STANDARD_M64ms', 'STANDARD_M128s', 'STANDARD_D2_V3'. **La valeur par défaut est « standard_d2_v2 »**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Nombre de nœuds d’ordinateur dédiés pour le pool Batch. La valeur par défaut est 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    ID de référence SKU de l’agent du nœud Azure Batch. Actuellement, seul l’agent de nœud Batch « batch.node.ubuntu 18.04 » est pris en charge.    |
| DockerDetails - partnerCredentials | Informations d’identification pour l’appel de l’API partenaire dans Docker. Le partenaire doit fournir ces informations à ses clients en fonction du mécanisme d’authentification pris en charge, par exemple nom d’utilisateur/mot de passe ou clés API. |
| partnerType | « Weather » (les autres types de partenaires dans FarmBeats sont « Sensor » et « Imagery »).  |
|  name   |   Nom souhaité du partenaire dans le système FarmBeats.   |
|  description |  Description   |

## <a name="next-steps"></a>Étapes suivantes

Vous avez interrogé les données de capteur à partir de votre instance d’Azure FarmBeats. À présent, découvrez comment [générer des cartes](generate-maps-in-azure-farmbeats.md#generate-maps) pour vos fermes.
