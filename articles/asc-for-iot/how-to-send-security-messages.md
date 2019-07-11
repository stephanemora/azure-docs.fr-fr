---
title: Envoyer des messages de sécurité à Azure Security Center pour l’IoT (préversion) | Microsoft Docs
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
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 73335773695059b3c2afd121a0dd39ada8d28bb0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618087"
---
# <a name="send-security-messages-sdk"></a>Envoyer des messages de sécurité à l’aide du SDK

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide pratique décrit les fonctionnalités du service Azure Security Center (ASC) pour l’IoT permettant de collecter et d’envoyer les messages de sécurité des appareils sans utiliser d’agent ASC pour l’IoT, et explique comment les utiliser.  

Dans ce guide, vous apprendrez comment : 
> [!div class="checklist"]
> * Utiliser l’API d’envoi de message de sécurité pour C#
> * Utiliser l’API d’envoi de message de sécurité pour C

## <a name="asc-for-iot-capabilities"></a>Fonctionnalités d’ASC pour l’IoT

ASC pour l’IoT peut traiter et analyser tous types de messages de sécurité, à condition que les données envoyées soient conformes au [schéma d’ASC pour l’IoT](https://aka.ms/iot-security-schemas) et qu’il s’agisse de messages de sécurité.

## <a name="security-message"></a>Message de sécurité

ASC pour l’IoT définit un message de sécurité selon les critères suivants :
- Le message a été envoyé avec le kit SDK C/C# Azure IoT.
- Le message est conforme au [schéma des messages de sécurité](https://aka.ms/iot-security-schemas).
- Le message a été défini comme message de sécurité avant l’envoi.

Chaque message de sécurité comporte les métadonnées de l’expéditeur, par exemple `AgentId`, `AgentVersion` et `MessageSchemaVersion`, et une liste d’événements de sécurité.
Le schéma définit les propriétés valides et requises du message de sécurité, et notamment les types d’événements.

[!NOTE]
> Les messages envoyés non conformes au schéma sont ignorés. Veillez à vérifier le schéma avant de lancer l’envoi de données, car les messages ignorés ne sont pas stockés. 
> Les messages envoyés sans être définis comme messages de sécurité avec le kit SDK C/C# Azure IoT ne sont pas acheminés vers le pipeline ASC pour l’IoT.

## <a name="valid-message-example"></a>Exemple de message valide

L’exemple ci-dessous montre un objet message de sécurité valide. Il contient les métadonnées du message et un seul événement de sécurité `ProcessCreate`.

Une fois défini comme message de sécurité et envoyé, ce message sera traité par ASC pour l’IoT.

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

Envoyez des messages de sécurité sans utiliser l’agent ASC pour l’IoT, à l’aide [d’Azure IoT C# device SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) ou [d’Azure IoT C device SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

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
