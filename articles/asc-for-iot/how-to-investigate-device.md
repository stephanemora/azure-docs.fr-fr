---
title: Guide d’enquête des appareils Azure Security Center pour l’IoT (préversion) | Microsoft Docs
description: Ce guide pratique explique comment utiliser Azure Security Center pour l’IoT afin d’examiner un appareil IoT suspect avec Log Analytics.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: mlottner
ms.openlocfilehash: 15e65c155a98ae12c156587735d34a16ed2c9109
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192653"
---
# <a name="investigate-a-suspicious-iot-device"></a>Examiner un appareil IoT suspect

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les alertes et les preuves d’Azure Security Center (ASC) pour l’IoT donnent des indications claires quant à la possibilité qu’un appareil IoT soit compromis ou impliqué dans des activités suspectes. 

Dans ce guide, utilisez les suggestions d’enquête fournies afin de déterminer les risques potentiels pour votre organisation, de choisir le mode de correction adéquat et de vous prémunir au mieux contre des attaques similaires.  

> [!div class="checklist"]
> * Rechercher les données de votre appareil
> * Effectuer l’examen à l’aide de requêtes kql


## <a name="how-can-i-access-my-data"></a>Accéder à ses données

Par défaut, ASC pour IoT stocke vos recommandations et alertes de sécurité dans votre espace de travail Log Analytics. Vous pouvez également choisir de stocker vos données de sécurité brutes.

Pour rechercher l’espace de travail Log Analytics abritant les données :

1. Ouvrez votre hub IoT. 
1. Sous **Sécurité**, cliquez sur **Vue d’ensemble**, puis sélectionnez **Paramètres**.
1. Changez les détails de la configuration de votre espace de travail Log Analytics. 
1. Cliquez sur **Enregistrer**. 

Une fois la configuration effectuée, procédez comme suit pour accéder aux données stockées dans votre espace de travail Log Analytics :

1. Dans votre hub IoT, cliquez sur une alerte ASC pour IoT. 
1. Cliquez sur **Investigation poussée**. 
1. Sélectionnez **Pour voir quels sont les appareils qui ont cette alerte, cliquez ici et consultez la colonne DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Étapes de l’examen des appareils IoT suspects

Rendez-vous dans votre espace de travail Log Analytics [pour accéder aux données](#how-can-i-access-my-data) brutes et aux insights de vos appareils IoT.

Examinez les données des appareils et recherchez les informations et les activités suivantes à l’aide des requêtes kql ci-dessous.

### <a name="related-alerts"></a>Alertes associées

Pour savoir si d’autres alertes ont été déclenchées à peu près au même moment, utilisez la requête kql suivante :

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>Utilisateurs disposant d’un accès

Pour savoir quels utilisateurs ont accès à un appareil spécifique, utilisez la requête kql suivante : 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
  ~~~
Ces données répondent aux questions suivantes : 
  1. Quels sont les utilisateurs ayant accès à l’appareil ?
  2. Les utilisateurs disposant d’un accès ont-ils les niveaux d’autorisation attendus ? 

### <a name="open-ports"></a>Ouvrir les ports

Pour savoir quels ports de l’appareil sont en cours d’utilisation ou ont été utilisés, exécutez la requête kql suivante : 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
  ~~~

    Use this data to discover:
  1. Quels sont les sockets d’écoute actifs sur l’appareil ?
  2. Les sockets d’écoute actuellement actifs sont-ils censés être autorisés ?
  3. Y a-t-il des adresses distantes suspectes connectées à l’appareil ?

### <a name="user-logins"></a>Connexions utilisateur

Pour savoir quels utilisateurs se sont connectés à l’appareil, utilisez la requête kql suivante : 
 
  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
  ~~~

    Use the query results to discover:
  1. Quels sont les utilisateurs qui se sont connectés à l’appareil ?
  2. Les utilisateurs qui se sont connectés sont-ils censés le faire ?
  3. Ces utilisateurs se sont-ils connectés à partir d’adresses IP attendues ou inattendues ?
  
### <a name="process-list"></a>Liste de processus

Pour savoir si la liste de processus est conforme à ce qui est attendu, utilisez la requête kql suivante : 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
  ~~~

    Use the query results to discover:

  1. Des processus suspects se sont-ils exécutés sur l’appareil ?
  2. Les processus ont-ils été exécutés par les utilisateurs appropriés ?
  3. Les exécutions de ligne de commande contenaient-elles les arguments corrects et attendus ?

## <a name="next-steps"></a>Étapes suivantes

Après avoir examiné un appareil pour mieux comprendre les risques que vous encourez, vous pouvez envisager de [configurer des alertes personnalisées](quickstart-create-custom-alerts.md) pour améliorer la sécurité de votre solution IoT. Si vous ne disposez pas d’un agent d’appareil, envisagez de [déployer un agent de sécurité](how-to-deploy-agent.md) ou de [changer la configuration d’un agent d’appareil existant](how-to-agent-configuration.md) afin d’améliorer vos résultats. 