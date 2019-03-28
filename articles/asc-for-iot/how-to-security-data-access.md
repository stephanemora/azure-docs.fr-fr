---
title: L’accès aux données à l’aide de ASC pour IoT Preview | Microsoft Docs
description: Découvrez comment accéder à vos données d’alerte et des recommandations de sécurité lorsque vous utilisez ASC pour IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541991"
---
# <a name="access-your-security-data"></a>Accéder à vos données de sécurité 

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC pour IoT stocke les données de sécurité brute, les recommandations et les alertes de sécurité (si vous choisissez d’enregistrer) dans votre espace de travail Analytique de journal.

## <a name="log-analytics"></a>Log Analytics

Pour configurer l’espace de travail Analytique de journal est utilisé :

1. Ouvrez votre IoT Hub.
1. Cliquez sur **sécurité**
2. Cliquez sur **paramètres**et modifier la configuration de votre espace de travail Analytique de journal.

Pour accéder à votre espace de travail Analytique de journal après la configuration :

1. Sélectionnez une alerte de ASC pour IoT. 
2. Cliquez sur **d’autres recherches menées**, puis cliquez sur **pour voir quels sont les appareils sur lesquels cette alerte Cliquez ici et afficher la colonne DeviceId**.

Pour plus d’informations sur l’interrogation des données à partir de l’Analytique de journal, consultez [prise en main des requêtes dans le journal Analytique](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alertes de sécurité

Alertes de sécurité sont stockées dans le **ASCforIoT.SecurityAlert** table au sein de votre espace de travail Analytique de journal configuré.

Utiliser les requêtes de base kql suivantes pour commencer à Explorer les alertes de sécurité.

### <a name="sample-records-query"></a>Exemple de requête enregistrements

Pour sélectionner des enregistrements au hasard : 

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

#### <a name="sample-query-results"></a>Résultats de l’exemple de requête 

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Description                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Attaque par force brute a réussi           | Une attaque de force Brute sur l’appareil a réussi.        |    {« Adresse Source complet » : « [\"10.165.12.18 :\"] », « Noms d’utilisateur » : « [\"\"] », « DeviceId » : "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Connexion locale réussie sur l’appareil      | Une connexion locale réussie à l’appareil a été détectée.     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Échec de la tentative de connexion locale sur l’appareil  | Une tentative de connexion locale ayant échoué à l’appareil a été détectée. |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary-query"></a>Requête de résumé d’appareil

Utilisez cette requête kql pour sélectionner un nombre d’alertes de sécurité distinctes a détecté la semaine dernière par IoT Hub, appareil, gravité d’alerte et le type d’alerte.

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

#### <a name="device-summary-query-results"></a>Résultats de requête de résumé de périphérique

| IoTHubId | deviceId| AlertSeverity| DisplayName | Nombre |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Attaque par force brute a réussi           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne        | Échec de la tentative de connexion locale sur l’appareil  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Élevé          | Connexion locale réussie sur l’appareil      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Moyenne        | Chiffrement médaille Miner                     | 4   |
|

### <a name="iot-hub-summary"></a>Résumé de l’IoT Hub

Utilisez cette requête kql pour sélectionner un nombre d’appareils distincts ayant des alertes dans la dernière semaine par IoT hub, gravité d’alerte, type d’alerte :

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

#### <a name="iot-hub-summary-query-results"></a>Résultats de la requête de résumé IoT Hub

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Élevé          | Attaque par force brute a réussi           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Moyenne        | Échec de la tentative de connexion locale sur l’appareil  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Élevé          | Connexion locale réussie sur l’appareil      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Moyenne        | Chiffrement médaille Miner                     | 1          |



## <a name="next-steps"></a>Étapes suivantes

- Lire l’ASC pour IoT [vue d’ensemble](overview.md)
- En savoir plus sur ASC pour IoT [Architecture](architecture.md)
- Comprendre et Explorer [ASC pour les alertes de l’IoT](concept-security-alerts.md)
