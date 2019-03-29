---
title: ASC pour guide d’investigation appareil IoT Preview | Microsoft Docs
description: Cette procédure traitant explique comment ASC pour IoT permet d’analyser un appareil IoT suspect à l’aide d’Analytique de journal.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 032315d1b618b9716e23c8433c6ec1bf64b7e77d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580537"
---
# <a name="investigate-a-suspicious-iot-device"></a>Examiner un appareil IoT suspect

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC pour les alertes de service de IoT et les preuves fournissent des indications clair lorsque les appareils IoT sont suspectés d’implication dans les activités suspectes ou lorsque les indications existent qu’un appareil est compromis. 

Dans ce guide, utilisez les suggestions d’investigation fournies pour aider à déterminer les risques potentiels pour votre organisation, choisissez comment corriger et découvrir des meilleurs moyens d’éviter des attaques similaires à l’avenir.  

> [!div class="checklist"]
> * Rechercher des données de votre appareil
> * Envisagez d’utiliser des requêtes de kql


## <a name="how-can-i-access-my-data"></a>Comment puis-je accéder à mes données ?

Par défaut, ASC pour IoT stocke vos alertes de sécurité et des recommandations dans votre espace de travail Analytique de journal. Vous pouvez également choisir de stocker vos données de sécurité brute.

Pour localiser le stockage des données dans votre espace de travail Analytique de journal :

1. Ouvrez votre IoT hub 
1. Cliquez sur **sécurité**, puis sélectionnez **paramètres**.
1. Modifier les détails de configuration de votre espace de travail Analytique de journal. 
1. Cliquez sur **Enregistrer**. 

Après la configuration, procédez comme suit pour accéder aux données stockées dans votre espace de travail Analytique de journal :

1. Sélectionnez et cliquez sur un ASC pour l’alerte IoT dans votre IoT Hub. 
1. Cliquez sur **d’autres recherches menées**. 
1. Sélectionnez **pour voir quels sont les appareils sur lesquels cette alerte Cliquez ici et afficher la colonne DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Étapes d’investigation pour les appareils IoT suspectes

Pour accéder aux informations et les données brutes sur vos appareils IoT, accédez à votre espace de travail Analytique de journal [accéder à vos données](#how-can-i-access-my-data).

Vérifiez et recherchez les données d’appareil pour les informations suivantes et les activités à l’aide de requêtes kql suivantes.

### <a name="related-alerts"></a>Alertes associées

Pour en savoir plus, si les autres alertes ont été déclenchée autour de la même moment, utilisez la requête kql suivante :

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>Utilisateurs avec accès

Pour en savoir plus les utilisateurs ayant accès à cet usage de périphérique la requête kql suivante : 

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
Utilisez ces données pour découvrir : 
  1. Les utilisateurs ayant accès à l’appareil ?
  2. Est-ce que les utilisateurs avec accès ont les niveaux d’autorisation comme prévu ? 

### <a name="open-ports"></a>Ouvrir les ports

Pour rechercher hors quels ports dans l’appareil sont actuellement en cours d’utilisation ou a été utilisés utilisez la requête kql suivante : 

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
  1. Les sockets écoute sont actuellement actives sur l’appareil ?
  2. L’écoute des sockets qui sont actuellement actives doivent être autorisés ?
  3. Existe-t-il des toutes les adresses à distance suspectes connectés à l’appareil ?

### <a name="user-logins"></a>Connexions utilisateur

Pour en savoir plus les utilisateurs connectés à l’appareil utilisent la requête kql suivante : 
 
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
  1. Les utilisateurs connectés à l’appareil ?
  2. Les utilisateurs connectés sont censés se connecter ?
  3. Les utilisateurs connectés ne s’est connecté à partir des adresses IP inattendus ou non ?
  
### <a name="process-list"></a>Liste des processus

Pour savoir si la liste des processus joue usage prévu la requête kql suivante : 

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

  1. Y a-t-il eu tous les processus suspects en cours d’exécution sur l’appareil ?
  2. Processus ont été exécutés par les utilisateurs appropriés ?
  3. Les exécutions de la ligne de commande ne contenait les arguments corrects et attendus ?

## <a name="next-steps"></a>Étapes suivantes
Après examen d’un appareil et l’obtention d’une meilleure compréhension de vos risques, vous souhaiterez [configuration des alertes personnalisées](quickstart-create-custom-alerts.md) pour améliorer votre sécurité de solution IoT. Si vous ne disposez pas d’un agent de périphérique, envisagez [déploiement d’un agent de sécurité](how-to-deploy-agent.md) ou [modification de la configuration d’un agent de périphérique existant](how-to-agent-configuration.md) afin d’améliorer vos résultats. 