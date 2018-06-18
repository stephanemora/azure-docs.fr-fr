---
title: Schémas de suivi AS2 pour la surveillance B2B - Azure Logic Apps | Microsoft Docs
description: Utilisez des schémas de suivi AS2 pour surveiller les messages B2B des transactions de votre compte d’intégration Azure.
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48e39fd20716e962c4a3e367fdff18e0b4fba32d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300879"
---
# <a name="start-or-enable-tracking-of-as2-messages-and-mdns-to-monitor-success-errors-and-message-properties"></a>Démarrage ou activation du suivi des messages AS2 et des MDN pour surveiller la réussite, les erreurs et les propriétés de message
Vous pouvez utiliser ces schémas de suivi AS2 dans votre compte d’intégration d’Azure pour surveiller les transactions B2B :

* Schéma de suivi de message AS2
* Schéma de suivi de MDN AS2

## <a name="as2-message-tracking-schema"></a>Schéma de suivi de message AS2
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| Propriété | type | Description |
| --- | --- | --- |
| senderPartnerName | Chaîne | Nom de partenaire de l’expéditeur du message AS2. (facultatif) |
| receiverPartnerName | Chaîne | Nom de partenaire du destinataire du message AS2. (facultatif) |
| as2To | Chaîne | Nom du destinataire du message AS2 dans les en-têtes du message AS2. (obligatoire) |
| as2From | Chaîne | Nom de l’expéditeur du message AS2 dans les en-têtes du message AS2. (obligatoire) |
| agreementName | Chaîne | Nom du contrat AS2 dans lequel les messages sont résolus. (facultatif) |
| direction | Chaîne | Direction du flux de messages (envoi ou réception). (obligatoire) |
| messageId | Chaîne | ID du message AS2 dans les en-têtes du message AS2 (facultatif) |
| dispositionType |Chaîne | Valeur de type de disposition MDN (notification de réception du message). (facultatif) |
| fileName | Chaîne | Nom de fichier, de l’en-tête du message AS2. (facultatif) |
| isMessageFailed |Booléen | Indique si le message AS2 a échoué. (obligatoire) |
| isMessageSigned | Booléen | Indique si le message AS2 a été signé. (obligatoire) |
| isMessageEncrypted | Booléen | Indique si le message AS2 a été chiffré. (obligatoire) |
| isMessageCompressed |Booléen | Indique si le message AS2 a été compressé. (obligatoire) |
| correlationMessageId | Chaîne | ID de message AS2 permettant de mettre en corrélation les messages avec des MDN. (facultatif) |
| incomingHeaders |Dictionnaire de JToken | Détails de l’en-tête de message AS2 entrant. (facultatif) |
| outgoingHeaders |Dictionnaire de JToken | Détails de l’en-tête de message AS2 sortant. (facultatif) |
| isNrrEnabled | Booléen | Utiliser la valeur par défaut si cette valeur est inconnue. (obligatoire) |
| isMdnExpected | Boolean | Utiliser la valeur par défaut si cette valeur est inconnue. (obligatoire) |
| mdnType | Enum | Les valeurs autorisées sont **NotConfigured**, **Sync** ou **Async**. (obligatoire) |

## <a name="as2-mdn-tracking-schema"></a>Schéma de suivi de MDN AS2
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": {
                },
                "outgoingHeaders": {
                }
            }
    }
````

| Propriété | type | Description |
| --- | --- | --- |
| senderPartnerName | Chaîne | Nom de partenaire de l’expéditeur du message AS2. (facultatif) |
| receiverPartnerName | Chaîne | Nom de partenaire du destinataire du message AS2. (facultatif) |
| as2To | Chaîne | Nom du partenaire qui reçoit le message AS2. (obligatoire) |
| as2From | Chaîne | Nom du partenaire qui envoie le message AS2. (obligatoire) |
| agreementName | Chaîne | Nom du contrat AS2 dans lequel les messages sont résolus. (facultatif) |
| direction |Chaîne | Direction du flux de messages (envoi ou réception). (obligatoire) |
| messageId | Chaîne | ID du message AS2. (facultatif) |
| originalMessageId |Chaîne | ID du message AS2 d’origine. (facultatif) |
| dispositionType | Chaîne | Valeur du type de disposition MDN. (facultatif) |
| isMessageFailed |Booléen | Indique si le message AS2 a échoué. (obligatoire) |
| isMessageSigned |Booléen | Indique si le message AS2 a été signé. (obligatoire) |
| isNrrEnabled | Booléen | Utiliser la valeur par défaut si cette valeur est inconnue. (obligatoire) |
| statusCode | Enum | Les valeurs autorisées sont **Accepted**, **Rejected** et **AcceptedWithErrors**. (obligatoire) |
| micVerificationStatus | Enum | Les valeurs autorisées sont **NotApplicable**, **Succeeded** et **Failed**. (obligatoire) |
| correlationMessageId | Chaîne | ID de corrélation. ID du message d’origine (ID de message du message pour lequel MDN est configurée). (facultatif) |
| incomingHeaders | Dictionnaire de JToken | Indique les détails de l’en-tête de message entrant. (facultatif) |
| outgoingHeaders |Dictionnaire de JToken | Indique les détails de l’en-tête de message sortant. (facultatif) |

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).    
* En savoir plus sur le [suivi des messages B2B](logic-apps-monitor-b2b-message.md).   
* En savoir plus sur les [schémas de suivi personnalisé B2B](logic-apps-track-integration-account-custom-tracking-schema.md).   
* En savoir plus sur le [schéma de suivi X12](logic-apps-track-integration-account-x12-tracking-schema.md).   
* En savoir plus sur [le suivi des messages B2B dans Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
