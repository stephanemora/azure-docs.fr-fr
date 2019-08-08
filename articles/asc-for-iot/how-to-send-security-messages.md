---
title: Envoyer vos messages de sécurité à Azure Security Center pour IoT (préversion) | Microsoft Docs
description: Découvrez comment envoyer vos messages de sécurité avec Azure Security Center pour l’IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: c780eea15b9f064d3279c75ac2f967e8b6099ecb
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596205"
---
# <a name="send-security-messages-sdk"></a>Envoyer des messages de sécurité à l’aide du SDK

Ce guide pratique décrit les fonctionnalités du service Azure Security Center pour IoT vous permettant de collecter et d’envoyer vos messages de sécurité des appareils sans utiliser d’agent Azure Security Center pour IoT, et explique comment les utiliser.  

Dans ce guide, vous apprendrez comment : 
> [!div class="checklist"]
> * Utiliser l’API d’envoi de message de sécurité pour C#
> * Utiliser l’API d’envoi de message de sécurité pour C

## <a name="azure-security-center-for-iot-capabilities"></a>Fonctionnalités Azure Security Center pour IoT

Azure Security Center pour IoT peut traiter et analyser tout type de données de messages de sécurité, à condition que les données envoyées soient conformes au [schéma Azure Security Center pour IoT](https://aka.ms/iot-security-schemas) et qu’il s’agisse de messages de sécurité.

## <a name="security-message"></a>Message de sécurité

Azure Security Center pour IoT définit un message de sécurité selon les critères suivants :
- Le message a été envoyé avec le kit SDK C/C# Azure IoT.
- Le message est conforme au [schéma des messages de sécurité](https://aka.ms/iot-security-schemas).
- Le message a été défini comme message de sécurité avant l’envoi.

Chaque message de sécurité comporte les métadonnées de l’expéditeur, par exemple `AgentId`, `AgentVersion` et `MessageSchemaVersion`, et une liste d’événements de sécurité.
Le schéma définit les propriétés valides et requises du message de sécurité, et notamment les types d’événements.

>[!Note]
> Les messages envoyés non conformes au schéma sont ignorés. Veillez à vérifier le schéma avant de lancer l’envoi de données, car les messages ignorés ne sont pas stockés. 

>[!Note]
> Les messages envoyés sans avoir être définis comme messages de sécurité avec le kit SDK C/C# Azure IoT ne sont pas acheminés vers le pipeline Azure Security Center pour IoT.

## <a name="valid-message-example"></a>Exemple de message valide

L’exemple ci-dessous montre un objet message de sécurité valide. Il contient les métadonnées du message et un seul événement de sécurité `ProcessCreate`.

Une fois défini comme message de sécurité et envoyé, ce message sera traité par Azure Security Center pour IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Envoyer des messages de sécurité 

Envoyez des messages de sécurité sans utiliser l’agent Azure Security Center pour IoT à l’aide [d’Azure IoT C# device SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) ou [d’Azure IoT C device SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Pour envoyer les données à partir de vos appareils en vue de leur traitement par Azure Security Center pour IoT, utilisez l’une des API suivantes pour marquer les messages avec le bon routage vers le pipeline de traitement Azure Security Center pour IoT. 

Toutes les données envoyées, même si elles sont marquées avec l’en-tête approprié, doivent également respecter le [schéma de message d’Azure Security Center pour IoT](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>API d’envoi de message de sécurité

L’API d’**envoi de messages de sécurité** est disponible en C et C#.  

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="c-api"></a>API C

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
- Consultez la [Vue d’ensemble](overview.md) du service Microsoft Azure Security Center pour IoT
- En savoir plus sur [l’architecture](architecture.md) d’Azure Security Center pour IoT
- Activer le [service](quickstart-onboard-iot-hub.md)
- Consulter les [Questions fréquentes (FAQ)](resources-frequently-asked-questions.md)
- Découvrir comment accéder aux [données de sécurité brutes](how-to-security-data-access.md)
- Comprendre les [recommandations](concept-recommendations.md)
- Comprendre les [alertes](concept-security-alerts.md)
