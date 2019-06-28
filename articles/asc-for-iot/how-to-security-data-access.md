---
title: Accéder aux données à l’aide d’Azure Security Center pour IoT (préversion) | Microsoft Docs
description: Découvrez comment accéder à vos données d’alerte et de recommandation de sécurité lors de l’utilisation d’Azure Security Center pour IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 1ec6a174d05f8707bbffcc9fb013a98c2eb9196c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200558"
---
# <a name="access-your-security-data"></a>Accéder à vos données de sécurité 

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) pour IoT stocke les alertes de sécurité, les recommandations et les données de sécurité brutes (si vous choisissez de les enregistrer) dans votre espace de travail Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Pour configurer l’espace de travail Log Analytics utilisé :

1. Ouvrez votre IoT Hub.
1. Cliquez sur **Sécurité**
2. Cliquez sur **Paramètres** et changez la configuration de votre espace de travail Log Analytics.

Pour accéder à votre espace de travail Log Analytics après la configuration :

1. Choisissez une alerte ou une recommandation dans ASC pour IoT. 
2. Cliquez sur **Investigation poussée**, puis sur **Pour voir quels sont les appareils qui ont cette alerte, cliquez ici et consultez la colonne DeviceId**.

Pour plus d’informations sur l’interrogation des données à partir de Log Analytics, consultez [Bien démarrer avec les requêtes dans Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alertes de sécurité

Les alertes de sécurité sont stockées dans la table _AzureSecurityOfThings.SecurityAlert_ dans l’espace de travail Log Analytics configuré pour la solution ASC pour IoT.

Nous avons fourni plusieurs requêtes utiles pour vous aider à commencer à explorer les alertes de sécurité.

### <a name="sample-records"></a>Exemples d’enregistrements

Sélectionnez quelques enregistrements aléatoires

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
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Attaque par force brute réussie           | Une attaque par force brute sur l’appareil a réussi        |    { "Full Source Address": "[\"10.165.12.18:\"]", "User Names": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Connexion locale réussie sur l’appareil      | Une connexion locale réussie à l’appareil a été détectée     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Échec de la tentative de connexion locale sur l’appareil  | Une tentative échouée de connexion locale à l’appareil a été détectée |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Récapitulatif de l’appareil

Sélectionnez le nombre d’alertes de sécurité distinctes détectées la semaine dernière par IoT Hub, appareil, gravité d’alerte et type d’alerte.

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Attaque par force brute réussie           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne        | Échec de la tentative de connexion locale sur l’appareil  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Connexion locale réussie sur l’appareil      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne        | Crypto Coin Miner                     | 4   |

### <a name="iot-hub-summary"></a>Récapitulatif IoT Hub

Sélectionnez plusieurs appareils distincts ayant eu des alertes la dernière semaine, par IoT Hub, gravité d’alerte, type d’alerte

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Élevé          | Attaque par force brute réussie           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Moyenne        | Échec de la tentative de connexion locale sur l’appareil  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Élevé          | Connexion locale réussie sur l’appareil      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Moyenne        | Crypto Coin Miner                     | 1          |

## <a name="security-recommendations"></a>Recommandations de sécurité

Les recommandations de sécurité sont stockées dans la table _AzureSecurityOfThings.SecurityRecommendation_ dans l’espace de travail Log Analytics configuré pour la solution ASC pour IoT.

Nous avons fourni plusieurs requêtes utiles pour vous aider à commencer à explorer les recommandations de sécurité.

### <a name="sample-records"></a>Exemples d’enregistrements

Sélectionnez quelques enregistrements aléatoires

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
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne | Actif | Règle de pare-feu permissive dans la chaîne d’entrée détectée | Une règle dans le pare-feu contenant un modèle permissif pour un large éventail d’adresses IP ou de ports a été trouvée | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne | Actif | Règle de pare-feu permissive dans la chaîne d’entrée détectée | Une règle dans le pare-feu contenant un modèle permissif pour un large éventail d’adresses IP ou de ports a été trouvée | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Récapitulatif de l’appareil

Sélectionnez le nombre de recommandations de sécurité actives distinctes par IoT Hub, appareil, gravité de la recommandation et type.

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

- Lire la [vue d’ensemble](overview.md) d’ASC pour IoT
- Découvrir [l’architecture](architecture.md) ASC pour IoT
- Comprendre et explorer les [alertes ASC pour IoT](concept-security-alerts.md)
- Comprendre et explorer les [recommandations ASC pour IoT](concept-recommendations.md)