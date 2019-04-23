---
title: Envoyer vos messages de sécurité à Azure Security Center pour la version préliminaire IoT | Microsoft Docs
description: Apprenez à envoyer vos messages de sécurité à l’aide d’Azure Security Center pour IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a9974fd15ae9c8c420992c3ae1084feebae0f57d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797344"
---
# <a name="send-security-messages-sdk"></a>Envoyer des messages de sécurité à l’aide du SDK

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide explique Azure Security Center (ASC) pour les fonctionnalités du service IoT lorsque vous choisissez collecter et envoyer des messages de sécurité de votre appareil sans utiliser un ASC pour l’agent IoT et explique comment faire.  

Dans ce guide, vous apprendrez comment : 
> [!div class="checklist"]
> * Utiliser l’API d’envoi de message de sécurité pour C#
> * Utiliser l’API d’envoi de message de sécurité pour C

## <a name="asc-for-iot-capabilities"></a>ASC pour les fonctionnalités de IoT

ASC pour IoT peut traiter et analyser n’importe quel type de données de message de sécurité, que les données envoyées est conforme à la [ASC pour IoT schéma](https://aka.ms/iot-security-schemas) et le message est défini comme un message de sécurité.

## <a name="security-message"></a>Message de sécurité

ASC pour IoT définit un message de sécurité à l’aide des critères suivants :
- Si le message a été envoyé avec Azure IoT C /C# SDK
- Si le message est conforme à la [schéma de message de sécurité](https://aka.ms/iot-security-schemas)
- Si le message a été défini comme un message de sécurité avant l’envoi

Chaque message de sécurité inclut les métadonnées de l’expéditeur comme `AgentId`, `AgentVersion`, `MessageSchemaVersion` et une liste d’événements de sécurité.
Le schéma définit les propriétés valides et requises du message de sécurité, y compris les types d’événements.

[!NOTE]
> Les messages envoyés non conformes au schéma sont ignorés. Veillez à vérifier le schéma avant de lancer l’envoi de données, car les messages ignorés ne sont pas stockés. 
> Les messages envoyés qui n’ont pas été définies comme un message de sécurité à l’aide de l’Azure IoT C /C# SDK n’est pas routé vers le ASC pour IoT pipeline

## <a name="valid-message-example"></a>Exemple de message valide

L’exemple ci-dessous montre un objet de message de sécurité valide. L’exemple contient les métadonnées de message et l’autre `ProcessCreate` des événements de sécurité.

Une fois définie comme un message de sécurité et envoyé, ce message sera traité par ASC pour IoT.

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
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Envoyer des messages de sécurité 

Envoyer des messages de sécurité sans utiliser l’ASC pour l’agent IoT, à l’aide de la [Azure IoT C# device SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) ou [appareil Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Pour envoyer les données à partir de vos appareils en vue de leur traitement par ASC pour IoT, utilisez une des API suivantes pour marquer les messages avec le bon routage vers le pipeline de traitement ASC pour IoT. Les messages envoyés de cette manière sont traités et affichés en tant qu’insights de sécurité dans ASC pour IoT au sein d’IoT Hub ou d’Azure Security Center. 

Toutes les données envoyées, même si elles sont marquées avec l’en-tête approprié, doivent également respecter le [schéma de message d’ASC pour IoT](https://aka.ms/iot-security-schemas). 

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
- Lire la [vue d’ensemble](overview.md) du service ASC pour IoT
- Découvrir plus en détail l’[architecture](architecture.md) ASC pour IoT
- Activer le [service](quickstart-onboard-iot-hub.md)
- Consulter les [Questions fréquentes (FAQ)](resources-frequently-asked-questions.md)
- Découvrir comment accéder aux [données de sécurité brutes](how-to-security-data-access.md)
- Comprendre les [recommandations](concept-recommendations.md)
- Comprendre les [alertes](concept-security-alerts.md)
