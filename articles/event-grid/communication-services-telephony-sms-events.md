---
title: Azure Communication Services - Événements de téléphonie et SMS
description: Cet article explique comment utiliser Azure Communication Services comme source d’événement Event Grid pour les événements de téléphonie et SMS.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 2456df394a25df3b64f464a144224d9f9b365408
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134314"
---
# <a name="azure-communication-services---telephony-and-sms-events"></a>Azure Communication Services - Événements de téléphonie et SMS

Cet article décrit les propriétés et le schéma des événements de téléphonie et SMS.  Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

## <a name="events-types"></a>Types d’événements

Azure Communication Services émet les types d’événements de téléphonie et SMS suivants :

| Type d'événement                                                  | Description                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.SMSReceived                         | Publié lorsqu’un SMS est reçu par un numéro de téléphone associé au service de communication. |
| Microsoft.Communication.SMSDeliveryReportReceived           | Publié lorsqu’un rapport de remise est reçu pour un SMS envoyé par le service de communication.     |

## <a name="event-responses"></a>Réponses aux événements

Quand un événement est déclenché, le service Event Grid envoie les données relatives à cet événement aux points de terminaison d’abonnement.

Cette section contient un exemple de ce à quoi ces données ressembleraient pour chaque événement.

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Événement Microsoft.Communication.SMSDeliveryReportReceived

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "eventType": "Microsoft.Communication.SMSDeliveryReportReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:22:20Z"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Événement Microsoft.Communication.SMSReceived

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "topic": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "eventType": "Microsoft.Communication.SMSReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:27:47Z"
}]
```
