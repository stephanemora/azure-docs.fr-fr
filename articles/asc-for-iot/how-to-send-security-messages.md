---
title: Envoyer vos messages de sécurité à Azure Security Center pour la version préliminaire IoT | Microsoft Docs
description: Apprenez à envoyer vos messages de sécurité à l’aide d’Azure Security Center pour IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: be17c5bb4d09e0868af0c6fd9b31f7653b614735
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762767"
---
# <a name="send-security-messages-sdk"></a>Envoyer des messages de sécurité SDK

> [!IMPORTANT]
> Azure Security Center pour IoT est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide explique Azure Security Center (ASC) pour les fonctionnalités du service IoT lorsque vous choisissez collecter et envoyer des messages de sécurité de votre appareil sans utiliser un ASC pour l’agent IoT et explique comment faire.  

Dans ce guide, vous apprendrez comment : 
> [!div class="checklist"]
> * Utiliser le message de sécurité envoi API pourC#
> * Utiliser l’envoi de messages de sécurité API pour C

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
> Les messages envoyés qui ne sont pas conformes au schéma sont ignorés. Veillez à vérifier le schéma avant de lancer l’envoi de données comme les messages ignorés ne sont pas actuellement stockés. 
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

Pour envoyer les données de l’appareil à partir de vos appareils pour le traitement par ASC pour IoT, utilisez une des API suivantes pour marquer les messages pour le routage correct à ASC pour le pipeline de traitement de l’IoT. Les messages envoyés de cette manière seront traitées et affichés en tant que les informations de sécurité dans ASC pour IoT dans les deux IoT Hub ou dans Azure Security Center. 

Toutes les données qui sont envoyées, même si marquée avec l’en-tête approprié, doivent également respecter les [ASC pour schéma de message IoT](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>API de message de sécurité d’envoi

Le **envoyer des messages de sécurité** API est actuellement disponible en C et C#.  

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
- Lire l’ASC pour le service IoT [vue d’ensemble](overview.md)
- En savoir plus sur ASC pour IoT [Architecture](architecture.md)
- Activer la [service](quickstart-onboard-iot-hub.md)
- Lire le [Forum aux questions](resources-frequently-asked-questions.md)
- Découvrez comment accéder aux [les données de sécurité brute](how-to-security-data-access.md)
- Comprendre [recommandations](concept-recommendations.md)
- Comprendre [alertes](concept-security-alerts.md)
