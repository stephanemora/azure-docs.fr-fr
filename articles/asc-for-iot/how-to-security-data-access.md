---
title: L’accès aux données à l’aide d’Azure Security Center pour la version préliminaire IoT | Microsoft Docs
description: Découvrez comment accéder à vos données d’alerte et des recommandations de sécurité lors de l’utilisation d’Azure Security Center pour IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 0788a62de701cb85a65c7053006e28cf4fffe5cb
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862588"
---
# <a name="access-your-security-data"></a>Accéder à vos données de sécurité 

> [!IMPORTANT]
> Azure Security Center pour IoT est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) pour IoT stocke les données de sécurité brute, les recommandations et les alertes de sécurité (si vous choisissez d’enregistrer) dans votre espace de travail Analytique de journal.

## <a name="log-analytics"></a>Log Analytics

Pour configurer l’espace de travail Analytique de journal est utilisé :

1. Ouvrez votre IoT Hub.
1. Cliquez sur **sécurité**
2. Cliquez sur **paramètres**et modifier la configuration de votre espace de travail Analytique de journal.

Pour accéder à votre espace de travail Analytique de journal après la configuration :

1. Choisir une alerte ou une recommandation dans ASC pour IoT. 
2. Cliquez sur **d’autres recherches menées**, puis cliquez sur **pour voir quels sont les appareils sur lesquels cette alerte Cliquez ici et afficher la colonne DeviceId**.

Pour plus d’informations sur l’interrogation des données à partir de l’Analytique de journal, consultez [prise en main des requêtes dans le journal Analytique](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alertes de sécurité

Alertes de sécurité sont stockées dans _AzureSecurityOfThings.SecurityAlert_ table dans l’espace de travail Analytique de journal configurée pour l’ASC pour la solution IoT.

Nous avons fourni un nombre de requêtes utiles pour vous aider à commencer à Explorer les alertes de sécurité.

### <a name="sample-records"></a>Exemples d’enregistrements

Sélectionnez quelques enregistrements aléatoire

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Description                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Attaque par force brute a réussi           | Une attaque de force Brute sur l’appareil a réussi.        |    {« Adresse Source complet » : « [\"10.165.12.18 :\"] », « Noms d’utilisateur » : « [\"\"] », « DeviceId » : "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Connexion locale réussie sur l’appareil      | Une connexion locale réussie à l’appareil a été détectée.     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Échec de la tentative de connexion locale sur l’appareil  | Une tentative de connexion locale ayant échoué à l’appareil a été détectée. |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Résumé de l’appareil

Sélectionnez le nombre d’alertes de sécurité distincts a détecté la semaine dernière par IoT Hub, appareil, gravité d’alerte et le type d’alerte.

```
// Select number of distinct security alerts detected last week by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Nombre |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Attaque par force brute a réussi           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne        | Échec de la tentative de connexion locale sur l’appareil  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Connexion locale réussie sur l’appareil      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne        | Chiffrement médaille Miner                     | 4   |

### <a name="iot-hub-summary"></a>IoT hub résumé

Sélectionnez un nombre d’appareils distincts ayant des alertes dans la dernière semaine, par IoT Hub, gravité d’alerte, type d’alerte

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Élevé          | Attaque par force brute a réussi           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Moyenne        | Échec de la tentative de connexion locale sur l’appareil  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Élevé          | Connexion locale réussie sur l’appareil      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Moyenne        | Chiffrement médaille Miner                     | 1          |

## <a name="security-recommendations"></a>Recommandations de sécurité

Recommandations de sécurité sont stockées dans _AzureSecurityOfThings.SecurityRecommendation_ table dans l’espace de travail Analytique de journal configurée pour l’ASC pour la solution IoT.

Nous vous avons fourni un nombre de requêtes utiles qui vous aideront à Explorer les recommandations de sécurité de démarrage.

### <a name="sample-records"></a>Exemples d’enregistrements

Sélectionnez quelques enregistrements aléatoire

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | IoTHubId | deviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Description | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne | Actif | Règle de pare-feu permissifs dans la chaîne d’entrée a été trouvé | Une règle dans le pare-feu a été trouvée qui contient un modèle permissif pour une large gamme de ports ou d'adresses IP | {« Règles » : « [{\"adresse source\":\"\",\"PortSource\":\"\",\"DestinationAddress\":\" \" \"DestinationPort\":\"1337\"}] »} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne | Actif | Règle de pare-feu permissifs dans la chaîne d’entrée a été trouvé | Une règle dans le pare-feu a été trouvée qui contient un modèle permissif pour une large gamme de ports ou d'adresses IP | {« Règles » : « [{\"adresse source\":\"\",\"PortSource\":\"\",\"DestinationAddress\":\" \" \"DestinationPort\":\"1337\"}] »} |

### <a name="device-summary"></a>Résumé de l’appareil

Sélectionnez le nombre de recommandations de sécurité active distinctes par IoT Hub, appareil, la gravité de la recommandation et type.

```
// Select number of distinct active security recommendations by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | RecommendationSeverity | Nombre |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne        | 4   |


## <a name="next-steps"></a>Étapes suivantes

- Lire l’ASC pour IoT [vue d’ensemble](overview.md)
- En savoir plus sur ASC pour IoT [Architecture](architecture.md)
- Comprendre et Explorer [ASC pour les alertes de l’IoT](concept-security-alerts.md)
- Comprendre et Explorer [ASC pour IoT recommandation](concept-recommendations.md)