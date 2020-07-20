---
title: Obtenir des données météorologiques auprès de partenaires météo
description: Cet article décrit comment obtenir des données météorologiques auprès de partenaires.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 7666ee1a81c2ed93ee5e246b3ec79f056f9d63ab
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187776"
---
# <a name="get-weather-data-from-weather-partners"></a>Obtenir des données météorologiques auprès de partenaires météo

Azure FarmBeats vous permet d’importer des données météorologiques à partir de vos fournisseurs de données météorologiques à l’aide d’un framework de connecteur basé sur Docker. À l’aide de ce framework, les fournisseurs de données météorologiques implémentent un Docker qui peut être intégré à FarmBeats. Actuellement, les fournisseurs de données météorologiques suivants sont pris en charge :

  ![DTN](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)

  [DTN](https://www.dtn.com/dtn-content-integration/)

Les données météorologiques peuvent être utilisées pour générer des insights actionnables et créer des modèles AI/ML sur FarmBeats.

## <a name="before-you-start"></a>Avant de commencer

Pour obtenir des données météorologiques, vérifiez que vous avez installé FarmBeats. **L’intégration des données météorologiques est prise en charge dans la version 1.2.11 ou supérieure**. Pour installer Azure FarmBeats, consultez [Installer FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Activer l’intégration des données météorologiques avec FarmBeats

Pour commencer à obtenir des données météorologiques sur votre hub de données FarmBeats, effectuez les étapes suivantes :

1. Accédez à votre swagger de hub de données FarmBeats (https://farmbeatswebsite-api.azurewebsites.net/swagger)

2. Accédez à l’API /Partner et effectuez une requête POST avec la charge utile d’entrée suivante :

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
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

   Par exemple, pour obtenir des données météorologiques de DTN, utilisez la charge utile ci-dessous. Vous pouvez modifier le nom et la description en fonction de vos préférences.

   > [!NOTE]
   > L’étape ci-dessous nécessite une clé API. Veuillez contacter DTN afin d’en obtenir une pour votre abonnement DTN.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Pour plus d’informations sur l’objet Partner, consultez [Annexe.](get-weather-data-from-weather-partner.md#appendix)

   L’étape précédente provisionne les ressources pour permettre à Docker de s’exécuter dans l’environnement FarmBeats du client.  

   Il faut environ 10 à 15 minutes pour provisionner les ressources ci-dessus.

3. Vérifiez l’état de l’objet /Partner que vous avez créé à l’étape 2. Pour ce faire, effectuez une requête GET sur l’API /Partner et vérifiez l’**état** de l’objet Partner. Une fois que FarmBeats a provisionné le partenaire, l’état devient **Active**.

4. Accédez à l’API /JobType et effectuez une requête GET sur cette API. Vérifiez les travaux météorologiques créés dans le cadre du processus d’ajout de partenaires à l’étape 2. Le champ **pipelineName** dans les travaux météorologiques aura le format suivant : « nom-partenaire_type-partenaire_nom-travail ».

5. Votre instance FarmBeats a maintenant un partenaire de données météorologiques actif, et vous pouvez exécuter des travaux pour demander des données météorologiques pour un lieu particulier (latitude/longitude) et une plage de dates. Les JobTypes auront des détails sur les paramètres requis pour exécuter des travaux météorologiques.

   Par exemple, pour DTN, les JobType suivants vont être créés :
   
   - get_dtn_daily_observations (Obtenir des observations quotidiennes pour un lieu et une période)
   - get_dtn_daily_forecasts (Obtenir des prévisions quotidiennes pour un lieu et une période)
   - get_dtn_hourly_observations (Obtenir des observations horaires pour un lieu et une période)
   - get_dtn_hourly_forecasts (Obtenir des prévisions horaires pour un lieu et une période)

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

   Par exemple, pour exécuter **get_dtn_daily_observations**, utilisez la charge utile suivante :

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. L’étape précédente exécutera les travaux météorologiques tels qu’ils sont définis dans le Docker du partenaire, et ingérera les données météorologiques dans FarmBeats. Vous pouvez vérifier l’état du travail en effectuant une requête GET sur/Jobs et en recherchant **currentState** dans la réponse. Une fois l’opération terminée, currentState prend la valeur **Succeeded**.

## <a name="query-ingested-weather-data"></a>Données météorologiques ingérées par la requête

Une fois les travaux météorologiques effectués, vous pouvez interroger les données météorologiques ingérées pour générer des modèles ou des insights actionnables à l’aide des API REST de FarmBeats Datahub.

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

## <a name="troubleshoot-job-failures"></a>Résoudre les échecs liés aux travaux

Pour résoudre les échecs liés aux travaux, vous pouvez consulter les journaux des travaux. Suivez les étapes présentées [ici](troubleshoot-azure-farmbeats.md#weather-data-job-failures).


## <a name="appendix"></a>Annexe

|        Partenaire   |  Détails   |
| ------- | -------             |
|     DockerDetails - imageName         |          Nom de l’image Docker. Par exemple docker.io/mydockerimage (image dans hub.docker.com) OU myazureacr.azurecr.io/mydockerimage (image dans Azure Container Registry), etc. Si aucun registre n’est fourni, la valeur par défaut est hub.docker.com      |
|          DockerDetails - imageTag             |         Nom d’étiquette de l’image Docker. La valeur par défaut est « latest »     |
|  DockerDetails - credentials      |  Informations d’identification pour accéder au Docker privé. Elles seront fournies au client par le partenaire   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Référence SKU de machine virtuelle Azure Batch Consultez [cette page](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour connaître toutes les machines virtuelles Linux disponibles. Les valeurs autorisées sont : "Small', 'ExtraLarge', 'Large', 'A8', 'A9', 'Medium', 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'A11', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_G3', 'STANDARD_G4', 'STANDARD_G5', 'STANDARD_D5_V2', 'BASIC_A1', 'BASIC_A2', 'BASIC_A3', 'BASIC_A4', 'STANDARD_A1', 'STANDARD_A2', 'STANDARD_A3', 'STANDARD_A4', 'STANDARD_A5', 'STANDARD_A6', 'STANDARD_A7', 'STANDARD_A8', 'STANDARD_A9', 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_D15_V2', 'STANDARD_F1', 'STANDARD_F2', 'STANDARD_F4', 'STANDARD_F8', 'STANDARD_F16', 'STANDARD_NV6', 'STANDARD_NV12', 'STANDARD_NV24', 'STANDARD_NC6', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24r', 'STANDARD_H8', 'STANDARD_H8m', 'STANDARD_H16', 'STANDARD_H16m', 'STANDARD_H16mr', 'STANDARD_H16r', 'STANDARD_A1_V2', 'STANDARD_A2_V2', 'STANDARD_A4_V2', 'STANDARD_A8_V2', 'STANDARD_A2m_V2', 'STANDARD_A4m_V2', 'STANDARD_A8m_V2', 'STANDARD_M64ms', 'STANDARD_M128s', 'STANDARD_D2_V3'. **La valeur par défaut est « standard_d2_v2 »**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Non. de nœuds d’ordinateur dédiés pour le pool Batch. La valeur par défaut est 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    ID de référence SKU de l’agent du nœud Azure Batch. Actuellement, seul l’agent de nœud Batch « batch.node.ubuntu 18.04 » est pris en charge.    |
| DockerDetails - partnerCredentials | Informations d’identification pour l’appel de l’API partenaire dans Docker. Le partenaire doit fournir ces informations à ses clients en fonction du mécanisme d’authentification pris en charge, par exemple nom d’utilisateur/mot de passe ou clés API. |
| partnerType | « Weather » (les autres types de partenaires dans FarmBeats sont « Sensor » et « Imagery »).  |
|  name   |   Nom souhaité du partenaire dans le système FarmBeats.   |
|  description |  Description   |

## <a name="next-steps"></a>Étapes suivantes

Vous avez interrogé les données de capteur à partir de votre instance d’Azure FarmBeats. À présent, découvrez comment [générer des cartes](generate-maps-in-azure-farmbeats.md#generate-maps) pour vos fermes.
